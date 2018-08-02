---
title: Подключение тома secret в службе "Экземпляры контейнеров Azure"
description: Узнайте, как подключить том secret для хранения конфиденциальной информации, чтобы обеспечить доступ экземплярам контейнеров
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: marsma
ms.openlocfilehash: 572e6701bbe69bbb07c76d468a309030fc37d984
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39159895"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Подключение тома secret в службе "Экземпляры контейнеров Azure"

Используйте том *secret*, чтобы предоставить конфиденциальную информацию контейнерам в группе контейнеров. В томе *secret* в файлах хранятся секреты, к которым у контейнеров в группе контейнеров есть доступ. С помощью секретов в томе *secret* можно избежать добавления конфиденциальных данных, например ключей SSH или учетных данных базы данных, в коде приложения.

Все тома *secret* поддерживает [tmpfs][tmpfs] — файловая система, сохраняющая данные в ОЗУ. Их содержимое никогда не записывается в хранилище для долговременного хранения данных.

> [!NOTE]
> Тома *secret* сейчас ограничены контейнерами Linux. Сведения о том, как передавать переменные безопасной среды для контейнеров Windows и Linux см. в статье [Настройка переменных среды](container-instances-environment-variables.md). Мы работаем над тем, чтобы обеспечить все функции для контейнеров Windows, но для текущей платформы есть отличия в [квотах и доступности регионов для службы "Экземпляры контейнеров Azure"](container-instances-quotas.md).

## <a name="mount-secret-volume---azure-cli"></a>Подключение тома secret в Azure CLI

Чтобы развернуть контейнер с одним или несколькими секретами с помощью Azure CLI, добавьте параметры `--secrets` и `--secrets-mount-path` в команду [az container create][az-container-create]. В этом примере том *secret*, состоящий из двух секретов, "mysecret1" и "mysecret2", подключается к `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image microsoft/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

В следующих выходных данных [az container exec][az-container-exec] показано открытие оболочки в выполняемом контейнере, список файлов в томе secret, а затем их содержимое:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Подключение тома secret с помощью YAML

Вы также можете развернуть группы контейнеров с помощью Azure CLI и [шаблона YAML](container-instances-multi-container-yaml.md). При развертывании группы контейнеров, состоящей из нескольких контейнеров, рекомендуется использовать шаблон YAML.

При развертывании с помощью шаблона YAML значения секретов в шаблоне должны быть в **кодировке Base64**. Тем не менее значения секретов отображаются в виде обычного текста в файлах в контейнере.

Следующий шаблон YAML определяет группу контейнеров с одним контейнером, где том *secret* подключается к `/mnt/secrets`. Том secret имеет два секрета, "mysecret1" и "mysecret2".

```yaml
apiVersion: '2018-06-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Для развертывания с помощью шаблона YAML сохраните предыдущий YAML в файл с именем `deploy-aci.yaml`, а затем выполните команду [az container create][az-container-create] с параметром `--file`:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Подключение тома secret с помощью Resource Manager

Помимо развертывания с помощью CLI и шаблона YAML группу контейнеров также можно развернуть с помощью [шаблона Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Сначала заполните массив `volumes` в разделе `properties` группы контейнеров шаблона. При развертывании с помощью шаблона Resource Manager значения секретов в шаблоне должны быть в **кодировке Base64**. Тем не менее значения секретов отображаются в виде обычного текста в файлах в контейнере.

Затем для каждого контейнера в группе контейнеров, в которой нужно подключить том *secret*, заполните массив `volumeMounts` в разделе `properties` определения контейнера.

Следующий шаблон Resource Manager определяет группу контейнеров с одним контейнером, где том *secret* подключается к `/mnt/secrets`. Том secret имеет два секрета, "mysecret1" и "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json --> [!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Для развертывания с помощью шаблона Resource Manager сохраните предыдущий JSON в файл с именем `deploy-aci.json`, а затем выполните команду [az group deployment create][az-group-deployment-create] с параметром `--template-file`:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Дополнительная информация

### <a name="volumes"></a>Тома

Сведения о подключении других типов томов в службе "Экземпляры контейнеров Azure" см. в следующих статьях:

* [Подключение общего файлового ресурса Azure с помощью службы "Экземпляры контейнеров Azure"](container-instances-volume-azure-files.md)
* [Mount an emptyDir volume in Azure Container Instances](container-instances-volume-emptydir.md) (Подключение тома emptyDir в службе "Экземпляры контейнеров Azure")
* [Подключение тома gitRepo в службе "Экземпляры контейнеров Azure"](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Защита переменных среды

Другой способ предоставления конфиденциальных данных в контейнеры (включая контейнеры Windows) — воспользоваться [защищенными переменными среды](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
