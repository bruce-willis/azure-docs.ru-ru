---
title: Краткое руководство. Отправка сообщений в Служебную шину Azure и получение сообщений из нее с помощью интерфейса командной строки Azure и Java | Документация Майкрософт
description: В этом кратком руководстве объясняется, как отправлять и получать сообщения через Служебную шину Azure с помощью интерфейса командной строки Azure и примера приложения на Java.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.devlang: java
ms.topic: quickstart
ms.date: 05/22/2018
ms.author: spelluru
ms.openlocfilehash: 279f71496cc408ba991c4335f98eff649ad1b923
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983996"
---
# <a name="quickstart-send-and-receive-messages-using-azure-cli-and-java"></a>Краткое руководство по отправке и получению сообщений с помощью интерфейса командной строки Azure и Java

Служебная шина Microsoft Azure представляет собой брокер интеграции сообщений корпоративного уровня, который гарантирует защищенную и надежную доставку сообщений. Типичный сценарий применения служебной шины подразумевает функциональное разделение двух или более приложений, служб либо процессов, позволяя им обмениваться данными или сведениями о состоянии, а также отправлять друг другу сообщения. При этом приложениям не обязательно подключаться к сети одновременно. 

Например, розничная компания может отправлять данные из точек продаж во вспомогательный офис или региональный центр дистрибуции для пополнения запасов и сбора сведений о товарах. В этом примере клиентское приложение отправляет сообщения в очередь Служебной шины Azure и получает сообщения из нее.

![очередь](./media/service-bus-quickstart-cli/quick-start-queue.png)

В этом кратком руководстве объясняется, как отправлять и принимать сообщения через служебную шину с помощью интерфейса командной строки Azure и библиотеки Java для служебной шины. И наконец, если вас интересует более подробная техническая информация, изучите [это описание](#understand-the-sample-code) ключевых элементов нашего примера кода.

Если у вас еще нет подписки Azure, вы можете создать [бесплатная учетная запись][], прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Вход в Azure

Нажмите кнопку Cloud Shell в меню в правом верхнем углу портала Azure, а затем в раскрывающемся списке **Выбрать окружение** щелкните пункт **Bash**. 

## <a name="use-cli-to-create-resources"></a>Создание ресурсов с помощью интерфейса командной строки

В Cloud Shell введите следующие команды в командной строке Bash, чтобы подготовить ресурсы служебной шины. Не забудьте заменить все заполнители правильными значениями:

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus queue
az servicebus queue create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myQueue

# Get the connection string for the namespace
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

После выполнения последней команды скопируйте строку подключения и имя очереди, а затем вставьте их в любое временное расположение, например в окно Блокнота. Они понадобятся вам на следующем шаге.

## <a name="send-and-receive-messages"></a>Отправка и получение сообщений

Создав пространство имен и очередь и получив необходимые учетные данные, вы можете приступать к отправке и получению сообщений. Этот код можно изучить в [папке с примером на GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/quickstarts-and-tutorials/quickstart-java/src/main/java/samples/quickstart/SendAndReceiveMessages.java).

1. Убедитесь, что открыто окно Cloud Shell и в нем отображается строка Bash.

2. Выполните следующую команду, которая клонирует [репозиторий GitHub для служебной шины](https://github.com/Azure/azure-service-bus/):

   ```bash
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Выберите папку примера в качестве текущего каталога, используя символы косой черты в качестве разделителей пути:

   ```bash
   cd azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java 
   ```

3. Запустите сборку приложения с помощью следующей команды:
   
   ```bash
   mvn clean package -DskipTests
   ```

4. Чтобы запустить программу, введите приведенную ниже команду. Пока вы не перезапустите оболочку Bash, значение переменной со строкой подключения будет автоматически заменяться:

   ```bash
   java -jar ./target/samples.quickstart-1.0.0-jar-with-dependencies.jar -c $connectionString -q myQueue
   ```

6. Проследите, как в очередь отправляются 10 сообщений. Обратите внимание, что сохранение порядка сообщений не гарантируется, но вы можете отслеживать отправку, подтверждение и получение сообщений с полезными данными.

   ![Выходные данные программы](./media/service-bus-quickstart-cli/javaqs.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Выполните следующую команду, чтобы удалить группу ресурсов, пространство имен и все связанные с ними ресурсы:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="understand-the-sample-code"></a>Разбор примера кода

Этот раздел содержит дополнительные сведения о ключевых фрагментах нашего примера кода. Вы можете просмотреть этот код в репозитории GitHub [здесь](https://github.com/Azure/azure-service-bus/blob/master/samples/Java/quickstarts-and-tutorials/quickstart-java/src/main/java/samples/quickstart/SendAndReceiveMessages.java).

### <a name="get-connection-string-and-queue"></a>Получение строки подключения и очереди

Сначала код объявляет две строковые переменные для хранения значений, которые передаются программе в качестве аргументов в командной строке.

```java
String ConnectionString = null;
String QueueName = null;
```

Эти значения добавляются с помощью параметров и назначаются в методе `runApp()`:

```java
public static void main(String[] args) {
    SendAndReceiveMessages app = new SendAndReceiveMessages();
    try {
        app.runApp(args);
        app.run();
    } catch (Exception e) {
        System.out.printf("%s", e.toString());
    }
    System.exit(0);
}

public void runApp(String[] args) {
    try {
        // parse connection string from command line             
        Options options = new Options();
        options.addOption(new Option("c", true, "Connection string"));
        options.addOption(new Option("q", true, "Queue Name"));
        CommandLineParser clp = new DefaultParser();
        CommandLine cl = clp.parse(options, args);
        if (cl.getOptionValue("c") != null && cl.getOptionValue("q") != null) {
            ConnectionString = cl.getOptionValue("c");
            QueueName =  cl.getOptionValue("q");
        }
        else
        {
            HelpFormatter formatter = new HelpFormatter();
            formatter.printHelp("run jar with", "", options, "", true);
        }

    } catch (Exception e) {
        System.out.printf("%s", e.toString());
    }
}
```

### <a name="create-queue-clients-to-send-and-receive"></a>Создание клиентов очереди для отправки и получения

Для отправки и получения сообщений метод `run()` создает экземпляры клиентов очереди, используя значения строки подключения и имени очереди. Этот код создает два клиента очереди: один — для отправки, другой — для получения:

```java
public void run() throws Exception {
// Create a QueueClient instance for receiving using the connection string builder
// We set the receive mode to "PeekLock", meaning the message is delivered
// under a lock and must be acknowledged ("completed") to be removed from the queue
QueueClient receiveClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
this.registerReceiver(receiveClient);

// Create a QueueClient instance for sending and then asynchronously send messages.
QueueClient sendClient = new QueueClient(new ConnectionStringBuilder(ConnectionString, QueueName), ReceiveMode.PEEKLOCK);
```

Метод `run()` также запускает асинхронную операцию отправки сообщений и закрывает средство отправки, когда его работа завершится:

```java
this.sendMessagesAsync(sendClient).thenRunAsync(() -> sendClient.closeAsync());
``` 

### <a name="construct-and-send-messages"></a>Создание и отправка сообщений

Метод `sendMessagesAsync()` создает набор из 10 сообщений и асинхронно отправляет их с помощью клиента очереди:

```java
CompletableFuture<Void> sendMessagesAsync(QueueClient sendClient) {
List<HashMap<String, String>> data =
        GSON.fromJson(
                "[" +
                        "{'name' = 'Einstein', 'firstName' = 'Albert'}," +
                        "{'name' = 'Heisenberg', 'firstName' = 'Werner'}," +
                        "{'name' = 'Curie', 'firstName' = 'Marie'}," +
                        "{'name' = 'Hawking', 'firstName' = 'Steven'}," +
                        "{'name' = 'Newton', 'firstName' = 'Isaac'}," +
                        "{'name' = 'Bohr', 'firstName' = 'Niels'}," +
                        "{'name' = 'Faraday', 'firstName' = 'Michael'}," +
                        "{'name' = 'Galilei', 'firstName' = 'Galileo'}," +
                        "{'name' = 'Kepler', 'firstName' = 'Johannes'}," +
                        "{'name' = 'Kopernikus', 'firstName' = 'Nikolaus'}" +
                        "]",
                new TypeToken<List<HashMap<String, String>>>() {}.getType());

List<CompletableFuture> tasks = new ArrayList<>();
for (int i = 0; i < data.size(); i++) {
    final String messageId = Integer.toString(i);
    Message message = new Message(GSON.toJson(data.get(i), Map.class).getBytes(UTF_8));
    message.setContentType("application/json");
    message.setLabel("Scientist");
    message.setMessageId(messageId);
    message.setTimeToLive(Duration.ofMinutes(2));
    System.out.printf("\nMessage sending: Id = %s", message.getMessageId());
    tasks.add(
            sendClient.sendAsync(message).thenRunAsync(() -> {
                System.out.printf("\n\tMessage acknowledged: Id = %s", message.getMessageId());
            }));
}
return CompletableFuture.allOf(tasks.toArray(new CompletableFuture<?>[tasks.size()]));
```

### <a name="receive-messages"></a>Получение сообщений

Метод `registerReceiver()` регистрирует обратный вызов `RegisterMessageHandler` и настаивает некоторые параметры обработчика сообщений:

```java
void registerReceiver(QueueClient queueClient) throws Exception {
    // register the RegisterMessageHandler callback
    queueClient.registerMessageHandler(new IMessageHandler() {
                           // callback invoked when the message handler loop has obtained a message
                           public CompletableFuture<Void> onMessageAsync(IMessage message) {
                               // receives message is passed to callback
                               if (message.getLabel() != null &&
                                       message.getContentType() != null &&
                                       message.getLabel().contentEquals("Scientist") &&
                                       message.getContentType().contentEquals("application/json")) {
                                    byte[] body = message.getBody();
                                   Map scientist = GSON.fromJson(new String(body, UTF_8), Map.class);

                                   System.out.printf(
                                           "\n\t\t\t\tMessage received: \n\t\t\t\t\t\tMessageId = %s, \n\t\t\t\t\t\tSequenceNumber = %s, \n\t\t\t\t\t\tEnqueuedTimeUtc = %s," +
                                                   "\n\t\t\t\t\t\tExpiresAtUtc = %s, \n\t\t\t\t\t\tContentType = \"%s\",  \n\t\t\t\t\t\tContent: [ firstName = %s, name = %s ]\n",
                                           message.getMessageId(),
                                           message.getSequenceNumber(),
                                           message.getEnqueuedTimeUtc(),
                                           message.getExpiresAtUtc(),
                                           message.getContentType(),
                                           scientist != null ? scientist.get("firstName") : "",
                                           scientist != null ? scientist.get("name") : "");
                               }
                               return CompletableFuture.completedFuture(null);
                           }

                           // callback invoked when the message handler has an exception to report
                           public void notifyException(Throwable throwable, ExceptionPhase exceptionPhase) {
                               System.out.printf(exceptionPhase + "-" + throwable.getMessage());
                           }
                       },
    // 1 concurrent call, messages are auto-completed, auto-renew duration
    new MessageHandlerOptions(1, true, Duration.ofMinutes(1)));

}
```

## <a name="next-steps"></a>Дополнительная информация

С помощью этой статьи вы создали пространство имен служебной шины и другие ресурсы, необходимые для отправки и получения сообщений через очередь. Чтобы лучше изучить код, выполняющий отправку и получение сообщений, см. следующее руководство по служебной шине:

> [!div class="nextstepaction"]
> [Обновление данных о запасах с помощью интерфейса командной строки и Java](./service-bus-tutorial-topics-subscriptions-cli.md)

[бесплатная учетная запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create