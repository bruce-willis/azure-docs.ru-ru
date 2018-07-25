---
title: Создание постоянных томов в службе Azure Kubernetes
description: Узнайте, как с помощью дисков Azure создавать постоянные тома для модулей pod в службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/10/2018
ms.author: iainfou
ms.openlocfilehash: 14617b57f59c068aa015c9bfea9b4d18520b4152
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38473688"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Создание постоянных томов в службе Azure Kubernetes (AKS) с помощью дисков Azure

Постоянный том — это часть хранилища, которая подготовлена к использованию для модулей pod Kubernetes. Постоянный том может использоваться одним или несколькими модулями pod и может быть подготовлен динамически или статически. Дополнительные сведения о постоянных томах Kubernetes см. в разделе [Kubernetes persistent volumes][kubernetes-volumes] (Постоянные тома Kubernetes). В этой статье описывается использование постоянных томов с дисками Azure в кластере службы Azure Kubernetes (AKS).

> [!NOTE]
> Диск Azure можно подключить только в *режиме доступа* *ReadWriteOnce*, то есть он будет доступен только для одного узла AKS. Если вам нужно использовать постоянный том на нескольких узлах, попробуйте использовать [службу файлов Azure][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Встроенные классы хранения

Класс хранения используется для определения того, как единица хранения создается динамически с помощью постоянного тома. Дополнительные сведения о классах хранения Kubernetes см. в разделе [Kubernetes Storage Classes][kubernetes-storage-classes] (Классы хранения Kubernetes).

Каждый кластер AKS включает два предварительно созданных класса хранения, настроенных для работы с дисками Azure. Класс хранения *default* подготавливает диск Azure ценовой категории "Стандартный". Класс хранения *managed-premium* подготавливает диск Azure ценовой категории "Премиум". Если узлы AKS в кластере используют хранилище класса Premium, выберите класс *managed-premium*.

Используйте команду [kubectl get sc][kubectl-get], чтобы просмотреть предварительно созданные классы хранения. В следующем примере демонстрируются предварительно созданные классы хранения, доступные для кластера AKS.

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Утверждения постоянных томов указаны в ГиБ, но счета за использование управляемых дисков Azure выставляются по номеру SKU для определенного размера. Эти номера SKU варьируются от 32 ГиБ для дисков S4 или P4 и до 4 ТиБ для дисков S50 или P50. Кроме того, число операций ввода-вывода в секунду и пропускная способность управляемого диска ценовой категории "Премиум" зависят как от номера SKU, так и от размера экземпляра для конкретных узлов в кластере AKS. Дополнительные сведения см. на странице [Цены на управляемые диски][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Создание заявки на доступ к постоянному тому

Утверждение постоянного тома (PVC) используется для автоматической подготовки хранилища на основе класса хранения. В этом случае утверждение постоянного тома может использовать один из предварительно созданных классов хранения для создания управляемого диска Azure уровня "Стандартный" или "Премиум".

Создайте файл `azure-premium.yaml` и скопируйте в него следующий манифест.

Обратите внимание на то, что в заметке указан класс хранения *managed-premium*, и эта заявка на доступ запрашивает диск емкостью *5 ГБ* с правами доступа *ReadWriteOnce*.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Создайте заявку на доступ к постоянному тому с помощью команды [kubectl apply][kubectl-apply] и укажите файл *azure-premium.yaml*.

```console
kubectl apply -f azure-premium.yaml
```

## <a name="use-the-persistent-volume"></a>Использование постоянного тома

После создания утверждения постоянного тома и успешной подготовки диска можно создать группу pod с доступом к диску. Приведенный ниже манифест создает модуль pod, который использует заявку на доступ к постоянному тому *azure-managed-disk* для подключения диска Azure по пути `/mnt/azure`.

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

```console
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
