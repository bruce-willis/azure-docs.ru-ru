---
title: Обзор интерфейсов API Центров событий Azure для платформы .NET Standard | Документация Майкрософт
description: Обзор API для платформы .NET Standard
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: 9b952bd96828c4f2c140cb2d75cecb9379895a63
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746649"
---
# <a name="event-hubs-net-standard-api-overview"></a>Обзор API Центров событий для платформы .NET Standard

В этой статье перечислены некоторые ключевые [клиентские API Центров событий Azure для .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/). Сейчас существует две клиентские библиотеки .NET Standard для Центров событий:

* [Microsoft.Azure.EventHubs.](/dotnet/api/microsoft.azure.eventhubs) Предоставляет все основные операции среды выполнения.
* [Microsoft.Azure.EventHubs.Processor.](/dotnet/api/microsoft.azure.eventhubs.processor) Расширяет функциональные возможности, позволяя отслеживать обработанные события. Это самый простой способ чтения из концентратора событий.

## <a name="event-hubs-client"></a>Клиент Центров событий

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) является основным объектом, который используется для отправки событий, создания приемников и получения данных среды выполнения. Этот клиент связан с определенным концентратором событий и создает подключение к конечной точке Центров событий.

### <a name="create-an-event-hubs-client"></a>Создание клиента Центров событий

Объект [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) создается из строки подключения. В следующем примере показан самый простой способ создания клиента:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Чтобы изменить строку подключения программными средствами, можно использовать класс [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder) и передать строку подключения в качестве параметра в [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_CreateFromConnectionString_System_String_).

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Отправка событий

Для отправки событий в концентратор событий используйте класс [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata). Текст должен быть массивом `byte` или сегментом массива `byte`.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Получение событий

Для получения событий из службы "Центры событий" рекомендуем использовать узел [Event Processor Host](#event-processor-host-apis), который позволяет автоматически отслеживать смещение и сведения о секциях концентратора событий. Однако, существуют определенные ситуации, в которых для получения событий может потребоваться гибкость основной библиотеки Центров событий.

#### <a name="create-a-receiver"></a>Создание приемника

Приемники связаны с определенными секциями. Поэтому для получения всех событий в концентраторе событий необходимо создать несколько экземпляров. Рекомендуем получать сведения о секциях программными средствами без жесткого программирования идентификаторов секций. Для этого можно использовать метод [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient#Microsoft_Azure_EventHubs_EventHubClient_GetRuntimeInformationAsync).

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Так как события никогда не удаляются из концентратора событий (только истекает их срок действия), правильно укажите начальную точку. В следующем примере показаны возможные комбинации:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Использование события

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>Интерфейсы API узла обработчика событий

Эти API обеспечивают отказоустойчивость рабочих процессов, которые могут стать недоступными, и распределяют секции между всеми имеющимися исполнителями.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Ниже приведен пример реализации интерфейса [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor).

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сценариях Центров событий см. в разделах, ссылки на которые указаны ниже.

* [Что такое Центры событий Azure?](event-hubs-what-is-event-hubs.md)
* [Общие сведения об API Центров событий](event-hubs-api-overview.md)

Ссылки на API-интерфейсы .NET:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)