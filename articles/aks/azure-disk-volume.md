---
title: Использование дисков Azure с AKS.
description: Использование дисков Azure с AKS.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4af4620ff7a17cae76c4d5f2cf1a30ce4a3dccd8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "34597073"
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

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
