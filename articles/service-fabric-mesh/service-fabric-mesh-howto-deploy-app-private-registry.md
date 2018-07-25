---
title: Развертывание приложения из частного реестра в службе "Сетка Azure Service Fabric" | Документация Майкрософт
description: Узнайте, как развернуть приложение, которое использует частный реестр контейнеров, в службе "Сетка Service Fabric" с помощью Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: af92d3c6ea881d00ec687a5560bf4db35aa431c5
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089492"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Развертывание приложения службы "Сетка Service Fabric" из частного реестра образов контейнеров

В этой статье показано, как развернуть приложение службы "Сетка Service Fabric Azure", которое использует частный реестр образов контейнеров.

## <a name="prerequisites"></a>Предварительные требования

### <a name="set-up-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric 
Для выполнения этой задачи можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Установите модуль расширения интерфейса командной строки службы "Сетка Azure Service Fabric", выполнив следующие [инструкции](service-fabric-mesh-howto-setup-cli.md).

### <a name="install-docker"></a>Установка Docker

Чтобы поддерживать контейнеризованные приложения Service Fabric, используемые службой "Сетка Service Fabric", установите Docker.

### <a name="windows-10"></a>Windows 10

Загрузите и установите последнюю версию [Docker Community Edition для Windows][download-docker]. 

Во время процесса установки при появлении запроса выберите **Использовать контейнеры Windows вместо контейнеров Linux**. Затем необходимо будет выйти из системы и снова войти. Если функция Hyper-V не была ранее включена, то после входа может потребоваться ее включить. Включите Hyper-V и затем перезагрузите компьютер.

После перезагрузки Docker предложит включит такую функцию, как **Containers**. Включите ее и повторно перезагрузите компьютер.

### <a name="windows-server-2016"></a>Windows Server 2016

Для установки Docker используйте следующие команды PowerShell. Дополнительные сведения см. в статье [Install Docker Enterprise Edition for Windows Server][download-docker-server] (Установка Docker Enterprise Edition для Windows).

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Перезагрузите компьютер.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в Azure и настройте активную подписку.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Создание реестра контейнеров и отправка в него образа

Создайте реестр контейнеров Azure, следуя инструкциям в разделе [Краткое руководство. Создание реестра контейнеров с использованием Azure CLI](../container-registry/container-registry-get-started-azure-cli.md). Выполните действия до шага [Вход в ACR](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr). 

### <a name="push-image-to-acr"></a>Отправка образа в ACR

Чтобы отправить образ в реестр контейнеров Azure, сначала нужно получить этот образ. Если у вас еще нет образов локального контейнера, используйте следующую команду, чтобы извлечь существующий образ из Docker Hub.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Прежде чем отправить образ в реестр, нужно добавить в него тег с полным именем сервера входа для ACR. Выполните следующую команду, чтобы получить полное имя сервера входа для экземпляра ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Присвойте образу тег с помощью команды [docker tag][docker-tag]. Замените значение `<acrLoginServer>` именем сервера входа для экземпляра ACR.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>Список образов контейнеров

В следующем примере перечисляются репозитории в реестре:

```azurecli
az acr repository list --name <acrName> --output table
```

Выходные данные:

```bash
Result
----------------
azure-mesh-helloworld
```

В следующем примере перечисляются теги в репозитории **azure-mesh-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

Выходные данные:

```bash
Result
--------
1.1-alpine
```
Приведенные выше выходные данные подтверждают наличие `azure-mesh-helloworld:1.1-alpine` в частном реестре контейнеров.

## <a name="retrieve-credentials-for-the-registry"></a>Получение учетных данных для реестра

Чтобы развернуть экземпляр контейнера из реестра, который был создан, во время развертывания необходимо указать учетные данные. Добавьте администратора в реестр, выполнив следующую команду.

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Получите имя сервера, имя пользователя и пароль для реестра с помощью следующих команд.

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Значения из предыдущих команд обозначены в следующей команде как `<acrLoginServer>`, `<acrUserName>` и `<acrPassword>`.


## <a name="deploy-the-template"></a>Развертывание шаблона

Создайте приложение и связанные ресурсы, выполнив приведенную ниже команду, и укажите учетные данные из предыдущего шага.

Параметр `registry-password` в шаблоне имеет значение `securestring`. Он не отображается в данных состояния развертывания и выходных данных команд `az mesh service show`. Убедитесь, что он правильно указан в следующей команде.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}" 
```

Через несколько минут команда должна вернуть следующий результат.

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Запуск приложения
После успешного развертывания приложения получите общедоступный IP-адрес конечной точки службы и откройте его в браузере. Отобразится веб-страница с логотипом службы "Сетка Service Fabric".

Команда развертывания вернет общедоступный IP-адрес конечной точки службы. При необходимости вы можете запросить сетевой ресурс, чтобы найти общедоступный IP-адрес конечной точки службы. 
 
Имя сетевого ресурса для этого приложения — `helloWorldPrivateRegistryNetwork`. Получить сведения о нем можно с помощью следующей команды. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Удаление ресурсов

В целях экономии ограниченных ресурсов, выделенных для предварительной версии программы, регулярно удаляйте ресурсы. Чтобы удалить ресурсы, относящиеся к этому примеру, удалите группу ресурсов, в которой они были развернуты.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Дополнительная информация
- Ознакомьтесь с примером приложения Hello World на сайте [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Узнайте больше о модели ресурсов Service Fabric из раздела [Introduction to Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md) (Общие сведения о модели ресурсов Service Fabric).
- Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/