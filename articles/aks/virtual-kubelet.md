---
title: Запуск Virtual Kubelet в кластере службы Azure Kubernetes
description: Использование Virtual Kubelet для запуска контейнеров Kubernetes в экземплярах контейнеров Azure.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/12/2018
ms.author: nepeters
ms.openlocfilehash: 0d046970b40f5253b07005ab578035c7c179e7df
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304015"
---
# <a name="virtual-kubelet-with-aks"></a>Использование Virtual Kubelet в службе контейнеров Azure

Экземпляры контейнеров Azure предоставляют размещенную среду для запуска контейнеров в Azure. При использовании экземпляров контейнеров Azure вам не требуется управлять базовой вычислительной инфраструктурой, это делает Azure. При запуске контейнеров в Экземплярах контейнеров Azure взимается плата за каждый второй запущенный контейнер.

При использовании поставщика Virtual Kubelet для Экземпляров контейнеров Azure вы можете назначить контейнеры Linux и Windows на экземпляр контейнера, как если бы это был стандартный узел Kubernetes. Эта конфигурация позволяет воспользоваться преимуществами Kubernetes и возможностью управления стоимостью и затратами экземпляров контейнеров.

> [!NOTE]
> Virtual Kubelet представляет собой экспериментальный открытый проект и должен использоваться таким образом. Чтобы внести свой вклад в проект, сообщить о проблемах и получить дополнительную информацию о Virtual Kubelet, зайдите на [страницу проекта Virtual Kubelet на GitHub][vk-github].

В этом документе приведены инструкции по настройке Virtual Kubelet для экземпляров контейнеров в службе Azure Kubernetes.

## <a name="prerequisite"></a>Предварительные требования

Для выполнения этих инструкций у вас должен быть кластер AKS. Если вам необходим кластер AKS, обратитесь к [руководству по началу работы со службой Azure Kubernetes][aks-quick-start].

Вам также потребуется Azure CLI **2.0.33** или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0](/cli/azure/install-azure-cli).

Для установки Virtual Kubelet также необходим [Helm](https://docs.helm.sh/using_helm/#installing-helm).

## <a name="installation"></a>Установка

Для установки Virtual Kubelet выполните команду [az aks install-connector][aks-install-connector]. В следующем примере разворачиваются соединители как для Linux, так и для Windows.

```azurecli-interactive
az aks install-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet --os-type Both
```

В команде `aks install-connector` могут использоваться следующие аргументы.

| Аргумент: | ОПИСАНИЕ | Обязательно |
|---|---|:---:|
| `--connector-name` | Имя соединителя ACI.| Yes |
| `--name` `-n` | Имя управляемого кластера. | Yes |
| `--resource-group` `-g` | Имя группы ресурсов. | Yes |
| `--os-type` | Тип операционной системы экземпляров контейнера. Допустимые значения: Both, Linux, Windows. Значение по умолчанию: Linux. | Нет  |
| `--aci-resource-group` | Группа ресурсов, в которой будут созданы группы контейнеров ACI. | Нет  |
| `--location` `-l` | Расположение для создания групп контейнеров ACI. | Нет  |
| `--service-principal` | Субъект-служба, используемый для проверки подлинности в API-интерфейсах Azure. | Нет  |
| `--client-secret` | Секрет, связанный с субъектом-службой. | Нет  |
| `--chart-url` | URL-адрес диаграммы Helm для установки соединителя ACI. | Нет  |
| `--image-tag` | Тег образа контейнера Virtual Kubelet. | Нет  |

## <a name="validate-virtual-kubelet"></a>Проверка Virtual Kubelet

Чтобы проверить установку Virtual Kubelet, получите список узлов Kubernetes, выполнив команду [kubectl get nodes][kubectl-get].

```console
$ kubectl get nodes

NAME                                    STATUS    ROLES     AGE       VERSION
aks-nodepool1-23443254-0                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-1                Ready     agent     16d       v1.9.6
aks-nodepool1-23443254-2                Ready     agent     16d       v1.9.6
virtual-kubelet-virtual-kubelet-linux   Ready     agent     4m        v1.8.3
virtual-kubelet-virtual-kubelet-win     Ready     agent     4m        v1.8.3
```

## <a name="run-linux-container"></a>Запуск контейнера Linux

Создайте файл `virtual-kubelet-linux.yaml` и скопируйте в него следующий код YAML. Замените значение `kubernetes.io/hostname` на имя узла Virtual Kubelet Linux. Обратите внимание, что [nodeSelector][node-selector] и [toleration][toleration] используются для назначения контейнера на узел.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Запустите приложение, выполнив команду [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f virtual-kubelet-linux.yaml
```

Чтобы вывести список pod для назначенного узла, выполните команду [kubectl get pods][kubectl-get] с аргументом `-o wide`. Обратите внимание, что pod `aci-helloworld` был назначен на узел `virtual-kubelet-virtual-kubelet-linux`.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
aci-helloworld-2559879000-8vmjw     1/1       Running   0          39s       52.179.3.180   virtual-kubelet-virtual-kubelet-linux
```

## <a name="run-windows-container"></a>Запуск контейнера Windows

Создайте файл `virtual-kubelet-windows.yaml` и скопируйте в него следующий код YAML. Замените значение `kubernetes.io/hostname` на имя узла Virtual Kubelet Windows. Обратите внимание, что [nodeSelector][node-selector] и [toleration][toleration] используются для назначения контейнера на узел.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: nanoserver/iis
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-win
      tolerations:
      - key: azure.com/aci
        effect: NoSchedule
```

Запустите приложение, выполнив команду [kubectl create][kubectl-create].

```azurecli-interactive
kubectl create -f virtual-kubelet-windows.yaml
```

Чтобы вывести список pod для назначенного узла, выполните команду [kubectl get pods][kubectl-get] с аргументом `-o wide`. Обратите внимание, что pod `nanoserver-iis` был назначен на узел `virtual-kubelet-virtual-kubelet-win`.

```console
$ kubectl get pods -o wide

NAME                                READY     STATUS    RESTARTS   AGE       IP             NODE
nanoserver-iis-868bc8d489-tq4st     1/1       Running   8         21m       138.91.121.91   virtual-kubelet-virtual-kubelet-win
```

## <a name="remove-virtual-kubelet"></a>Удаление Virtual Kubelet

Для удаления Virtual Kubelet выполните команду [az aks remove-connector][aks-remove-connector]. Замените значения аргументов на имя соединителя, кластера AKS и группы ресурсов кластера AKS.

```azurecli-interactive
az aks remove-connector --resource-group myAKSCluster --name myAKSCluster --connector-name virtual-kubelet
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с дополнительной информацией о Virtual Kubelet на [странице проекта Virtual Kubelet в Github][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az_aks_list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create
[kubectl-get]: https://kubernetes.io/docs/user-guide/kubectl/v1.8/#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
