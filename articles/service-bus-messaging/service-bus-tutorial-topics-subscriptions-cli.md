---
title: Руководство. Обновление ассортимента товаров в магазинах розничной торговли с помощью каналов публикации (подписки) и фильтра разделов через Azure CLI | Документация Майкрософт
description: Из этого руководства вы узнаете, как отправлять и получать сообщения из раздела и подписки, а также как добавлять и использовать правила фильтрации с помощью Azure CLI
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.author: sethm
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 81d538c0324f8fa89a7ce86ceaf2b0a2a76b4d51
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43120690"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Руководство. Обновление информации о запасах с помощью интерфейса командной строки, разделов и подписок

Служебная шина Microsoft Azure — это мультитенантная облачная служба для обмена сообщениями, которая позволяет пересылать информацию между приложениями и службами. Асинхронная работа обеспечивает гибкий обмен сообщениями через брокер и обработку сообщений в порядке поступления, а также возможность публикации и подписки. В этом руководстве объясняется, как использовать подписки и темы Служебной шины Azure для обновления списка розничных товаров через каналы публикации (подписки) с помощью Azure CLI и Java.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание раздела служебной шины и одной или нескольких подписок на этот раздел с помощью Azure CLI;
> * добавление фильтров раздела с помощью Azure CLI;
> * создание двух сообщений с разным содержимым;
> * отправка сообщений и проверка их поступления в ожидаемые подписки;
> * получение сообщений из подписок.

Такой сценарий, например, отлично подходит для обновления сведений об ассортименте товаров в нескольких магазинах розничной торговли. В этом сценарии каждый магазин или группа магазинов получают сообщения, позволяющие им обновить ассортимент. В этом руководстве показано, как реализовать этот сценарий с помощью подписок и фильтров. Сначала вы создадите раздел с тремя подписками, добавите в него правила и фильтры, а затем отправите и получите сообщения через разделы и подписки.

![Раздел](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Если у вас еще нет подписки Azure, вы можете создать [бесплатная учетная запись][], прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для разработки приложения Служебной шины с помощью Java должны быть установлены следующие компоненты:

- [пакет средств разработки Java](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) последней версии;
- [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org) 3.0 или более поздней версии.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Разделы и подписки служебной шины

Каждая [подписка на раздел](service-bus-messaging-overview.md#topics) может получать копию любого сообщения. Разделы полностью совместимы с очередями служебной шины на уровнях протоколов и семантики. Разделы служебной шины поддерживают широкий набор правил отбора и условий фильтра, а также позволяют создать действия для присвоения или изменения свойств сообщения. Каждый раз, когда срабатывает правило, создается новое сообщение. Чтобы узнать больше о правилах, фильтрах и действиях, перейдите по [этой ссылке](topic-filters.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

После установки CLI откройте командную строку и выполните следующие команды, чтобы войти в Azure: Эти действия не нужно выполнять, если вы используете Cloud Shell:

1. Если же вы используете Azure CLI локально, для входа в Azure выполните следующую команду: Действие входа не требуется, если вы выполняете эти команды в Cloud Shell:

   ```azurecli-interactive
   az login
   ```

2. В качестве текущего контекста укажите подписку Azure, которую вы хотите использовать:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Использование CLI для подготовки ресурсов

Введите следующие команды, чтобы подготовить ресурсы служебной шины. Не забудьте заменить все заполнители правильными значениями:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

После выполнения последней команды скопируйте строку подключения и имя очереди, а затем вставьте их в любое временное расположение, например в окно Блокнота. Они понадобятся вам на следующем шаге.

## <a name="create-filter-rules-on-subscriptions"></a>Создание правил фильтрации по подпискам

Итак, вы уже подготовили пространство имен, разделы и подписки, также у вас есть необходимые учетные данные, а значит вы готовы создать правила фильтрации для этих подписок, чтобы отправлять и получать сообщения. Этот код можно изучить в [папке с примером на GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/quickstarts-and-tutorials/tutorial-topics-subscriptions-filters-java/src/main/java/com/microsoft/azure/).

## <a name="send-and-receive-messages"></a>Отправка и получение сообщений

1. Убедитесь, что открыто окно Cloud Shell и в нем отображается строка Bash.

2. Выполните следующую команду, которая клонирует [репозиторий GitHub для Служебной шины](https://github.com/Azure/azure-service-bus/):

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Перейдите к папке `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java` с примерами. Обратите внимание, что для команд в оболочке Bash учитывается регистр символов и в качестве разделителей пути допустима только косая черта.

3. Запустите сборку приложения с помощью следующей команды:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Чтобы запустить программу, введите приведенную ниже команду. Не забудьте вместо заполнителей указать строку подключения и имя раздела, которые вы получили на предыдущем шаге:

   ```shell
  java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Проследите, как происходит отправка 10 сообщений в раздел и их получение из отдельных подписок:

   ![Выходные данные программы](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Выполните следующую команду, чтобы удалить группу ресурсов, пространство имен и все связанные с ними ресурсы:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Разбор примера кода

Этот раздел содержит дополнительные сведения о работе этого примера кода.

### <a name="get-connection-string-and-queue"></a>Получение строки подключения и очереди

Прежде всего в этом коде объявляется набор переменных, которые управляют выполнением остальных частей программы:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";    
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

В качестве параметров командной строки принимаются только строка подключения и имя раздела, которые передаются в `main()`. В методе `run()` активируется фактическое выполнение кода, который отправляет сообщения в раздел и получает их:

```java
public static void main(String[] args) {
        TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
        // Send sample messages.
        this.sendMessagesToTopic();

        // Receive messages from subscriptions.
        this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Создание клиента раздела для отправки сообщений

Чтобы отправлять и получать сообщения, метод `sendMessagesToTopic()` создает экземпляр клиента раздела, который использует строку подключения и имя раздела, а затем вызывает другой метод для отправки сообщений:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
         // Create client for the topic.
        TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

        // Create a message sender from the topic client.

        System.out.printf("\nSending orders to topic.\n");

        // Now we can start sending orders.
        CompletableFuture.allOf(
                SendOrders(topicClient,Store[0]),
                SendOrders(topicClient,Store[1]),
                SendOrders(topicClient,Store[2]),
                SendOrders(topicClient,Store[3]),
                SendOrders(topicClient,Store[4]),
                SendOrders(topicClient,Store[5]),
                SendOrders(topicClient,Store[6]),
                SendOrders(topicClient,Store[7]),
                SendOrders(topicClient,Store[8]),
                SendOrders(topicClient,Store[9])                
        ).join();

        System.out.printf("\nAll messages sent.\n");
    }

     public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));         
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8)); 
            // We always set the Sent to field
            message.setTo(store);    
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both. 
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});
                        
            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());            
            topicClient.sendAsync(message);
        }
               
        return new CompletableFuture().completedFuture(null);         
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Получение сообщений из отдельных подписок

Метод `receiveAllMessages()` вызывает метод `receiveAllMessageFromSubscription()`, который создает отдельного клиента подписки для каждого вызова и получает сообщения из отдельных подписок:

```java
public void receiveAllMessages() throws Exception {     
    System.out.printf("\nStart Receiving Messages.\n");
    
    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2]) 
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {
        
        int receivedMessages = 0;

        // Create subscription client.
        IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

        // Create a receiver from the subscription client and receive all messages.
        System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

        while (true)
        {
            // This will make the connection wait for N seconds if new messages are available. 
            // If no additional messages come we close the connection. This can also be used to realize long polling.
            // In case of long polling you would obviously set it more to e.g. 60 seconds.
            IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
            if (receivedMessage != null)
            {
                if ( receivedMessage.getProperties() != null ) {                                                                                
                    System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));                                                                                          
                    
                    // Show the label modified by the rule action
                    if(receivedMessage.getLabel() != null)
                        System.out.printf("Label=%s\n", receivedMessage.getLabel());   
                }
                
                byte[] body = receivedMessage.getBody();
                Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
                System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());                          
                
                subscriptionClient.complete(receivedMessage.getLockToken());
                receivedMessages++;
            }
            else
            {
                // No more messages to receive.
                subscriptionClient.close();
                break;
            }
        }
        System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
        
        return new CompletableFuture().completedFuture(null);
}
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим руководством вы подготовили ресурсы с помощью Azure CLI, а затем отправили и получили сообщения через раздел Служебной шины и подписки на него. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * создание раздела служебной шины и одной или нескольких подписок на этот раздел с помощью портала Azure;
> * добавление фильтров раздела в коде .NET;
> * создание двух сообщений с разным содержимым;
> * отправка сообщений и проверка их поступления в ожидаемые подписки;
> * получение сообщений из подписок.

Чтобы изучить дополнительные примеры отправки и получения сообщений, перейдите к [описанию примеров для служебной шины на GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Следующее руководство содержит дополнительные сведения об использовании возможностей публикации и подписки в cлужебной шине Azure.

> [!div class="nextstepaction"]
> [Обновление информации о запасах с помощью PowerShell, разделов и подписок](service-bus-tutorial-topics-subscriptions-portal.md)

[бесплатная учетная запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create