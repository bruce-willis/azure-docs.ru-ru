---
title: Руководство по Kubernetes в Azure. Обновление кластера
description: В этом руководстве по Службе Azure Kubernetes (AKS) вы узнаете, как обновить имеющийся кластер AKS до последней доступной версии Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 11e082ae235706613b0a60b12bc2b27896953508
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "41917751"
---
# <a name="tutorial-upgrade-kubernetes-in-azure-kubernetes-service-aks"></a>Руководство. Обновление Kubernetes в службе Azure Kubernetes (AKS)

В ходе жизненного цикла приложения и кластера вам может потребоваться выполнить обновления до последней доступной версии Kubernetes и использовать новые функции. Кластер службы Azure Kubernetes (AKS) можно обновить с помощью Azure CLI. При обновлении узлы Kubernetes тщательно [блокируются и очищаются][kubernetes-drain], чтобы минимизировать влияние на работающие приложения.

В этом руководстве (часть седьмая из семи) мы обновляем кластер Kubernetes. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Определение текущей и доступной версии Kubernetes.
> * Обновление узлов Kubernetes.
> * Проверка успешного обновления.

## <a name="before-you-begin"></a>Перед началом работы

В предыдущих руководствах приложение было упаковано в образ контейнера, этот образ был передан в реестр контейнеров Azure и был создан кластер Kubernetes. Затем приложение было запущено в кластере Kubernetes. Если вы не выполнили эти действия и хотите продолжить работу, вернитесь к руководству по [созданию образов контейнеров (часть 1)][aks-tutorial-prepare-app].

Для выполнения задач из этого руководства требуется Azure CLI 2.0.44 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="get-available-cluster-versions"></a>Получение доступных версий кластера

Перед обновлением кластера выполните команду [az aks get-upgrades][], чтобы проверить, какие выпуски Kubernetes доступные для этого.

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере текущей является версия *1.9.6*, а доступные для обновления представлены в колонке *Обновления*.

```
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  ----------------------
default  myResourceGroup  1.9.9            1.9.9              1.10.3, 1.10.5, 1.10.6
```

## <a name="upgrade-a-cluster"></a>Обновление кластера

Для обновления кластера AKS используйте команду [az aks upgrade][]. В следующем примере кластер обновляется до версии Kubernetes *1.10.6*.

> [!NOTE]
> Одновременно можно выполнить только одно обновление дополнительного номера версии. Например, можно обновить версию *1.9.6* до *1.10.3*, но невозможно напрямую обновить версию *1.9.6* до *1.11.x*. Чтобы обновить версию *1.9.6* до *1.11.x*, сначала обновите версию *1.9.6* до *1.10.3*, а затем выполните другое обновление с версии *1.10.3* до *1.11.x*.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.10.6
```

Ниже приведен краткий пример выходных данных, где в качестве *kubernetesVersion* показана версия *1.10.6*:

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
  "kubernetesVersion": "1.10.6",
  "location": "eastus",
  "name": "myAKSCluster",
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="validate-an-upgrade"></a>Проверка обновления

Убедитесь, что обновление успешно выполнено, используя команду [az aks show][] следующим образом:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере выходных данных показано, что кластер AKS выполняется на основе версии *KubernetesVersion 1.10.6*:

```
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.10.6               Succeeded            myaksclust-myresourcegroup-19da35-bd54a4be.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы обновили Kubernetes в кластере AKS. Вы научились выполнять следующие задачи:

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
[azure-cli-install]: /cli/azure/install-azure-cli