---
title: Краткое руководство. Отправка сообщений в Служебную шину Azure и получение сообщений из нее | Документация Майкрософт
description: В этом кратком руководстве описано, как отправлять и получать сообщения Служебной шины Azure с помощью PowerShell и клиента .NET Standard
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/22/2018
ms.author: spelluru
ms.openlocfilehash: 5652069e7a81f54936a41ddb563b49fe6131e7e0
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696842"
---
# <a name="quickstart-send-and-receive-messages-using-azure-powershell-and-net"></a>Краткое руководство. Отправка и получение сообщений Служебной шины Azure с помощью PowerShell и .NET

Служебная шина Microsoft Azure представляет собой брокер интеграции сообщений корпоративного уровня, который гарантирует защищенную и абсолютно надежную доставку сообщений. Типичный сценарий применения служебной шины подразумевает функциональное разделение двух или более приложений, служб либо процессов, которым нужно обмениваться данными или сведениями о состоянии. В таких сценариях часто требуется назначать несколько пакетных заданий для других приложений и служб или запускать обработку заказов. Например, розничная компания может отправлять данные из точек продаж во вспомогательный офис или региональный центр дистрибуции для пополнения запасов и сбора сведений о товарах. В этом сценарии клиентское приложение отправляет сообщения в очередь служебной шины и получает сообщения из нее.

![очередь](./media/service-bus-quickstart-powershell/quick-start-queue.png)

В этом кратком руководстве описано, как отправлять и получать сообщения через очередь служебной шины, создав с помощью PowerShell пространство имен для обмена сообщениями и очередь в этом пространстве имен, а также как получить учетные данные для авторизации в этом пространстве имен. Далее в этой процедуре демонстрируется отправка и получение сообщений через созданную очередь с применением [библиотеки .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

Если у вас еще нет подписки Azure, создайте [бесплатная учетная запись][], прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуются:

- [Visual Studio 2017 с обновлением 3 (версия 15.3, 26730.01)](http://www.visualstudio.com/vs) или более новая версия.
- [Пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows) версии 2.0 или более новой.

Для работы с этим кратким руководством требуется последняя версия Azure PowerShell. Если вам нужно выполнить установку или обновление, см. руководство по [установке и настройке Azure PowerShell][].

## <a name="log-in-to-azure"></a>Вход в Azure

1. Прежде всего установите модуль служебной шины для PowerShell, если у вас его еще нет:

   ```azurepowershell-interactive
   Install-Module AzureRM.ServiceBus
   ```

2. Выполните следующую команду, чтобы войти в Azure:

   ```azurepowershell-interactive
   Login-AzureRmAccount
   ```

3. Введите следующие команды, чтобы настроить текущий контекст подписки или выбрать активную подписку:

   ```azurepowershell-interactive
   Select-AzureRmSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzureRmContext
   ```

## <a name="provision-resources"></a>Подготовка ресурсов

Введите следующие команды в командную строку PowerShell, чтобы подготовить ресурсы служебной шины. Не забудьте заменить все заполнители правильными значениями:

```azurepowershell-interactive
# Create a resource group 
New-AzureRmResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzureRmServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzureRmServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

После выполнения командлета `Get-AzureRmServiceBusKey` скопируйте строку подключения и имя очереди, затем вставьте их в любое временное хранилище данных, например в файл окно Блокнота. Они понадобятся вам на следующем шаге.

## <a name="send-and-receive-messages"></a>Отправка и получение сообщений

Создав пространство имен и очередь и получив необходимые учетные данные, вы можете приступать к отправке и получению сообщений. Этот код можно изучить в [папке с примером на GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Чтобы выполнить этот код, сделайте следующее:

1. Выполните следующую команду, которая клонирует [репозиторий GitHub для служебной шины](https://github.com/Azure/azure-service-bus/):

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. Перейдите к папке `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart` с примерами.

4. Если у вас еще нет строки подключения, получите ее с помощью приведенного ниже командлета PowerShell. Не забудьте заменить `my-resourcegroup` и `namespace-name` фактическими значениями: 

   ```azurepowershell-interactive
   Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5.  В командной строке PowerShell введите следующую команду:

   ```shell
   dotnet build
   ```

6.  Перейдите в папку `bin\Debug\netcoreapp2.0`.

7.  Введите приведенную ниже команду, чтобы запустить программу. Не забудьте ввести вместо `myConnectionString` значение, которое вы получили ранее, а вместо `myQueueName` — имя созданной очереди:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. Проследите, как в очередь отправляются 10 сообщений, а затем поступают из нее:

   ![Выходные данные программы](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Выполните следующую команду, чтобы удалить группу ресурсов, пространство имен и все связанные с ними ресурсы:

```powershell-interactive
Remove-AzureRmResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Разбор примера кода

Этот раздел содержит дополнительные сведения о работе этого примера кода. 

### <a name="get-connection-string-and-queue"></a>Получение строки подключения и очереди

Строка подключения и имя очереди передаются в метод `Main()` в качестве аргументов командной строки. `Main()` объявляет две строковые переменные для хранения этих значений:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Затем метод `Main()` запускает асинхронный цикл обработки сообщений `MainAsync()`.

### <a name="message-loop"></a>Цикл обработки сообщений

Метод MainAsync() создает клиент очереди, используя аргументы командной строки, затем вызывает обработчик получения сообщений с именем `RegisterOnMessageHandlerAndReceiveMessages()` и отправляет набор сообщений:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

Метод `RegisterOnMessageHandlerAndReceiveMessages()` просто настраивает несколько параметров для обработчика событий и вызывает метод `RegisterMessageHandler()` клиента, который начинает прием сообщений:

```csharp
static void RegisterOnMessageHandlerAndReceiveMessages()
{
    // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
    var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
    {
        // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
        // Set it according to how many messages the application wants to process in parallel.
        MaxConcurrentCalls = 1,

        // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
        // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
        AutoComplete = false
    };

    // Register the function that will process messages
    queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
} 
```

### <a name="send-messages"></a>Отправка сообщений

Операции создания и отправки сообщений выполняются в методе `SendMessagesAsync()`:

```csharp
static async Task SendMessagesAsync(int numberOfMessagesToSend)
{
    try
    {
        for (var i = 0; i < numberOfMessagesToSend; i++)
        {
            // Create a new message to send to the queue
            string messageBody = $"Message {i}";
            var message = new Message(Encoding.UTF8.GetBytes(messageBody));

            // Write the body of the message to the console
            Console.WriteLine($"Sending message: {messageBody}");

            // Send the message to the queue
            await queueClient.SendAsync(message);
        }
    }
    catch (Exception exception)
    {
        Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
    }
}
```

### <a name="process-messages"></a>Обработка сообщений

Метод `ProcessMessagesAsync()` обнаруживает новые сообщения, обрабатывает их и завершает их прием:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этой статьи вы создали пространство имен служебной шины и другие ресурсы, необходимые для отправки и получения сообщений через очередь. Чтобы лучше изучить код, выполняющий отправку и получение сообщений, см. следующее руководство по служебной шине:

> [!div class="nextstepaction"]
> [Update inventory using Azure PowerShell](./service-bus-tutorial-topics-subscriptions-powershell.md) (Обновление информации о запасах с помощью Azure PowerShell)

[бесплатная учетная запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[установке и настройке Azure PowerShell]: /powershell/azure/install-azurerm-ps