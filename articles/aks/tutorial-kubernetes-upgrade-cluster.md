---
title: Руководство по Kubernetes в Azure. Обновление кластера
description: Руководство по Kubernetes в Azure. Обновление кластера
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: d66197b69a0804a49fabb72e9b97c77e000bdf88
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131649"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Руководство. Обновление Kubernetes в службе Azure Kubernetes (AKS)

Кластер службы Azure Kubernetes (AKS) можно обновить с помощью Azure CLI. При обновлении узлы Kubernetes тщательно [блокируются и очищаются][kubernetes-drain], чтобы минимизировать влияние на работающие приложения.

В этом руководстве (часть седьмая из семи) мы обновляем кластер Kubernetes. Здесь будут выполнены следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, этот образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes.

Если вы не выполнили эти действия и хотите продолжить работу, вернитесь к руководству по [созданию образов контейнеров (часть 1)][aks-tutorial-prepare-app].

## <a name="get-cluster-versions"></a>Получение версий кластера

Перед обновлением кластера выполните команду [az aks get-upgrades][], чтобы проверить выпуски Kubernetes, доступные для обновления.

```azurecli
az aks get-upgrades --name myAKSCluster --resource-group myResourceGroup --output table
```

В следующем примере текущая версия узла — *1.9.6*, а доступные для обновления версии показаны в столбце *Обновления*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------
default  myResourceGroup  1.9.6            1.9.6              1.10.3
```

## <a name="upgrade-cluster"></a>Обновление кластера

Для обновления узлов кластера используйте команду [az aks upgrade][]. В следующем примере кластер обновляется до версии *1.10.3*.

```azurecli
az aks upgrade --name myAKSCluster --resource-group myResourceGroup --kubernetes-version 1.10.3
```

Ниже приведен краткий пример выходных данных, где в качестве *kubernetesVersion* показана версия *1.10.3*:

```json
{
  "agentPoolProfiles": [
    {
      "count": 3,
      "maxPods": 110,
      "name": "nodepool1",
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS1_v2",
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": false,
  "fqdn": "myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io",
  "id": "/subscriptions/<Subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.10.3",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-upgrade"></a>Проверка обновления

Проверьте, выполнено ли обновление, введя команду [az aks show][].

```azurecli
az aks show --name myAKSCluster --resource-group myResourceGroup --output table
```

Выходные данные:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.3               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы обновили Kubernetes в кластере AKS. Были выполнены следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

Перейдите по ссылке для получения дополнительных сведений об AKS.

> [!div class="nextstepaction"]
> [Общие сведения о службе AKS][aks-intro]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-intro]: ./intro-kubernetes.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az aks show]: /cli/azure/aks#az-aks-show
[az aks get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az aks upgrade]: /cli/azure/aks#az-aks-upgrade