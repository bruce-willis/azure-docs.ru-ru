---
title: Сохранение состояния с помощью подключения тома службы файлов Azure в контейнере в приложении службы "Сетка Service Fabric" | Документация Майкрософт
description: Узнайте, как сохранить состояние с помощью подключения тома службы файлов Azure в контейнере приложения службы "Сетка Service Fabric", используя Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 94a4e17e6285893520a2f6482b32a69b1229e2fa
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090638"
---
# <a name="store-state-by-mounting-azure-files-based-volume-in-service-fabric-mesh-application"></a>Сохранение состояния с помощью подключения тома службы файлов Azure в приложении службы "Сетка Service Fabric"

В этой статье показано, как сохранить состояние в службе файлов Azure, подключив том в контейнере приложения Service Fabric Mesh. В этом примере приложение Counter содержит службу ASP.NET Core с веб-страницей, на которой в браузере отображается значение счетчика. 

`counterService` периодически считывает значение счетчика из файла, увеличивает его и записывает обратно в файл. Этот файл хранится в папке, подключенной к тому в общем ресурсе службы файлов Azure. 

## <a name="set-up-service-fabric-mesh-cli"></a>Настройка CLI для Сетки Service Fabric 
Для выполнения этой задачи можно использовать Azure Cloud Shell или локальный экземпляр Azure CLI. Установите модуль расширения интерфейса командной строки службы "Сетка Azure Service Fabric", выполнив следующие [инструкции](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Вход в Azure
Войдите в Azure и настройте подписку.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-file-share"></a>Создание общей папки 
Создайте файловый ресурс Azure, следуя этим [инструкциям](/azure/storage/files/storage-how-to-create-file-share). В следующих инструкциях имя учетной записи хранения, ключ учетной записи хранения и имя файлового ресурса обозначены как `<storageAccountName>`, `<storageAccountKey>` и `<fileShareName>` соответственно.

## <a name="create-resource-group"></a>Создать группу ресурсов
Создайте группу ресурсов, в которую будет развертываться приложение. Можно использовать существующую группу ресурсов и пропустить этот шаг. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-template"></a>Развертывание шаблона

Создайте приложение и связанные ресурсы, выполнив приведенную ниже команду, и укажите значения `storageAccountName`, `storageAccountKey` и `fileShareName` из предыдущего шага.

Параметр `storageAccountKey` в шаблоне имеет значение `securestring`. Он не отображается в данных состояния развертывания и выходных данных команд `az mesh service show`. Убедитесь, что он правильно указан в следующей команде.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json  --parameters "{\"location\": {\"value\": \"eastus\"}, \"fileShareName\": {\"value\": \"<fileShareName>\"}, \"storageAccountName\": {\"value\": \"<storageAccountName>\"}, \"storageAccountKey\": {\"value\": \"<storageAccountKey>\"}}"
```

Предыдущая команда развертывает приложение Linux, используя [шаблон mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.linux.json). Если необходимо развернуть приложение Windows, используйте [шаблон mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/counter/mesh_rp.windows.json). Образы контейнера для ОС Windows больше, чем образы контейнера для Linux, поэтому может потребоваться больше времени для развертывания.

Через несколько минут команда должна вернуть следующий результат.

`counterApp has been deployed successfully on counterAppNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Запуск приложения
После успешного развертывания приложения получите общедоступный IP-адрес конечной точки службы и откройте его в браузере. Отобразится веб-страница со значением счетчика, которое обновляется каждую секунду.

Команда развертывания вернет общедоступный IP-адрес конечной точки службы. При необходимости вы можете запросить сетевой ресурс, чтобы найти общедоступный IP-адрес конечной точки службы. 
 
Имя сетевого ресурса для этого приложения — `counterAppNetwork`. Получить сведения о нем можно с помощью следующей команды. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name counterAppNetwork
```

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Проверка доступности тома для приложения
Приложение создает файл `counter.txt` в файловом ресурсе, расположенном в папке `counter/counterService`. Этот файл содержит значение счетчика, отображаемое на веб-странице.

Данный файл можно скачать с помощью любого инструмента, поддерживающего обзор файлового ресурса в службе файлов Azure. [Обозреватель службы хранилища Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/) является примером инструмента.

## <a name="delete-the-resources"></a>Удаление ресурсов

В целях экономии ограниченных ресурсов, выделенных для предварительной версии программы, регулярно удаляйте ресурсы. Чтобы удалить ресурсы, относящиеся к этому примеру, удалите группу ресурсов, в которой они были развернуты.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с примером приложения, использующего том службы файлов Azure, на сайте [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Узнайте больше о модели ресурсов Service Fabric из раздела [Introduction to Service Fabric Resource Model](service-fabric-mesh-service-fabric-resources.md) (Общие сведения о модели ресурсов Service Fabric).
- Узнайте больше о службе "Сетка Service Fabric" из раздела [Что такое Сетка Service Fabric?](service-fabric-mesh-overview.md)
