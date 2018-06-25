---
title: Использование дисков Azure с AKS
description: Использование дисков Azure с AKS.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/06/2018
ms.author: nepeters
ms.openlocfilehash: 3841222d08b23f43f69e77fa43c469793b70ce63
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801387"
---
# <a name="persistent-volumes-with-azure-disks"></a>Постоянные тома с дисками Azure

Постоянный том — это часть хранилища, которая подготовлена к использованию для модулей pod Kubernetes. Постоянный том может использоваться одним или несколькими модулями pod и может быть подготовлен динамически или статически. Дополнительные сведения о постоянных томах Kubernetes см. в разделе [Kubernetes persistent volumes][kubernetes-volumes] (Постоянные тома Kubernetes).

В этом документе описываются использование постоянных томов с дисками Azure в кластере службы Azure Kubernetes.

> [!NOTE]
> Диск Azure можно подключить только с в режиме доступа ReadWriteOnce, который предоставляет доступ только к одному узлу AKS. Если вам нужно использовать постоянный том на нескольких узлах, попробуйте использовать [службу файлов Azure][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Встроенные классы хранения

Класс хранения используется для определения того, как единица хранения создается динамически с помощью постоянного тома. Дополнительные сведения о классах хранения Kubernetes см. в разделе [Kubernetes Storage Classes][kubernetes-storage-classes] (Классы хранения Kubernetes).

Каждый кластер AKS включает два предварительно созданных класса хранения, настроенных для работы с дисками Azure. Класс хранения `default` подготавливает диск Azure уровня "Стандартный". Класс хранения `managed-premium` подготавливает диск Azure уровня "Премиум". Если узлы AKS в кластере используют хранилище уровня "Премиум", выберите класс `managed-premium`.

Используйте команду [kubectl get sc][kubectl-get], чтобы просмотреть предварительно созданные классы хранения.

```console
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Утверждения постоянных томов указаны в ГиБ, но счета за использование управляемых дисков Azure выставляются по номеру SKU для определенного размера. Эти номера SKU варьируются от 32 ГиБ для дисков S4 или P4 и до 4 ТиБ для дисков S50 или P50. Кроме того, число операций ввода-вывода в секунду и пропускная способность управляемого диска уровня "Премиум" зависят как от номера SKU, так и от размера экземпляров узлов в кластере AKS. Дополнительные сведения см. на странице [Цены на управляемые диски][managed-disk-pricing-performance].

## <a name="create-persistent-volume-claim"></a>Создание утверждения постоянного тома

Утверждение постоянного тома (PVC) используется для автоматической подготовки хранилища на основе класса хранения. В этом случае утверждение постоянного тома может использовать один из предварительно созданных классов хранения для создания управляемого диска Azure уровня "Стандартный" или "Премиум".

Создайте файл `azure-premium.yaml` и скопируйте в него следующий манифест.

Обратите внимание на то, что класс хранения `managed-premium` указан в заметке и утверждение запрашивает диск емкостью `5GB` с правами доступа `ReadWriteOnce`.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
  annotations:
    volume.beta.kubernetes.io/storage-class: managed-premium
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

Создайте утверждение постоянного тома с помощью команды [kubectl apply][kubectl-apply].

```azurecli-interactive
kubectl apply -f azure-premium.yaml
```

## <a name="using-the-persistent-volume"></a>Использование постоянного тома

После создания утверждения постоянного тома и успешной подготовки диска можно создать группу pod с доступом к диску. Приведенный ниже манифест создает группу pod, использующую утверждение постоянного тома `azure-managed-disk` для подключения диска Azure по пути `/mnt/azure`.

Создайте файл `azure-pvc-disk.yaml` и скопируйте в него следующий манифест.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

Выполните команду [kubectl apply][kubectl-apply], чтобы создать pod.

```azurecli-interactive
kubectl apply -f azure-pvc-disk.yaml
```

Теперь у вас есть работающий pod с диском Azure, подключенным к каталогу `/mnt/azure`. Эта конфигурация может отображаться при проверке вашего модуля через `kubectl describe pod mypod`.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о постоянных томах Kubernetes, использующих диски Azure.

> [!div class="nextstepaction"]
> [Storage Classes][azure-disk-volume] (Классы хранения)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md 
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
