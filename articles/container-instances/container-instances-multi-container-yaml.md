---
title: Развертывание многоконтейнерных групп в службе "Экземпляры контейнеров Azure" с помощью Azure CLI и YAML
description: Узнайте, как развернуть группу с несколькими контейнерами в службе "Экземпляры контейнеров Azure" с помощью Azure CLI и YAML-файла.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/17/2018
ms.author: marsma
ms.openlocfilehash: 1d1885112b8e7f7b1e187073c86d561eb57fd23f
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114469"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Развертывание многоконтейнерной группы с помощью YAML-файла

Служба "Экземпляры контейнеров Azure" поддерживает развертывание нескольких контейнеров в одном узле с использованием [группы контейнеров](container-instances-container-groups.md). Это полезно при создании сопроводительного приложения для ведения журнала, мониторинга или любой другой конфигурации, когда службе требуется еще один прикрепленный процесс.

Есть два метода развертывания многоконтейнерных групп с помощью Azure CLI:

* развертывание YAML-файла (в этой статье);
* [развертывание шаблонов Resource Manager](container-instances-multi-container-group.md).

Из-за более сжатого характера формата YAML-файла развертывание с помощью такого файла рекомендуется, если оно включает *только* экземпляры контейнера. Если вам нужно развернуть дополнительные ресурсы службы Azure (например, общие файловые ресурсы Azure) во время развертывания экземпляра контейнера, рекомендуется использовать развертывание шаблонов Resource Manager.

> [!NOTE]
> Многоконтенерные группы сейчас ограничены контейнерами Linux. Мы работаем над тем, чтобы обеспечить все функции для контейнеров Windows, но для текущей платформы есть отличия в [квотах и доступности регионов для службы "Экземпляры контейнеров Azure"](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Настройка YAML-файла

Чтобы развернуть многоконтейнерную группу с помощью команды [az container create][az-container-create] в Azure CLI, вы должны указать конфигурацию группы контейнеров в YAML-файле, а затем передать его как параметр в команду выше.

Сначала скопируйте следующий YAML-файл в новый файл с именем **deploy-aci.yaml**.

Этот YAML-файл определяет группу контейнеров с именем "myContainerGroup", имеющую два контейнера, общедоступный IP-адрес и два предоставленных порта. Первый контейнер в группе запускает веб-приложение с выходом в Интернет. Второй контейнер, расширение, периодически отправляет HTTP-запросы веб-приложению, запущенному в первом контейнере через локальную сеть группы контейнеров.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>Развертывание группы контейнеров

Создайте группу ресурсов с помощью команды [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Выполните развертывание группы контейнеров с помощью команды [az container create][az-container-create], передав YAML-файл как аргумент:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

В течение нескольких секунд вы должны получить исходный ответ Azure.

## <a name="view-deployment-state"></a>Просмотр состояния развертывания

Чтобы просмотреть состояние развертывания, используйте команду [az container show][az-container-show] ниже.

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Если вы хотите просмотреть запущенное приложение, перейдите к его IP-адресу в своем браузере. Например, в этом примере выходных данных используется IP-адрес `52.168.26.124`:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Просмотр журналов

Просмотрите выходные данные журнала контейнера с помощью команды [az container logs][az-container-logs]. Аргумент `--container-name` определяет контейнер, из которого извлекаются журналы. В этом примере указывается первый контейнер.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Выходные данные:

```console
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Чтобы просмотреть журналы для сопроводительного контейнера, выполните ту же команду, указав имя второго контейнера.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Выходные данные:

```console
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Как видите, сопроводительное приложение периодически выполняет HTTP-запрос к основному веб-приложению через локальную сеть группы, чтобы убедиться, что оно работает. Пример сопроводительного приложения можно расширить, реализовав активацию оповещения при получении кода ответа HTTP, отличающегося от 200 OK.

## <a name="deploy-from-private-registry"></a>Развертывание с помощью частного реестра

Чтобы использовать частный реестр образов контейнера, добавьте следующий YAML-файл со значениями, измененными для вашей среды:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Например, в следующем YAML-файле развертывается группа контейнеров с одним контейнером, чей образ извлекается из частного реестра контейнеров Azure с именем "myregistry":

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Экспорт группы контейнеров в YAML-файл

Вы можете экспортировать конфигурацию имеющейся группы контейнеров в YAML-файл, используя команду Azure CLI [az container export][az-container-export].

Экспорт эффективен для сохранения конфигурации группы контейнеров, так как во время него эти конфигурации сохраняются в системе управления версиями для "конфигурации как кода". Или вы можете использовать экспортированный файл в качестве отправной точки при разработке новой конфигурации в YAML-файле.

Экспортируйте конфигурацию для созданной ранее группы контейнеров, выполнив следующую команду [az container export][az-container-export]:

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

Если команда выполнена успешно, выходные данные не появятся, но вы можете просмотреть содержимое файла, чтобы увидеть результат. Например, первые несколько строк с атрибутом `head`:

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье описаны шаги по развертыванию многоконтейнерного экземпляра контейнера Azure. Чтобы получить исчерпывающие сведения о службе "Экземпляры контейнеров Azure", включая использование частного реестра контейнеров Azure, ознакомьтесь с приведенным ниже руководством.

> [!div class="nextstepaction"]
> [Руководство по использованию службы "Экземпляры контейнеров Azure"][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
