---
title: Краткое руководство по Azure. Создание концентратора событий с помощью PowerShell | Документация Майкрософт
description: В этом кратком руководстве объясняется, как создать концентратор событий с помощью Azure PowerShell и как затем отправлять и получать данные, используя .NET Standard.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: b1f0c0d06f6c5f99a843a384e1dda667b967a02b
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42743913"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Краткое руководство. Создание концентратора событий с помощью Azure PowerShell

Центры событий Azure — это высокомасштабируемая платформа потоковой передачи данных и служба приема событий, принимающая и обрабатывающая миллионы событий в секунду. В этом кратком руководстве показано, как создать концентратор событий с помощью Azure PowerShell и как затем отправлять и получать данные из концентратора событий, используя пакет SDK для .NET Standard.

Для работы с этим кратким руководством вам потребуется подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись][], прежде чем начать работу.

## <a name="prerequisites"></a>Предварительные требования

В рамках этого руководства вам потребуются:

- [Visual Studio 2017 с обновлением 3 (версия 15.3, 26730.01)](http://www.visualstudio.com/vs) или более новая версия.
- [Пакет SDK для .NET Standard](https://www.microsoft.com/net/download/windows) версии 2.0 или более новой.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы используете PowerShell локально, то для работы с этим кратким руководством необходимо запускать последнюю версию PowerShell. Если вам нужно выполнить установку или обновление, см. руководство по [установке и настройке Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="provision-resources"></a>Подготовка ресурсов

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов — это логическая коллекция ресурсов Azure, она необходима для создания концентратора событий. 

В следующем примере создается группа ресурсов в регионе "Восточная часть США". Замените значение `myResourceGroup` именем вашей группы ресурсов:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Создание пространства имен в Центрах событий

После создания группы ресурсов создайте пространство имен Центров событий в этой группе ресурсов. Пространство имен Центров событий предоставляет уникальное полное доменное имя, в котором можно создать концентратор событий. Замените значение `namespace_name` уникальным именем вашего пространства имен:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>Создание концентратора событий

Теперь, когда у вас есть пространство имен Центров событий, создайте концентратор событий в этом пространстве имен:

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Создание учетной записи хранения для узла обработчика событий

Узел обработчика событий упрощает прием событий от Центров событий путем управления контрольными точками и параллельными приемниками. Для создания контрольных точек узлу обработчика событий требуется учетная запись хранения. Чтобы создать учетную запись хранения и получить ее ключи, выполните следующие команды:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>Получение строки подключения

Строка подключения необходима для подключения к концентраторам событий и для обработки событий. Чтобы получить сведения о строке подключения, выполните:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Потоковая передача в Центры событий

Теперь вы можете запустить потоковую передачу в Центры событий. Примеры можно загрузить или клонировать из [репозитория Центров событий GitHub](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Отправка событий

Чтобы начать потоковую передачу событий, загрузите пример [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) из GitHub или клонируйте [репозиторий Центров событий на GitHub](https://github.com/Azure/azure-event-hubs), выполнив следующую команду:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Перейдите в папку \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender и загрузите файл SampleSender.sln в Visual Studio.

Затем добавьте пакет Nuget [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) в проект.

В файле Program.cs замените следующие заполнители именем концентратора событий и строкой подключения:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Теперь выполните сборку и запуск примера. Вы увидите события, отправляемые в концентратор событий:

![][3]

### <a name="receive-and-process-events"></a>Прием и обработка событий

Теперь загрузите образец приемника узла обработчика событий, который принимает сообщения, отправленные вами. Загрузите пример [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) из GitHub или клонируйте [репозиторий Центров событий на GitHub](https://github.com/Azure/azure-event-hubs), выполнив следующую команду:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Перейдите в папку \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver и загрузите файл решения SampleEphReceiver.sln в Visual Studio.

Затем добавьте пакеты Nuget [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) и [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) в проект.

В файле Program.cs присвойте следующим константам соответствующие значения:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Теперь выполните сборку и запуск примера. Вы увидите события, получаемые вашим приложением:

![][4]

На портале Azure можно посмотреть скорость, с которой события обрабатываются в данном пространстве имен Центров событий, как это изображено ниже:

![][5]

## <a name="clean-up-resources"></a>Очистка ресурсов

После завершения этого краткого руководства можно удалить группу ресурсов и содержащиеся в ней пространство имен, учетную запись хранения и концентратор событий. Замените значение `myResourceGroup` именем вашей группы ресурсов. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Дальнейшие действия

С помощью этой статьи вы создали пространство имен Центров событий и другие ресурсы, необходимые для отправки и получения событий в концентраторе событий. Для получения дополнительных сведений перейдите к следующему руководству:

> [!div class="nextstepaction"]
> [Визуализация аномальных данных в потоках данных Центров событий](event-hubs-tutorial-visualize-anomalies.md)

[создайте бесплатную учетную запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
