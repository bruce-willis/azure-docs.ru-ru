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
ms.openlocfilehash: 4f8df8e7004ca3cee832b6230dc153b21e2a6c18
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186719"
---
# <a name="cluster-autoscaler-on-azure-kubernetes-service-aks---preview"></a>Автомасштабирование кластера с помощью службы Azure Kubernetes (AKS) — предварительная версия

Служба Azure Kubernetes (AKS) предоставляет гибкое решение для развертывания управляемого кластера Kubernetes в Azure. По мере роста потребления ресурсов автомасштабирование кластера позволяет кластеру увеличиваться, удовлетворяя этому спросу с учетом заданных ограничений. Служба автомасштабирования кластера реализует масштабирование узлов агента на основе ожидающих модулей pod. Эта служба периодически проверяет ожидающие модули pod или пустые узлы кластера и увеличивает размер кластера, если это возможно. По умолчанию служба автомасштабирования кластера проверяет наличие ожидающих модулей каждые 10 секунд и удаляет узел в том случае, если он был не нужен в течение более 10 минут. При использовании с горизонтальным автомасштабирование модулей pod основного будет обновлять реплики pod и ресурсы по требованию. Если узлов не хватает или ненужные узлы использованы при масштабировании модулей pod, служба автомасштабирования кластера сообщит об этом и запланирует модули pod в новом наборе узлов.

> [!IMPORTANT]
> Интеграция автомасштабирования кластера Azure Kubernetes (AKS) в данный момент находится в стадии **предварительной версии**. Предварительные версии предоставляются при условии, что вы принимаете [дополнительные условия использования](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.
>

## <a name="prerequisites"></a>Предварительные требования

В этом документе предполагается, что у вас есть кластер AKS с поддержкой RBAC. Если вам необходим кластер AKS, обратитесь к [руководству по началу работы со службой Azure Kubernetes][aks-quick-start].

 Чтобы использовать автомасштабирование кластера, в кластере необходимо использовать Kubernetes версии 1.10.X или более поздней версии и в нем должен быть включен RBAC. Дополнительные сведения об обновлении кластера см. в статье об [обновлении кластера AKS][aks-upgrade].

## <a name="gather-information"></a>Сбор сведений

Ниже перечислены все сведения, которые нужно указать при настройке автомасштабирования.

- *Идентификатор подписки*. Идентификатор, соответствующий подписке, которая используется для этого кластера
- *Имя группы ресурсов*. Имя группы ресурсов, которой принадлежит кластер 
- *Имя кластера*. Имя, присвоенное кластеру
- *Идентификатор клиента*. Идентификатор приложения, предоставленный на этапе создания разрешения
- *Секрет клиента*. Секрет приложения, предоставленный на этапе создания разрешения
- *Идентификатор клиента*. Идентификатор владельца учетной записи
- *Группа ресурсов узла*. Имя группы ресурсов, которая содержит узлы агента в кластере
- *Имя пула узлов*. Имя масштабируемого пула узлов
- *Минимальное количество узлов*. Минимальное количество узлов в кластере
- *Максимальное количество узлов*. Максимальное количество узлов в кластере
- *Тип виртуальной машины*. Служба, используемая для создания кластера Kubernetes

Получите идентификатор подписки с помощью команды: 

``` azurecli
az account show --query id
```

Создайте набор учетных данных Azure, выполнив следующую команду:

```console
$ az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscription-id>" --output json

"appId": <app-id>,
"displayName": <display-name>,
"name": <name>,
"password": <app-password>,
"tenant": <tenant-id>
```

Идентификатор приложения, пароль и идентификатор владельца будут использоваться как clientID, clientSecret и tenantID на следующих этапах.

Получите имя пула узлов, выполнив следующую команду. 

```console
$ kubectl get nodes --show-labels
```

Выходные данные:

```console
NAME                       STATUS    ROLES     AGE       VERSION   LABELS
aks-nodepool1-37756013-0   Ready     agent     1h        v1.10.3   agentpool=nodepool1,beta.kubernetes.io/arch=amd64,beta.kubernetes.io/instance-type=Standard_DS1_v2,beta.kubernetes.io/os=linux,failure-domain.beta.kubernetes.io/region=eastus,failure-domain.beta.kubernetes.io/zone=0,kubernetes.azure.com/cluster=MC_[resource-group]\_[cluster-name]_[location],kubernetes.io/hostname=aks-nodepool1-37756013-0,kubernetes.io/role=agent,storageprofile=managed,storagetier=Premium_LRS
 ```

Затем извлеките значение метки **agentpool**. По умолчанию имя пула узлов кластера — nodepool1.

Чтобы получить имя группы ресурсов узла, извлеките значение метки **kubernetes.azure.com<span></span>/cluster**. Имя группы ресурсов узла имеет вид MC_[группа_ресурсов]\_[имя_кластера]_[расположение].

Параметр vmType определяет используемую службу — в этом примере AKS.

Теперь у вас есть следующие данные:

- SubscriptionID
- ResourceGroup
- ClusterName
- ClientID
- ClientSecret
- идентификатор клиента;
- NodeResourceGroup
- VMType

Далее нужно закодировать эти значения, используя метод base64. Например, чтобы закодировать значение VMType, используя метод base64, выполните такую команду:

```console
$ echo AKS | base64
QUtTCg==
```

## <a name="create-secret"></a>Создание секрета
Используя эти данные, создайте секрет для развертывания с помощью значений, полученных в предыдущих шагах, в таком формате:

```yaml
---
apiVersion: v1
kind: Secret
metadata:
  name: cluster-autoscaler-azure
  namespace: kube-system
data:
  ClientID: <base64-encoded-client-id>
  ClientSecret: <base64-encoded-client-secret>
  ResourceGroup: <base64-encoded-resource-group>
  SubscriptionID: <base64-encode-subscription-id>
  TenantID: <base64-encoded-tenant-id>
  VMType: QUtTCg==
  ClusterName: <base64-encoded-clustername>
  NodeResourceGroup: <base64-encoded-node-resource-group>
---
```

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
kubectl create -f cluster-autoscaler-containerservice.yaml
```

Чтобы проверить, работает ли автомасштабирование кластера, выполните следующую команду и просмотрите список модулей pod. Если есть модуль pod, заблокированный работающей службой cluster-autoscaler, значит автомасштабирование кластера развернуто.

```console
kubectl -n kube-system get pods
```

Чтобы просмотреть состояние автомасштабирования кластера, выполните команду

```console
kubectl -n kube-system describe configmap cluster-autoscaler-status
```

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
