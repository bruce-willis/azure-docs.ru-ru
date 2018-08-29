---
title: Использование службы файлов Azure с AKS
description: Использование дисков Azure с AKS.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/15/2018
ms.author: iainfou
ms.openlocfilehash: ea77244d4b2e078c5eda716e94a97291350228f5
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "42146979"
---
# <a name="persistent-volumes-with-azure-files"></a>Использование постоянных томов со службой файлов Azure

Постоянный том — это часть хранилища, которая создана для использования в кластере Kubernetes. Постоянный том может использоваться одним или несколькими модулями pod и может быть создан динамически или статически. В этом документе описано **динамическое создание** файлового ресурса Azure в качестве постоянного тома.

Дополнительные сведения о постоянных томах Kubernetes, в том числе о статическом создании см. в [этой статье][kubernetes-volumes].

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

При динамическом создании файлового ресурса Azure в качестве тома Kubernetes можно использовать любую учетную запись хранения, если она содержится в той же группе ресурсов **узла** AKS. В названии этой группы есть префикс *MC_*, который был создан при подготовке ресурсов кластера AKS. Получите имя группы ресурсов, выполнив команду [az aks show].

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

С помощью команды [az storage account create][az-storage-account-create] создайте учетную запись хранения.

Обновите `--resource-group` именем группы ресурсов, которая была получена на предыдущем шаге, а `--name` — любым именем. Укажите уникальное имя учетной записи хранения:

```azurecli
az storage account create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name mystorageaccount --sku Standard_LRS
```

> [!NOTE]
> Сейчас служба файлов Azure поддерживает только хранилище ценовой категории "Стандартный". Вы не сможете подготовить том, если используете хранилище ценовой категории "Премиум".

## <a name="create-a-storage-class"></a>Создание класса хранения

Класс хранения используется для определения того, как создается файловый ресурс Azure. В классе можно указать учетную запись хранения. Если учетная запись хранения не указана, то требуется указать *skuName* и *location*, после чего все учетные записи хранения в связанной группе ресурсов будут проверены на соответствие. Дополнительные сведения о классах хранения Kubernetes для файлов Azure см. в разделе о [классах хранения Kubernetes][kubernetes-storage-classes].

Создайте файл под названием `azure-file-sc.yaml` и скопируйте в него следующий пример манифеста. Укажите для параметра *storageAccount* имя вашей учетной записи хранения, созданной на предыдущем шаге. Дополнительные сведения о *mountOptions* см. в разделе [Параметры подключения][mount-options].

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
  storageAccount: mystorageaccount
```

Создайте класс хранения с помощью команды [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-sc.yaml
```

## <a name="create-a-cluster-role-and-binding"></a>Создание роли кластера и привязки

Чтобы ограничить действия, которые могут быть выполнены, кластеры AKS используют управление доступом на основе ролей (RBAC) Kubernetes. *Роли* определяют разрешения для предоставления, а *привязки* применяют их к желаемым пользователям. Эти назначения могут применяться для определенного пространства имен или в масштабах всего кластера. Дополнительные сведения см. в статье об [использовании авторизации RBAC][kubernetes-rbac].

Чтобы разрешить платформе Azure создавать необходимые ресурсы хранения, создайте *clusterrole* и *clusterrolebinding*. Создайте файл `azure-pvc-roles.yaml` и скопируйте в него следующий код YAML:

```yaml
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRole
metadata:
  name: system:azure-cloud-provider
rules:
- apiGroups: ['']
  resources: ['secrets']
  verbs:     ['get','create']
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: system:azure-cloud-provider
roleRef:
  kind: ClusterRole
  apiGroup: rbac.authorization.k8s.io
  name: system:azure-cloud-provider
subjects:
- kind: ServiceAccount
  name: persistent-volume-binder
  namespace: kube-system
```

Присвойте разрешения с помощью команды [применить kubectl][kubectl-apply]:

```console
kubectl apply -f azure-pvc-roles.yaml
```

## <a name="create-a-persistent-volume-claim"></a>Создание заявки на доступ к постоянному тому

Утверждение постоянного тома (PVC) использует объект класса хранения для динамической подготовки файлового ресурса Azure. Следующий код YAML можно использовать, чтобы создать утверждение постоянного тома в размере *5 ГБ* с доступом *ReadWriteMany*. Дополнительные сведения о режимах доступа см. в документации [по постоянным томам Kubernetes][access-modes].

Теперь создайте файл под названием`azure-file-pvc.yaml` и скопируйте в него следующий код YAML. Убедитесь, что *storageClassName* соответствует классу хранения, созданному на предыдущем шаге.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azurefile
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: azurefile
  resources:
    requests:
      storage: 5Gi
```

Создайте утверждение постоянного тома с помощью команды [kubectl apply][kubectl-apply]:

```console
kubectl apply -f azure-file-pvc.yaml
```

После ее выполнения будет создан файловый ресурс. Также будет создан секрет Kubernetes, содержащий сведения о подключении и учетные данные. Вы можете использовать команду [kubectl get][kubectl-get], чтобы просмотреть состояние PVC:

```
$ kubectl get pvc azurefile

NAME        STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
azurefile   Bound     pvc-8436e62e-a0d9-11e5-8521-5a8664dc0477   5Gi        RWX            azurefile      5m
```

## <a name="use-the-persistent-volume"></a>Использование постоянного тома

Следующий код YAML создает pod, использующий утверждение постоянного тома *azurefile* для подключения файлового ресурса Azure по пути */mnt/azure*.

Создайте файл `azure-pvc-files.yaml` и скопируйте в него следующий код YAML. Убедитесь, что *claimName* соответствует утверждению постоянного тома, созданному на предыдущем шаге.

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
        claimName: azurefile
```

Выполните команду [kubectl apply][kubectl-apply], чтобы создать pod.

```console
kubectl apply -f azure-pvc-files.yaml
```

Теперь у вас есть запущенный pod с диском Azure, подключенным к каталогу */mnt/azure*. Эта конфигурация может отображаться при проверке вашего модуля через `kubectl describe pod mypod`. Ниже приведен краткий пример выходных данных, где показано, что том подключен к контейнеру:

```
Containers:
  myfrontend:
    Container ID:   docker://053bc9c0df72232d755aa040bfba8b533fa696b123876108dec400e364d2523e
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:d85914d547a6c92faa39ce7058bd7529baacab7e0cd4255442b04577c4d1f424
    State:          Running
      Started:      Wed, 15 Aug 2018 22:22:27 +0000
    Ready:          True
    Mounts:
      /mnt/azure from volume (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-8rv4z (ro)
[...]
Volumes:
  volume:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  azurefile2
    ReadOnly:   false
[...]
```

## <a name="mount-options"></a>Параметры подключения

Значения по умолчанию *fileMode* and *dirMode* различаются между версиями Kubernetes, как описано в следующей таблице.

| версия | value |
| ---- | ---- |
| v1.6.x, v1.7.x | 0777 |
| v1.8.0-v1.8.5 | 0700 |
| v1.8.6 или выше | 0755 |
| v1.9.0 | 0700 |
| v1.9.1 или выше | 0755 |

При динамическом создании постоянного тома с классом хранилища и использовании кластера версии 1.8.5 или выше параметры подключения можно указать в объекте класса хранения. В следующем примере задается значение *0777*.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
parameters:
  skuName: Standard_LRS
```

При статическом создании постоянного тома и использовании кластера версии 1.8.5 или выше параметры подключения необходимо указать в объекте *PersistentVolume*. Дополнительные сведения о статическом создании постоянного тома см. в [этом разделе][pv-static].

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: azurefile
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  azureFile:
    secretName: azure-secret
    shareName: azurefile
    readOnly: false
  mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  ```

При использовании кластера версии 1.8.0–1.8.4 контекст безопасности можно указать, задав для *runAsUser* значение *0*. Дополнительные сведения о контексте безопасности Pod см. в разделе [Configure a Security Context][kubernetes-security-context] (Настройка контекста безопасности).

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о постоянных томах Kubernetes, использующих службу файлов Azure.

> [!div class="nextstepaction"]
> [Подключаемый модуль Kubernetes для службы файлов Azure][kubernetes-files]

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-files]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_file/README.md
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-security-context]: https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/#azure-file
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[pv-static]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#static
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-list]: /cli/azure/group#az-group-list
[az-resource-show]: /cli/azure/aks#az-aks-show
[az-storage-account-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-create]: /cli/azure/storage/account#az-storage-account-create
[az-storage-key-list]: /cli/azure/storage/account/keys#az-storage-account-keys-list
[az-storage-share-create]: /cli/azure/storage/share#az-storage-share-create
[mount-options]: #mount-options
