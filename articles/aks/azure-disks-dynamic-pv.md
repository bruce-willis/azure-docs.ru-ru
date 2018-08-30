---
title: Создание постоянных томов в службе Azure Kubernetes
description: Узнайте, как с помощью дисков Azure создавать постоянные тома для модулей pod в службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/20/2018
ms.author: iainfou
ms.openlocfilehash: 7048ab4e08d25fd5181857a4e7592d0bcb7d3b5f
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42885600"
---
# <a name="create-persistent-volumes-with-azure-disks-for-azure-kubernetes-service-aks"></a>Создание постоянных томов в службе Azure Kubernetes (AKS) с помощью дисков Azure

Постоянный том — это часть хранилища, которая подготовлена к использованию для модулей pod Kubernetes. Постоянный том может использоваться одним или несколькими модулями pod и может быть подготовлен динамически или статически. Дополнительные сведения о постоянных томах Kubernetes см. в разделе [Kubernetes persistent volumes][kubernetes-volumes] (Постоянные тома Kubernetes). В этой статье описывается использование постоянных томов с дисками Azure в кластере службы Azure Kubernetes (AKS).

> [!NOTE]
> Диск Azure можно подключить только в *режиме доступа* *ReadWriteOnce*, то есть он будет доступен только для одного узла AKS. Если вам нужно использовать постоянный том на нескольких узлах, попробуйте использовать [службу файлов Azure][azure-files-pvc].

## <a name="built-in-storage-classes"></a>Встроенные классы хранения

Класс хранения используется для определения того, как единица хранения создается динамически с помощью постоянного тома. Дополнительные сведения о классах хранения Kubernetes см. в разделе [Kubernetes Storage Classes][kubernetes-storage-classes] (Классы хранения Kubernetes).

Каждый кластер AKS содержит два предварительно созданных класса хранения, настроенных для работы с дисками Azure.

* Класс хранения *default* подготавливает диск Azure ценовой категории "Стандартный".
    * Хранилище класса Standard использует жесткие диски и обеспечивает экономичное хранение при достаточной производительности. Эти диски идеально подходят для экономных рабочих нагрузок разработки и тестирования.
* Класс хранения *managed-premium* подготавливает диск Azure ценовой категории "Премиум".
    * Диски уровня "Премиум" используют высокопроизводительные твердотельные накопители с низкой задержкой. Они идеально подходят для виртуальных машин, выполняющих производственную рабочую нагрузку. Если узлы AKS в кластере используют хранилище класса Premium, выберите класс *managed-premium*.

Используйте команду [kubectl get sc][kubectl-get], чтобы просмотреть предварительно созданные классы хранения. В следующем примере демонстрируются предварительно созданные классы хранения, доступные для кластера AKS.

```
$ kubectl get sc

NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   1h
managed-premium     kubernetes.io/azure-disk   1h
```

> [!NOTE]
> Утверждения постоянных томов указаны в ГиБ, но счета за использование управляемых дисков Azure выставляются по номеру SKU для определенного размера. Эти номера SKU варьируются от 32 ГиБ для дисков S4 или P4 и до 4 ТиБ для дисков S50 или P50. Число операций ввода-вывода в секунду и пропускная способность управляемого диска уровня "Премиум" зависят как от номера SKU, так и от размера экземпляров узлов в кластере AKS. Дополнительные сведения см. на странице [Цены на управляемые диски][managed-disk-pricing-performance].

## <a name="create-a-persistent-volume-claim"></a>Создание заявки на доступ к постоянному тому

Утверждение постоянного тома (PVC) используется для автоматической подготовки хранилища на основе класса хранения. В этом случае утверждение постоянного тома может использовать один из предварительно созданных классов хранения для создания управляемого диска Azure уровня "Стандартный" или "Премиум".

Создайте файл `azure-premium.yaml` и скопируйте в него следующий манифест. Утверждение запрашивает диск с именем `azure-managed-disk` размером *5 ГБ* с доступом *ReadWriteOnce*. Класс хранилища *managed-premium* указан как класс хранения.

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

> [!TIP]
> Чтобы создать диск, который использует хранилище уровня "Стандартный", используйте класс `storageClassName: default` вместо *managed-premium*.

Создайте заявку на доступ к постоянному тому с помощью команды [kubectl create][kubectl-create] и укажите файл *azure-premium.yaml*.

```
$ kubectl create -f azure-premium.yaml

persistentvolumeclaim/azure-managed-disk created
```

## <a name="use-the-persistent-volume"></a>Использование постоянного тома

После создания утверждения постоянного тома и успешной подготовки диска можно создать группу pod с доступом к диску. Приведенный ниже манифест создает базовый модуль pod NGINX, который использует утверждение постоянного тома *azure-managed-disk* для подключения диска Azure по пути `/mnt/azure`.

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

Создайте модуль pod с помощью команды [kubectl create][kubectl-create], как показано ниже.

```
$ kubectl create -f azure-pvc-disk.yaml

pod/mypod created
```

Теперь у вас есть работающий pod с диском Azure, подключенным к каталогу `/mnt/azure`. Эту конфигурацию можно видеть при проверке модуля с помощью `kubectl describe pod mypod`, как показано в сокращенном примере ниже.

```
$ kubectl describe pod mypod

[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azure-managed-disk
    ReadOnly:   false
  default-token-smm2n:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-smm2n
    Optional:    false

Events:
  Type    Reason                 Age   From                               Message
  ----    ------                 ----  ----                               -------
  Normal  Scheduled              2m    default-scheduler                  Successfully assigned mypod to aks-nodepool1-79590246-0
  Normal  SuccessfulMountVolume  2m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "default-token-smm2n"
  Normal  SuccessfulMountVolume  1m    kubelet, aks-nodepool1-79590246-0  MountVolume.SetUp succeeded for volume "pvc-faf0f176-8b8d-11e8-923b-deb28c58d242"
[...]
```

## <a name="back-up-a-persistent-volume"></a>Резервное копирование постоянного тома

Для резервного копирования данных на постоянном томе создайте моментальный снимок управляемого диска для этого тома. Затем этот моментальный снимок можно использовать для создания восстановленного диска и присоединить к модулям pod в качестве средства восстановления этих данных.

В первую очередь получите имя тома с помощью команды `kubectl get pvc`, например для PVC с именем *azure-managed-disk*:

```
$ kubectl get pvc azure-managed-disk

NAME                 STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
azure-managed-disk   Bound     pvc-faf0f176-8b8d-11e8-923b-deb28c58d242   5Gi        RWO            managed-premium   3m
```

Это имя тома формирует имя соответствующего диска Azure. Отправьте запрос идентификатора диска с помощью команды [az disk list][az-disk-list] и укажите имя тома PVC, как показано в примере ниже.

```
$ az disk list --query '[].id | [?contains(@,`pvc-faf0f176-8b8d-11e8-923b-deb28c58d242`)]' -o tsv

/subscriptions/<guid>/resourceGroups/MC_MYRESOURCEGROUP_MYAKSCLUSTER_EASTUS/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

Используйте идентификатор диска, чтобы создать моментальный снимок диска с помощью команды [az snapshot create][az-snapshot-create]. В следующем примере создается моментальный снимок с именем *pvcSnapshot* в той же группе ресурсов, что и кластер AKS (*MC_myResourceGroup_myAKSCluster_eastus*). При создании моментальных снимков и восстановлении дисков в группах ресурсов могут возникнуть проблемы, когда кластер AKS не может получить к доступ к этим дискам.

```azurecli
$ az snapshot create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name pvcSnapshot \
    --source /subscriptions/<guid>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/MicrosoftCompute/disks/kubernetes-dynamic-pvc-faf0f176-8b8d-11e8-923b-deb28c58d242
```

В зависимости от объема данных на диске создание моментального снимка может занять несколько минут.

## <a name="restore-and-use-a-snapshot"></a>Восстановление из моментального снимка и его использование

Чтобы восстановить диск и использовать его в модуле pod Kubernetes, используйте моментальный снимок как источник при создании диска с помощью команды [az disk create][az-disk-create]. Эта операция сохраняет исходный ресурс, если необходимо получить доступ к исходному моментальному снимку данных. В следующем примере создается диск с именем *pvcRestored* из моментального снимка с именем *pvcSnapshot*.

```azurecli
az disk create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --source pvcSnapshot
```

Чтобы использовать восстановленный диск с модулем pod, укажите идентификатор этого диска в манифесте. Получите идентификатор диска с помощью команды [az disk show][az-disk-show]. В следующем примере возвращается идентификатор диска *pvcRestored*, созданного на предыдущем шаге.

```azurecli
az disk show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name pvcRestored --query id -o tsv
```

Создайте манифест модуля pod с именем `azure-restored.yaml` и укажите URI-код диска, полученного на предыдущем шаге. В следующем примере создается базовый веб-сервер NGINX с восстановленным диском, подключенным в качестве тома в точке */mnt/azure*.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypodrestored
spec:
  containers:
    - name: myfrontendrestored
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      azureDisk:
        kind: Managed
        diskName: pvcRestored
        diskURI: /subscriptions/<guid>/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
```

Создайте модуль pod с помощью команды [kubectl create][kubectl-create], как показано ниже.

```
$ kubectl create -f azure-restored.yaml

pod/mypodrestored created
```

Можно использовать `kubectl describe pod mypodrestored` для просмотра сведений о модуле pod, как в следующем кратком примере отображения сведений о томе.

```
$ kubectl describe pod mypodrestored

[...]
Volumes:
  volume:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     pvcRestored
    DiskURI:      /subscriptions/19da35d3-9a1a-4f3b-9b9c-3c56ef409565/resourceGroups/MC_myResourceGroupAKS_myAKSCluster_eastus/providers/Microsoft.Compute/disks/pvcRestored
    Kind:         Managed
    FSType:       ext4
    CachingMode:  ReadWrite
    ReadOnly:     false
[...]
```

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о постоянных томах Kubernetes, использующих диски Azure.

> [!div class="nextstepaction"]
> [Storage Classes][azure-disk-volume] (Классы хранения)

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/windows/premium-storage.md
[az-disk-list]: /cli/azure/disk#az-disk-list
[az-snapshot-create]: /cli/azure/snapshot#az-snapshot-create
[az-disk-create]: /cli/azure/disk#az-disk-create
[az-disk-show]: /cli/azure/disk#az-disk-show
