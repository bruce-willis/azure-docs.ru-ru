---
title: Обзор интерфейсов API концентраторов событий Azure для платформы .NET Framework | Документация Майкрософт
description: Сводные сведения о некоторых ключевых клиентских API концентраторов событий для .NET Framework.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 06/13/2018
ms.author: shvija
ms.openlocfilehash: dd915671c8b7d51595fea520187b917005f5dc53
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007561"
---
# <a name="event-hubs-net-framework-api-overview"></a>Обзор API концентраторов событий для платформы .NET Framework

В этой статье перечислены некоторые ключевые [клиентские API Центров событий Azure для .NET Framework](https://www.nuget.org/packages/WindowsAzure.ServiceBus/). Существуют две категории: API управления и API среды выполнения. API среды выполнения состоят из всех операций, необходимых для отправки и получения сообщения. Операции управления позволяют управлять состоянием сущности концентраторов событий путем создания, обновления и удаления сущностей.

[Сценарии мониторинга](event-hubs-metrics-azure-monitor.md) распространяются как на управление, так и на среду выполнения. Подробную справочную документацию по API .NET см. в руководстве по [.NET Framework](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.NET Standard](/dotnet/api/microsoft.azure.eventhubs) и [API EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor).

## <a name="management-apis"></a>API управления

Для выполнения указанных ниже операций управления требуется разрешение на **управление** пространством имен концентраторов событий.

### <a name="create"></a>Создание

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Блокировка изменений

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Delete

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>Интерфейсы API среды выполнения
### <a name="create-publisher"></a>Создание издателя

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Публикация сообщения

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Создание потребителя

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Использование сообщения

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Интерфейсы API узла обработчика событий

Эти API обеспечивают отказоустойчивость рабочих процессов, которые могут стать недоступными, и распределяют секции между всеми имеющимися исполнителями.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

Интерфейс [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor) определяется следующим образом.

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сценариях концентраторов событий см. в разделах, ссылки на которые указаны ниже.

* [Что такое концентраторы событий Azure?](event-hubs-what-is-event-hubs.md)
* [Руководство по программированию концентраторов событий](event-hubs-programming-guide.md)

Ссылки на API-интерфейсы .NET:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.EventHubs.EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
