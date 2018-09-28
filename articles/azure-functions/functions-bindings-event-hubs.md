---
title: Привязки Центров событий Azure для службы "Функции Azure"
description: Узнайте, как использовать привязки Центров событий Azure в Функциях Azure.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.assetid: daf81798-7acc-419a-bc32-b5a41c6db56b
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/08/2017
ms.author: glenga
ms.openlocfilehash: 2c78e1d39227153dd65f145512fab4769b09e5c0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966577"
---
# <a name="azure-event-hubs-bindings-for-azure-functions"></a>Привязки Центров событий Azure для службы "Функции Azure"

Здесь объясняется, как работать с привязками [Центров событий Azure](../event-hubs/event-hubs-what-is-event-hubs.md) для службы "Функции Azure". Функции Azure поддерживают привязки триггера и выходные привязки для Центров событий Azure.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-1x"></a>Пакеты – Функции 1.x

Привязки Центров событий службы "Функции Azure" версии 1.x доступны в пакете NuGet [Microsoft.Azure.WebJobs.ServiceBus](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus) версии 2.x.
Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs).


[!INCLUDE [functions-package](../../includes/functions-package.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

В службе "Функции" версии 2.x используйте пакет [Microsoft.Azure.WebJobs.Extensions.EventHubs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventHubs) версии 3.x.
Исходный код для пакета находится в репозитории GitHub [azure-webjobs-sdk](https://github.com/Azure/azure-webjobs-sdk/tree/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs).

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="trigger"></a>Триггер

Используйте триггер Центров событий Azure для ответа на событие, отправленное в поток событий концентратора событий. Чтобы настроить триггер, необходимо иметь доступ для чтения к концентратору событий.

При активации функции триггера Центров событий сообщение, вызвавшее активацию, передается в функцию в качестве строки.

## <a name="trigger---scaling"></a>Триггер: масштабирование

Каждый экземпляр функции триггера концентратора событий связан только с одним экземпляром [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Центры событий гарантируют, что определенный раздел может использоваться только одним экземпляром [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor).

Например, рассмотрим концентратор событий:

* 10 разделов;
* 1000 событий, равномерно распределенных между всеми разделами, со 100 сообщениями в каждом разделе.

При первом включении у функции будет только 1 экземпляр. Назовем его `Function_0`. В `Function_0` есть один экземпляр [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor), который арендует все десять разделов. Этот экземпляр начнет считывать события из разделов 0–9. При этом возможно следующее:

* **Новые экземпляры функции не требуются**. Экземпляр `Function_0` может обработать все 1000 событий, прежде чем запустится логика масштабирования службы "Функции". В этом случае все 1000 сообщений будут обрабатываться экземпляром `Function_0`.

* **Добавлен дополнительный экземпляр функции**. Логика масштабирования службы "Функции" определяет, что `Function_0` содержит больше сообщений, чем может обработать. В этом случае создается новый экземпляр приложения-функции (`Function_1`), а также новый экземпляр [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor). Центры событий определяют, что новый экземпляр узла пытается читать сообщения. Центры событий балансируют нагрузку разделов в экземплярах узла. Например, разделы 0–4 могут быть назначены `Function_0`, а разделы 5–9 — `Function_1`. 

* **Добавлено еще несколько (N) экземпляров функции**. Логика масштабирования службы "Функции" определяет, что у экземпляров функции `Function_0` и `Function_1` сообщений больше, чем они могут обработать. Создаются новые экземпляры приложения-функции `Function_2`...`Functions_N`. `N` превышает число разделов концентратора событий. В нашем примере Центры событий снова распределяют нагрузку разделов, в этом случае — по экземплярам `Function_0`...`Functions_9`. 

Обратите внимание, что когда служба "Функции" развертывается до `N` экземпляров, которых больше, чем разделов концентратора событий, это делается для того, чтобы экземпляры [EventProcessorHost](https://docs.microsoft.com/dotnet/api/microsoft.azure.eventhubs.processor) всегда были доступны для получения блокировок разделов, по мере того как они становятся доступными из других экземпляров. Вы платите только за ресурсы, используемые при выполнении экземпляров функции. Плата за подготовку избыточных экземпляров не взимается.

Когда функция выполнена (с ошибками или без), в связанную учетную запись хранения добавляются контрольные точки. После этого все 1000 сообщений больше не будут извлекаться.

## <a name="trigger---example"></a>Пример триггера

Языковой пример см. в разделах:

* [C#](#trigger---c-example)
* [Скрипт C# (CSX)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)
* [Java](#trigger---java-example)

### <a name="trigger---c-example"></a>Пример C# в триггере

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая записывает в журнал текст сообщений триггера концентратора событий.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Чтобы получить доступ к [метаданным события](#trigger---event-metadata) в коде функции, создайте привязку к объекту [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (требуется инструкция using для `Microsoft.ServiceBus.Messaging`). Вы также можете получить доступ к тем же свойствам, используя выражения привязки в сигнатуре метода.  В следующем примере показаны оба способа получения одних и тех же данных:

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run(
    [EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] EventData myEventHubMessage, 
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Для пакетного получения событий создайте массив `string` или `EventData`.

```cs
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---c-script-example"></a>Пример скрипта C# в триггере

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Эта функция записывает в журнал текст сообщений триггера концентратора событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. В первом примере приведены данные для службы "Функции" версии 2.x, а во втором — для версии 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже приведен код скрипта C#.

```cs
using System;

public static void Run(string myEventHubMessage, TraceWriter log)
{
    log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
}
```

Чтобы получить доступ к [метаданным события](#trigger---event-metadata) в коде функции, создайте привязку к объекту [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata) (требуется инструкция using для `Microsoft.ServiceBus.Messaging`). Вы также можете получить доступ к тем же свойствам, используя выражения привязки в сигнатуре метода.  В следующем примере показаны оба способа получения одних и тех же данных:

```cs
#r "Microsoft.ServiceBus"
using System.Text;
using System;
using Microsoft.ServiceBus.Messaging;

public static void Run(EventData myEventHubMessage,
    DateTime enqueuedTimeUtc, 
    Int64 sequenceNumber,
    string offset,
    TraceWriter log)
{
    log.Info($"Event: {Encoding.UTF8.GetString(myEventHubMessage.GetBytes())}");
    // Metadata accessed by binding to EventData
    log.Info($"EnqueuedTimeUtc={myEventHubMessage.EnqueuedTimeUtc}");
    log.Info($"SequenceNumber={myEventHubMessage.SequenceNumber}");
    log.Info($"Offset={myEventHubMessage.Offset}");
    // Metadata accessed by using binding expressions
    log.Info($"EnqueuedTimeUtc={enqueuedTimeUtc}");
    log.Info($"SequenceNumber={sequenceNumber}");
    log.Info($"Offset={offset}");
}
```

Для пакетного получения событий создайте массив `string` или `EventData`.

```cs
public static void Run(string[] eventHubMessages, TraceWriter log)
{
    foreach (var message in eventHubMessages)
    {
        log.Info($"C# Event Hub trigger function processed a message: {message}");
    }
}
```

### <a name="trigger---f-example"></a>Пример F# в триггере

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Эта функция записывает в журнал текст сообщений триггера концентратора событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. В первом примере приведены данные для службы "Функции" версии 2.x, а во втором — для версии 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже показан код F#.

```fsharp
let Run(myEventHubMessage: string, log: TraceWriter) =
    log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)
```

### <a name="trigger---javascript-example"></a>Пример JavaScript в триггере

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Функция считывает [метаданные события](#trigger---event-metadata) и записывает сообщение в журнал.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. В первом примере приведены данные для службы "Функции" версии 2.x, а во втором — для версии 1.x. 


```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "myEventHubMessage",
  "direction": "in",
  "path": "MyEventHub",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, eventHubMessage) {
    context.log('Event Hubs trigger function processed message: ', myEventHubMessage);
    context.log('EnqueuedTimeUtc =', context.bindingData.enqueuedTimeUtc);
    context.log('SequenceNumber =', context.bindingData.sequenceNumber);
    context.log('Offset =', context.bindingData.offset);
     
    context.done();
};
```

Чтобы получить события в пакете, задайте для параметра `cardinality` в файле *function.json* значение `many`, как показано в приведенных ниже примерах. В первом примере приведены данные для службы "Функции" версии 2.x, а во втором — для версии 1.x. 

```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "eventHubName": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```
```json
{
  "type": "eventHubTrigger",
  "name": "eventHubMessages",
  "direction": "in",
  "path": "MyEventHub",
  "cardinality": "many",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, eventHubMessages) {
    context.log(`JavaScript eventhub trigger function called for message array ${eventHubMessages}`);
    
    eventHubMessages.forEach((message, index) => {
        context.log(`Processed message ${message}`);
        context.log(`EnqueuedTimeUtc = ${context.bindingData.enqueuedTimeUtcArray[index]}`);
        context.log(`SequenceNumber = ${context.bindingData.sequenceNumberArray[index]}`);
        context.log(`Offset = ${context.bindingData.offsetArray[index]}`);
    });

    context.done();
};
```

### <a name="trigger---java-example"></a>Пример Java в триггере

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция Java](functions-reference-java.md), которая использует эту привязку. Эта функция записывает в журнал текст сообщений триггера концентратора событий.

```json
{
  "type": "eventHubTrigger",
  "name": "msg",
  "direction": "in",
  "eventHubName": "myeventhubname",
  "connection": "myEventHubReadConnectionAppSetting"
}
```

```java
@FunctionName("ehprocessor")
public void eventHubProcessor(
  @EventHubTrigger(name = "msg",
                  eventHubName = "myeventhubname",
                  connection = "myconnvarname") String message,
       final ExecutionContext context ) 
       {
          context.getLogger().info(message);
 }
 ```

 В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `EventHubTrigger` для параметров, значения которых будут поступать из концентратора событий. Параметры с этими заметками запускают функцию, когда происходит событие.  Эта заметка может использоваться с собственными типами Java, объектами POJO или значениями nullable, которые необязательно использовать<T>. 

## <a name="trigger---attributes"></a>Атрибуты триггера

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [EventHubTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubTriggerAttribute.cs).

Конструктор атрибута принимает имя концентратора событий, имя группы потребителей и имя параметра приложения, содержащего строку подключения. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#trigger---configuration). Ниже приведен пример атрибута `EventHubTriggerAttribute`.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static void Run([EventHubTrigger("samples-workitems", Connection = "EventHubConnectionAppSetting")] string myEventHubMessage, TraceWriter log)
{
    ...
}
```

Полный пример см. в разделе [Пример C# в триггере](#trigger---c-example).

## <a name="trigger---configuration"></a>Конфигурация триггера

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `EventHubTrigger`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Нужно задать значение `eventHubTrigger`. Это свойство задается автоматически при создании триггера на портале Azure.|
|**direction** | Недоступно | Нужно задать значение `in`. Это свойство задается автоматически при создании триггера на портале Azure. |
|**name** | Недоступно | Имя переменной, представляющей элемент события в коде функции. | 
|**path** |**EventHubName** | Только служба "Функции" версии 1.x. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. | 
|**eventHubName** |**EventHubName** | Только служба "Функции" версии 2.x. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**consumerGroup** |**ConsumerGroup** | Необязательное свойство, которое используется для задания [группы потребителей](../event-hubs/event-hubs-features.md#event-consumers), используемой для подписки на события в концентраторе. Если аргумент опущен, используется группа потребителей `$Default`. | 
|**кратность** | Недоступно | Для JavaScript. Задайте значение `many`, чтобы включить пакетную обработку.  Если опустить это значение или задать `one`, функции будет передано одно сообщение. | 
|**подключение** |**Connection** | Имя параметра приложения, содержащего строку подключения к пространству имен концентратора событий. Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для [пространства имен](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace), а не сам концентратор событий. Для активации триггера эта строка подключения должна обладать, по крайней мере, правами на чтение.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="trigger---event-metadata"></a>Триггер — метаданные события

Триггер Центров событий предоставляет несколько [свойств метаданных](functions-triggers-bindings.md#binding-expressions---trigger-metadata). Эти свойства можно использовать как часть выражений привязки в других привязках или как параметры в коде. Эти свойства относятся к классу [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata).

|Свойство|type|ОПИСАНИЕ|
|--------|----|-----------|
|`PartitionContext`|[PartitionContext](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.partitioncontext)|Экземпляр `PartitionContext`.|
|`EnqueuedTimeUtc`|`DateTime`|Время попадания в очередь в формате UTC.|
|`Offset`|`string`|Смещение данных относительно потока разделов концентратора событий. Смещение — это маркер или идентификатор события в потоке Центров событий. Этот идентификатор уникален внутри раздела потока Центров событий.|
|`PartitionKey`|`string`|Раздел, в который следует отправлять данные события.|
|`Properties`|`IDictionary<String,Object>`|Свойства пользователя данных события.|
|`SequenceNumber`|`Int64`|Регистрационный номер транзакции события в журнале.|
|`SystemProperties`|`IDictionary<String,Object>`|Свойства системы, включая данные события.|

См. [примеры кода](#trigger---example), в которых используются эти свойства, в предыдущих разделах этой статьи.

## <a name="trigger---hostjson-properties"></a>Свойства host.json в триггере

В файле [host.json](functions-host-json.md#eventhub) содержатся параметры, управляющие реакцией триггера Центров событий на событие.

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="output"></a>Выходные данные

Используйте выходную привязку Центров событий для записи событий в поток событий. Чтобы записывать события в концентратор событий, необходимо иметь разрешение на оправку в него событий.

Убедитесь в наличии ссылок на требуемые пакеты: [Функции 1.x](#packages---functions-1.x) или [Функции 2.x](#packages---functions-2.x) 

## <a name="output---example"></a>Пример выходных данных

Языковой пример см. в разделах:

* [C#](#output---c-example)
* [Скрипт C# (CSX)](#output---c-script-example)
* [F#](#output---f-example)
* [JavaScript](#output---javascript-example)
* [Java](#output---java-example)

### <a name="output---c-example"></a>Пример выходных данных C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), которая записывает сообщение в концентратор событий, используя возвращаемое значение метода в качестве выходных данных.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

### <a name="output---c-script-example"></a>Пример выходных данных скрипта C#

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция сценария C#](functions-reference-csharp.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. В первом примере приведены данные для службы "Функции" версии 2.x, а во втором — для версии 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ниже приведен код сценария C#, который создает одно сообщение.

```cs
using System;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.Verbose(msg);   
    outputEventHubMessage = msg;
}
```

Ниже приведен код сценария C#, который создает несколько сообщений.

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, TraceWriter log)
{
    string message = $"Event Hub message created at: {DateTime.Now}";
    log.Info(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

### <a name="output---f-example"></a>Пример выходных данных F#

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция F#](functions-reference-fsharp.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. В первом примере приведены данные для службы "Функции" версии 2.x, а во втором — для версии 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ниже показан код F#.

```fsharp
let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
    let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
    log.Verbose(msg);
    outputEventHubMessage <- msg;
```

### <a name="output---javascript-example"></a>Пример выходных данных JavaScript

В следующем примере показаны привязка триггера концентратора событий в файле *function.json* и [функция JavaScript](functions-reference-node.md), которая использует эту привязку. Эта функция записывает сообщение в концентратор событий.

В приведенных ниже примерах показаны данные привязки Центров событий в файле *function.json*. В первом примере приведены данные для службы "Функции" версии 2.x, а во втором — для версии 1.x. 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```
```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

Ниже приведен код JavaScript, который отправляет отдельное сообщение.

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Event Hub message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Event Hub message created at: " + timeStamp;
    context.done();
};
```

Ниже приведен код JavaScript, который отправляет несколько сообщений.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Event Hub message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

### <a name="output---java-example"></a>Пример выходных данных Java

В следующем примере показана функция Java, которая записывает в концентратор событий сообщение, содержащее текущее время.

```java
@}FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 *&#47;5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
 ```

В [библиотеке среды выполнения функций Java](/java/api/overview/azure/functions/runtime) используйте заметку `@EventHubOutput` для параметров, значения которых будут опубликованы в концентраторе событий.  Параметр должен быть типа `OutputBinding<T>`, где T — POJO или любой собственный тип Java. 

## <a name="output---attributes"></a>Выходные атрибуты

В [библиотеках классов C#](functions-dotnet-class-library.md) используйте атрибут [EventHubAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs).

Конструктор атрибута принимает имя концентратора событий и имя параметра приложения, содержащего строку подключения. Дополнительные сведения об этих параметрах см. в разделе [Привязки концентраторов событий функций Azure](#output---configuration). Ниже приведен пример атрибута `EventHub`.

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, TraceWriter log)
{
    ...
}
```

Полный пример см. в разделе [Пример выходных данных C#](#output---c-example).

## <a name="output---configuration"></a>Выходная конфигурация

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `EventHub`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type** | Недоступно | Для этого свойства необходимо задать значение "eventHub" |
|**direction** | Недоступно | Для этого свойства необходимо задать значение out. Этот параметр задается автоматически при создании привязки на портале Azure. |
|**name** | Недоступно | Имя переменной, используемое в коде функции, которая представляет событие. | 
|**path** |**EventHubName** | Только служба "Функции" версии 1.x. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. | 
|**eventHubName** |**EventHubName** | Только служба "Функции" версии 2.x. Имя концентратора событий. Если имя концентратора событий указано также в строке подключения, такое значение переопределяет это свойство во время выполнения. |
|**подключение** |**Connection** | Имя параметра приложения, содержащего строку подключения к пространству имен концентратора событий. Скопируйте эту строку подключения, нажав кнопку **Сведения о подключении** для *пространства имен*, а не сам концентратор событий. Чтобы отправлять сообщения в поток событий, эта строка подключения должна иметь разрешения на отправку.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="output---usage"></a>Использование выходной привязки

В коде C# и сценарии C# для отправки сообщений используйте параметр метода, например `out string paramName`. В скрипте C# `paramName` — это значение, заданное в свойстве `name` файла *function.json*. Для записи нескольких сообщений можно использовать `ICollector<string>` или `IAsyncCollector<string>` вместо `out string`.

В JavaScript для доступа к выходному событию можно использовать `context.bindings.<name>`. `<name>` — это значение, заданное в свойстве `name` файла *function.json*.

## <a name="exceptions-and-return-codes"></a>Исключения и коды возврата

| Привязка | Справочные материалы |
|---|---|
| Концентратор событий | [Руководство по операциям](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)
