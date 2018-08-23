---
title: Kubernetes в Azure — автомасштабирование кластера
description: Узнайте, как использовать инструмент автомасштабирования кластера со службой Azure Kubernetes (AKS) для автоматического масштабирования кластера в соответствии с потребностями.
services: container-service
author: sakthivetrivel
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/19/18
ms.author: sakthivetrivel
ms.custom: mvc
ms.openlocfilehash: 06b93273ea096f2d24df4411a64ce99d054892cf
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42142940"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Автомасштабирование кластера с помощью службы Azure Kubernetes (AKS) — предварительная версия

Служба Azure Kubernetes (AKS) предоставляет гибкое решение для развертывания управляемого кластера Kubernetes в Azure. По мере роста потребления ресурсов автомасштабирование кластера позволяет кластеру увеличиваться, удовлетворяя этому спросу с учетом заданных ограничений. Служба автомасштабирования кластера реализует масштабирование узлов агента на основе ожидающих модулей pod. Эта служба периодически проверяет ожидающие модули pod или пустые узлы кластера и увеличивает размер кластера, если это возможно. По умолчанию служба автомасштабирования кластера проверяет наличие ожидающих модулей каждые 10 секунд и удаляет узел в том случае, если он был не нужен в течение более 10 минут. При использовании с [горизонтальным автомасштабированием модулей pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) эта операция масштабирования будет обновлять реплики pod и ресурсы по требованию. Если узлов не хватает или ненужные узлы использованы при масштабировании модулей pod, служба автомасштабирования кластера сообщит об этом и запланирует модули pod в новом наборе узлов.

В этой статье описывается развертывание автомасштабирования кластера на узлах агента. Однако, так как автомасштабирование кластера развертывается в пространстве имен системы Kube, автомасштабирование не будет уменьшать масштаб узла, на котором работает модуль pod.

> [!IMPORTANT]
> Интеграция автомасштабирования кластера Azure Kubernetes (AKS) в данный момент находится в стадии **предварительной версии**. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>

## <a name="prerequisites"></a>Предварительные требования

В этом документе предполагается, что у вас есть кластер AKS с поддержкой RBAC. Если вам необходим кластер AKS, обратитесь к [руководству по началу работы со службой Azure Kubernetes][aks-quick-start].

 Чтобы использовать автомасштабирование кластера, в кластере необходимо использовать Kubernetes версии 1.10.X или более поздней версии и в нем должен быть включен RBAC. Дополнительные сведения об обновлении кластера см. в статье об [обновлении кластера AKS][aks-upgrade].

## <a name="gather-information"></a>Сбор сведений

Чтобы создать разрешения для выполнения автомасштабирования в кластере, запустите следующий скрипт bash:

```sh
#! /bin/bash
ID=`az account show --query id -o json`
SUBSCRIPTION_ID=`echo $ID | tr -d '"' `

TENANT=`az account show --query tenantId -o json`
TENANT_ID=`echo $TENANT | tr -d '"' | base64`

read -p "What's your cluster name? " cluster_name
read -p "Resource group name? " resource_group

CLUSTER_NAME=`echo $cluster_name | base64`
RESOURCE_GROUP=`echo $resource_group | base64`

PERMISSIONS=`az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$SUBSCRIPTION_ID" -o json`
CLIENT_ID=`echo $PERMISSIONS | sed -e 's/^.*"appId"[ ]*:[ ]*"//' -e 's/".*//' | base64`
CLIENT_SECRET=`echo $PERMISSIONS | sed -e 's/^.*"password"[ ]*:[ ]*"//' -e 's/".*//' | base64`

SUBSCRIPTION_ID=`echo $ID | tr -d '"' | base64 `

NODE_RESOURCE_GROUP=`az aks show --name $cluster_name  --resource-group $resource_group -o tsv --query 'nodeResourceGroup' | base64`

echo "---
apiVersion: v1
kind: Secret
metadata:
    name: cluster-autoscaler-azure
    namespace: kube-system
data:
    ClientID: $CLIENT_ID
    ClientSecret: $CLIENT_SECRET
    ResourceGroup: $RESOURCE_GROUP
    SubscriptionID: $SUBSCRIPTION_ID
    TenantID: $TENANT_ID
    VMType: QUtTCg==
    ClusterName: $CLUSTER_NAME
    NodeResourceGroup: $NODE_RESOURCE_GROUP
---"
```

После выполнения шагов в скрипте ваши данные будут выведены в виде секрета. Это может выглядеть следующим образом:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>$
  ResourceGroup: <base64-encoded-resource-group>  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

Затем получите имя пула узлов, выполнив следующую команду. 

```console
$ kubectl get nodes --show-labels
```

Выходные данные:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Затем извлеките значение метки **agentpool**. По умолчанию имя пула узлов кластера — nodepool1.

Теперь с помощью секрета и пула узлов вы можете создать схему развертывания.

## <a name="create-a-deployment-chart"></a>Создание схемы развертывания

Создайте файл с именем `aks-cluster-autoscaler.yaml` и скопируйте в него представленный ниже код YAML.

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["events","endpoints"]
  verbs: ["create", "patch"]
- apiGroups: [""]
  resources: ["pods/eviction"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["pods/status"]
  verbs: ["update"]
- apiGroups: [""]
  resources: ["endpoints"]
  resourceNames: ["cluster-autoscaler"]
  verbs: ["get","update"]
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["watch","list","get","update"]
- apiGroups: [""]
  resources: ["pods","services","replicationcontrollers","persistentvolumeclaims","persistentvolumes"]
  verbs: ["watch","list","get"]
- apiGroups: ["extensions"]
  resources: ["replicasets","daemonsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["policy"]
  resources: ["poddisruptionbudgets"]
  verbs: ["watch","list"]
- apiGroups: ["apps"]
  resources: ["statefulsets"]
  verbs: ["watch","list","get"]
- apiGroups: ["storage.k8s.io"]
  resources: ["storageclasses"]
  verbs: ["get", "list", "watch"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: Role
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
rules:
- apiGroups: [""]
  resources: ["configmaps"]
  verbs: ["create"]
- apiGroups: [""]
  resources: ["configmaps"]
  resourceNames: ["cluster-autoscaler-status"]
  verbs: ["delete","get","update"]

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: cluster-autoscaler
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: RoleBinding
metadata:
  name: cluster-autoscaler
  namespace: kube-system
  labels:
    k8s-addon: cluster-autoscaler.addons.k8s.io
    k8s-app: cluster-autoscaler
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: cluster-autoscaler
subjects:
  - kind: ServiceAccount
    name: cluster-autoscaler
    namespace: kube-system

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  labels:
    app: cluster-autoscaler
  name: cluster-autoscaler
  namespace: kube-system
spec:
  replicas: 1
  selector:
    matchLabels:
      app: cluster-autoscaler
  template:
    metadata:
      labels:
        app: cluster-autoscaler
    spec:
      serviceAccountName: cluster-autoscaler
      containers:
      - image: k8s.gcr.io/cluster-autoscaler:{{ ca_version }}
        imagePullPolicy: Always
        name: cluster-autoscaler
        resources:
          limits:
            cpu: 100m
            memory: 300Mi
          requests:
            cpu: 100m
            memory: 300Mi
        command:
        - ./cluster-autoscaler
        - --v=3
        - --logtostderr=true
        - --cloud-provider=azure
        - --skip-nodes-with-local-storage=false
        - --nodes=1:10:nodepool1
        env:
        - name: ARM_SUBSCRIPTION_ID
          valueFrom:
            secretKeyRef:
              key: SubscriptionID
              name: cluster-autoscaler-azure
        - name: ARM_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: ResourceGroup
              name: cluster-autoscaler-azure
        - name: ARM_TENANT_ID
          valueFrom:
            secretKeyRef:
              key: TenantID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_ID
          valueFrom:
            secretKeyRef:
              key: ClientID
              name: cluster-autoscaler-azure
        - name: ARM_CLIENT_SECRET
          valueFrom:
            secretKeyRef:
              key: ClientSecret
              name: cluster-autoscaler-azure
        - name: ARM_VM_TYPE
          valueFrom:
            secretKeyRef:
              key: VMType
              name: cluster-autoscaler-azure
        - name: AZURE_CLUSTER_NAME
          valueFrom:
            secretKeyRef:
              key: ClusterName
              name: cluster-autoscaler-azure
        - name: AZURE_NODE_RESOURCE_GROUP
          valueFrom:
            secretKeyRef:
              key: NodeResourceGroup
              name: cluster-autoscaler-azure
      restartPolicy: Always
```

Скопируйте и вставьте созданный на предыдущем шаге секрет в начале файла.

Затем, чтобы задать диапазон узлов, укажите аргумент для параметра `--nodes` в разделе `command` в формате МИН_ЗНАЧЕНИЕ:МАКС_ЗНАЧЕНИЕ:ИМЯ_ПУЛА_УЗЛОВ. Например: `--nodes=3:10:nodepool1` задает минимальное количество узлов 3, максимальное количество узлов 10 и имя пула узлов nodepool1.

Затем в поле образа в разделе **containers** укажите нужную версию службы автомасштабирования кластера. Для AKS требуется версия 1.2.2 или более новая. В этом примере используется версия 1.2.2.

## <a name="deployment"></a>Развертывание

Запустите службу автомасштабирования кластера, выполнив команду

```console
kubectl create -f aks-cluster-autoscaler.yaml
```

Чтобы проверить, работает ли автомасштабирование кластера, выполните следующую команду и просмотрите список модулей pod. В нем должен присутствовать модуль pod, заблокированный работающей службой cluster-autoscaler. Если он есть, значит автомасштабирование кластера развернуто.

```console
kubectl -n kube-system get pods
```

Чтобы просмотреть состояние автомасштабирования кластера, выполните команду

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

## <a name="interpreting-the-cluster-autoscaler-status"></a>Интерпретация состояния автомасштабирования кластера

```console
$ kubectl -n kube-system describe configmap cluster-autoscaler-status
Name:         cluster-autoscaler-status
Namespace:    kube-system
Labels:       <none>
Annotations:  cluster-autoscaler.kubernetes.io/last-updated=2018-07-25 22:59:22.661669494 +0000 UTC

Data
====
status:
----
Cluster-autoscaler status at 2018-07-25 22:59:22.661669494 +0000 UTC:
Cluster-wide:
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 registered=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC

NodeGroups:
  Name:        nodepool1
  Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC
  ScaleDown:   NoCandidates (candidates=0)
               LastProbeTime:      2018-07-25 22:59:22.067828801 +0000 UTC
               LastTransitionTime: 2018-07-25 00:38:36.41372897 +0000 UTC


Events:  <none>
```

Состояние автомасштабирования кластера можно просмотреть на двух разных уровнях: на уровне кластера и каждой группы узлов. Так как в настоящее время AKS поддерживает только один пул узлов, эти метрики одинаковы.

* Указанное состояние работоспособности относится ко всем узлам. Если автомасштабирование кластера пытается создать или удалить узлы в кластере, состояние изменится на Unhealthy (Неработоспособный). Вот описание состояния разных узлов:
    * Ready означает, что узел готов к планированию модулей.
    * Unready означает, что после запуска узла произошел сбой.
    * NotStarted означает, что узел еще не полностью запущен.
    * LongNotStarted означает, что узел не удалось запустить в разумных пределах.
    * Registered означает, что узел зарегистрирован в группе.
    * Unregistered означает, что узел присутствует на стороне поставщика кластера, но не зарегистрирован в Kubernetes.
  
* Параметр ScaleUp позволяет проверить, когда кластер определяет, что необходимо увеличить его масштаб.
    * Переход — это изменение числа узлов в кластере или состояния узла.
    * Количество готовых узлов — это количество доступных и готовых узлов в кластере. 
    * CloudProviderTarget — это количество узлов, рабочую нагрузку которых по определению автомасштабирования кластера необходимо обработать.

* Параметр ScaleDown позволяет проверить наличие кандидатов для уменьшения масштаба. 
    * Кандидат для уменьшения масштаба — это узел, удаление которого по определению автомасштабирования кластера не повлияет на способность кластера обрабатывать рабочую нагрузку. 
    * Приведенные значения времени показывают последнюю проверку кластера на наличие кандидатов для уменьшения масштаба и последний переход.

Наконец, в разделе "Events" отображаются любые события увеличения или уменьшения масштаба (неудавшиеся или успешные), а также время выполнения автомасштабирования кластера.

## <a name="next-steps"></a>Дополнительная информация

Узнать больше об использовании автомасштабирования кластера совместно с горизонтальным автомасштабированием модулей pod можно в статье [Масштабирование приложения и инфраструктуры Kubernetes][aks-tutorial-scale].

Узнайте больше о развертывании AKS и управлении ею из руководств по AKS.

> [!div class="nextstepaction"]
> [Руководство по AKS][aks-tutorial-prepare-app]

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md

<!-- LINKS - external -->
[cluster-autoscale]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md
