---
title: Использование дисков Azure с AKS.
description: Использование дисков Azure с AKS.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: aa9b92df84a48ef4cb706e9e89e0f6c0a25cd42a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420498"
---
# <a name="volumes-with-azure-disks"></a>Тома с дисками Azure

Контейнерные приложения часто требуются для обращения к данным и их хранения во внешнем томе данных. В качестве этого внешнего хранилища данных можно использовать диски Azure. Из этой статьи вы узнаете, как использовать диск Azure в качестве тома Kubernetes в кластере службы Azure Kubernetes.

Дополнительные сведения о томах Kubernetes см. в разделе [Volumes][kubernetes-volumes] (Тома).

## <a name="create-an-azure-disk"></a>Создание диска Azure

Прежде чем подключить управляемый диск Azure в качестве тома Kubernetes, убедитесь, что диск существует в группе ресурсов **узла** AKS. Получите имя группы ресурсов, выполнив команду [az resource show][az-resource-show].

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Чтобы создать диск Azure, используйте команду [az disk create][az-disk-create].

Обновите `--resource-group` именем группы ресурсов, которая была получена на предыдущем шаге, а `--name` — любым именем.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

Когда диск будет создан, вы увидите результат, аналогичный приведенному ниже. Это значение является идентификатором диска, который используется при подключении диска.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```
> [!NOTE]
> Счета для управляемых дисков Azure выставляются в зависимости от SKU для определенного размера. Эти номера SKU варьируются от 32 ГиБ для дисков S4 или P4 и до 4 ТиБ для дисков S50 или P50. Кроме того, число операций ввода-вывода в секунду и пропускная способность управляемого диска ценовой категории "Премиум" зависят как от номера SKU, так и от размера экземпляров узлов в кластере AKS. Дополнительные сведения см. на странице [Цены на управляемые диски][managed-disk-pricing-performance].

> [!NOTE]
> Если вам нужно создать диск в отдельной группе ресурсов, также добавьте субъект-службу Azure Kubernetes Service (AKS) для кластера в группу ресурсов, содержащую диск с ролью `Contributor`. 
>

## <a name="mount-disk-as-volume"></a>Подключите диска в качестве тома

Подключите диск Azure к pod, настроив том в спецификации контейнера.

Создайте файл `azure-disk-pod.yaml` со следующим содержимым. Замените `diskName` именем созданного диска, а `diskURI` — идентификатором диска. Кроме того, запишите `mountPath`. Это путь, по которому диск Azure подключен в pod.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

Чтобы создать pod, используйте команду kubectl.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Теперь у вас есть запущенная группа pod с подключенным диском Azure в папке `/mnt/azure`.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о томах Kubernetes, использующих диски Azure.

> [!div class="nextstepaction"]
> [Подключаемый модуль Kubernetes для дисков Azure][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/resource#az-resource-show
