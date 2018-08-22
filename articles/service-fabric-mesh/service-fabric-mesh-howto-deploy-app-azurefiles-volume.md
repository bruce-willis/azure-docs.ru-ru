---
title: Сохранение состояния в приложении "Сетка Azure Service Fabric" путем подключения тома службы файлов Azure внутри контейнера | Документация Майкрософт
description: Узнайте, как сохранить состояние в приложении "Сетка Azure Service Fabric" путем подключения тома службы файлов Azure в контейнере с помощью Azure CLI.
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
ms.date: 08/09/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 3b350deff2883761af6a3a2b3c5c9ef22235bde0
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40038354"
---
# <a name="store-state-in-an-azure-service-fabric-mesh-application-by-mounting-an-azure-files-based-volume-inside-the-container"></a>Сохранение состояния в приложении "Сетка Azure Service Fabric" путем подключения тома службы файлов Azure внутри контейнера

В этой статье показано, как сохранить состояние в службе файлов Azure, подключив том в контейнере приложения Service Fabric Mesh. В этом примере приложение Counter содержит службу ASP.NET Core с веб-страницей, на которой в браузере отображается значение счетчика. 

`counterService` периодически считывает значение счетчика из файла, увеличивает его и записывает обратно в файл. Этот файл хранится в папке, подключенной к тому в общем ресурсе службы файлов Azure.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этой задачи можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Чтобы использовать Azure CLI в рамках работы с этой статьей, убедитесь, что `az --version` возвращает по крайней мере версию `azure-cli (2.0.43)`.  Установите (или обновите) модуль расширения интерфейса командной строки службы "Сетка Azure Service Fabric", выполнив эти [инструкции](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите в Azure и настройте подписку.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-file-share"></a>Создание общей папки

Создайте файловый ресурс Azure, следуя этим [инструкциям](/azure/storage/files/storage-how-to-create-file-share). В следующих инструкциях имя учетной записи хранения, ключ учетной записи хранения и имя файлового ресурса обозначены как `<storageAccountName>`, `<storageAccountKey>` и `<fileShareName>` соответственно. Эти значения доступны на портале Azure:
* <storageAccountName> В разделе **Учетные записи хранения** это имя учетной записи хранения, которое вы использовали при создании файлового ресурса.
* <storageAccountKey> Выберите учетную запись хранения в разделе **Учетные записи хранения**, а затем выберите **Ключи доступа** и используйте значение в разделе **key1**.
* <fileShareName> Выберите учетную запись хранения в разделе **Учетные записи хранения**, а затем выберите **Файлы**. Нужно использовать имя только что созданного файлового ресурса.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Создайте группу ресурсов, в которую будет развертываться приложение. Следующая команда создает группу ресурсов с именем `myResourceGroup` в расположении на востоке Соединенных Штатов Америки.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Развертывание шаблона

Создайте приложение и связанные ресурсы, выполнив приведенную ниже команду, и укажите значения `storageAccountName`, `storageAccountKey` и `fileShareName` из предыдущего шага [создания файлового ресурса](#create-a-file-share).

Параметр `storageAccountKey` в шаблоне представляет собой защищенную строку. Он не отображается в данных состояния развертывания и выходных данных команд `az mesh service show`. Убедитесь, что он правильно указан в следующей команде.

Следующая команда развертывает приложение Linux с помощью шаблона [mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Чтобы развернуть приложение Windows, используйте шаблон [mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Имейте в виду, что развертывание больших образов контейнеров может занять больше времени.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Через несколько минут команда должна вернуть результат `counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Запуск приложения

Команда развертывания вернет общедоступный IP-адрес конечной точки службы. После успешного развертывания приложения получите общедоступный IP-адрес конечной точки службы и откройте его в браузере. Отобразится веб-страница со значением счетчика, которое обновляется каждую секунду.

Имя сетевого ресурса для этого приложения — `counterAppNetwork`. С помощью следующей команды можно просмотреть сведения о приложении, такие как его описание, расположение, группа ресурсов и т. д.

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Проверка доступности тома для приложения

Приложение создает файл `counter.txt` в файловом ресурсе, расположенном в папке `counter/counterService`. Этот файл содержит значение счетчика, отображаемое на веб-странице.

Этот файл можно скачать с помощью любого инструмента, поддерживающего просмотр файлового ресурса в службе файлов Azure, например такого как [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="delete-the-resources"></a>Удаление ресурсов

Регулярно удаляйте ресурсы, которые больше не используются в Azure. Чтобы удалить ресурсы, относящиеся к этому примеру, удалите группу ресурсов, в которой они были развернуты (при этом будут удалены все элементы, связанные с этой группой ресурсов), с помощью следующей команды:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с примером приложения, использующего том службы файлов Azure, на сайте [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Узнайте больше о модели ресурсов Service Fabric из раздела [Introduction to Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md) (Общие сведения о модели ресурсов Service Fabric).
- Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)