---
title: Руководство. Обновление ассортимента товаров в магазинах розничной торговли с помощью каналов публикации (подписки) и фильтра разделов на портале Azure | Документация Майкрософт
description: Из этого руководства вы узнаете, как отправлять и получать сообщения из раздела и подписки, а также как добавлять и использовать правила фильтрации с помощью .NET
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: 357bdcbbee348d8cb89e2d75060a3e7ba05e2c86
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47406240"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Руководство. Обновление информации о запасах с помощью портала Azure, разделов и подписок

Служебная шина Microsoft Azure — это мультитенантная облачная служба для обмена сообщениями, которая позволяет пересылать информацию между приложениями и службами. Асинхронная работа обеспечивает гибкий обмен сообщениями через брокер и обработку сообщений в порядке поступления, а также возможность публикации и подписки. В этом руководстве объясняется, как использовать подписки и темы Служебной шины Azure для обновления списка розничных товаров через каналы публикации (подписки) с помощью портала Azure и .NET.

Из этого руководства вы узнаете, как выполнять следующие задачи:
> [!div class="checklist"]
> * создание раздела служебной шины и одной или нескольких подписок на этот раздел с помощью портала Azure;
> * добавление фильтров раздела в коде .NET;
> * создание двух сообщений с разным содержимым;
> * отправка сообщений и проверка их поступления в ожидаемые подписки;
> * получение сообщений из подписок.

Такой сценарий, например, отлично подходит для обновления сведений об ассортименте товаров в нескольких магазинах розничной торговли. В этом сценарии каждый магазин или группа магазинов получают сообщения, позволяющие им обновить ассортимент. В этом руководстве показано, как реализовать этот сценарий с помощью подписок и фильтров. Сначала вы создадите раздел с тремя подписками, добавите в него правила и фильтры, а затем отправите и получите сообщения через разделы и подписки.

![Раздел](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Если у вас еще нет подписки Azure, вы можете создать [бесплатная учетная запись][], прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством вам потребуются:

- [Visual Studio 2017 с обновлением 3 (версия 15.3, 26730.01)](http://www.visualstudio.com/vs) или более новая версия.
- [Пакет SDK для .NET Core](https://www.microsoft.com/net/download/windows) версии 2.0 или более новой.

## <a name="service-bus-topics-and-subscriptions"></a>Разделы и подписки служебной шины

Каждая [подписка на раздел](service-bus-messaging-overview.md#topics) может получать копию любого сообщения. Разделы полностью совместимы с очередями служебной шины на уровнях протоколов и семантики. Разделы служебной шины поддерживают широкий набор правил отбора и условий фильтра, а также позволяют создать действия для присвоения или изменения свойств сообщения. Каждый раз, когда срабатывает правило, создается новое сообщение. Чтобы узнать больше о правилах, фильтрах и действиях, перейдите по [этой ссылке](topic-filters.md).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Сначала откройте [портал Azure][Azure portal] и выполните вход, используя подписку Azure. Здесь вам в первую очередь нужно создать пространство имен служебной шины с типом **обмен сообщениями**.

## <a name="create-a-service-bus-namespace"></a>Создание пространства имен служебной шины

Пространство имен служебной шины для обмена сообщениями предоставляет уникальный контейнер, ограничивающий область действия. Вы можете обращаться к этому контейнеру по [полному доменному имени][] и создавать в нем очереди, разделы и подписки. В следующем примере создается пространство имен служебной шины для обмена сообщениями в новой или существующей [группе ресурсов](/azure/azure-resource-manager/resource-group-portal):

1. На портале в области навигации слева щелкните **+ Создать ресурс**, **Интеграция Enterprise** и **Служебная шина**.
2. В диалоговом окне **Создание пространства имен** укажите имя пространства имен. Система немедленно проверяет, доступно ли оно.
3. Убедившись, что пространство имен доступно, выберите ценовую категорию: "Стандартный" или "Премиум".
4. Выберите **подписку** Azure, в рамках которой будет создано пространство имен.
5. Выберите существующую **группу ресурсов** , в которую будет включено это пространство имен, или создайте новую.      
6. Укажите **расположение**— страну или регион для размещения пространства имен.
7. Нажмите кнопку **Создать**. Теперь система создает пространство имен и включает его. Возможно, вам придется подождать несколько минут, пока система выделит ресурсы для вашей учетной записи.

  ![пространство_имен](./media/service-bus-tutorial-topics-subscriptions-portal/create-namespace.png)

### <a name="obtain-the-management-credentials"></a>Получение учетных данных управления

При создании нового пространства имен автоматически создается начальное правило подписанного URL-адреса (SAS) и связанная с ним пара первичного и вторичного ключей, каждый из которых предоставляет полный контроль над всеми аспектами пространства имен. Чтобы скопировать начальное правило, выполните следующие действия.

1. Щелкните **Все ресурсы**, а затем щелкните созданное имя пространства имен.
2. В окне пространства имен щелкните **Политики общего доступа**.
3. В окне **Политики общего доступа** щелкните **RootManageSharedAccessKey**.
4. В окне **Policy: RootManageSharedAccessKey** (Политика: RootManageSharedAccessKey) нажмите кнопку **Копировать** рядом с полем **Первичная строка подключения**, чтобы скопировать строку подключения в буфер обмена для последующего использования. Вставьте на время эти значения в Блокноте или любом другом месте.

    ![Строка подключения][connection-string]
5. Повторите предыдущий шаг, скопировав и вставив значение **первичного ключа** во временное расположение для последующего использования.

## <a name="create-a-topic-and-subscriptions"></a>Создание раздела и подписок

Чтобы создать раздел служебной шины, укажите пространство имен, в котором вы хотите его создать. В следующем примере показано, как создать раздел на портале.

1. В левой области навигации портала щелкните **Служебная шина**. Если элемент **Служебная шина** не отображается, щелкните **Все службы**.
2. Щелкните пространство имен, в котором хотите создать раздел.
3. В окне пространства имен выберите **Разделы**, а затем в окне **Разделы** щелкните **+ Раздел**.
4. Введите **имя** раздела, а остальные значения по умолчанию не изменяйте.
5. Нажмите кнопку **Создать** в нижней части окна.
6. Запишите имя созданного раздела.
7. Выберите только что созданный раздел.
8. Щелкните **+ Подписка**, введите имя подписки **S1** и сохраните значения по умолчанию для остальных параметров.
9. Повторите предыдущий шаг дважды, создав подписки с именами **S2** и **S3**.

## <a name="create-filter-rules-on-subscriptions"></a>Создание правил фильтрации по подпискам

Итак, вы уже подготовили пространство имен, разделы и подписки, также у вас есть необходимые учетные данные, а значит вы готовы создать правила фильтрации для этих подписок, чтобы отправлять и получать сообщения. Этот код можно изучить в [папке с примером на GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/GettingStarted\BasicSendReceiveTutorialwithFilters).

### <a name="send-and-receive-messages"></a>Отправка и получение сообщений

Чтобы выполнить этот код, сделайте следующее:

1. В командной строке или в командной строке PowerShell выполните следующую команду, которая клонирует [репозиторий GitHub для Служебной шины](https://github.com/Azure/azure-service-bus/):

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Перейдите к папке `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveTutorialwithFilters` с примерами.

3. Найдите строку подключения, которую вы скопировали в Блокнот ранее в разделе [Получение учетных данных управления](#obtain-the-management-credentials) этого руководства. Также здесь потребуется имя раздела, который вы создали в предыдущем разделе.

4. В командной строке введите следующую команду:

   ```shell
   dotnet build
   ```

5. Перейдите в папку `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp2.0`.

6. Введите приведенную ниже команду, чтобы запустить программу. Не забудьте ввести вместо `myConnectionString` значение, которое вы получили ранее, а вместо `myTopicName` — имя созданного раздела:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Следуйте инструкциям в консоли, чтобы выбрать процедуру создания фильтра. При создании фильтров необходимо также удалить стандартные фильтры. Если вы используете PowerShell или интерфейс командной строки, стандартный фильтр удалять не требуется, но при выполнении операций в коде это сделать необходимо. Команды консоли 1 и 3 позволяют управлять фильтрами для ранее созданных подписок:

   - Выполните команду 1, чтобы удалить стандартные фильтры.
   - Выполните команду 2, чтобы добавить собственные фильтры.
   - Выполните команду 3, если нужно удалить свои фильтры. Обратите внимание, что это действие не восстанавливает стандартные фильтры.

    ![Пример выходных данных команды 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Завершив создание фильтров, вы можете отправлять сообщения. Нажмите клавишу 4 и наблюдайте, как в раздел отправляются 10 сообщений:

    ![Передача выходных данных](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Нажмите клавишу 5 и наблюдайте за получением этих сообщений. Если не все 10 сообщений будут успешно получены, нажмите клавишу "M" для отображения меню и снова нажмите 5.

    ![Выходные данные операции получения](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите пространство имен и очередь, если они вам больше не нужны. Для этого выберите эти ресурсы на портале и щелкните **Удалить**.

## <a name="understand-the-sample-code"></a>Разбор примера кода

Этот раздел содержит дополнительные сведения о работе этого примера кода.

### <a name="get-connection-string-and-topic"></a>Получение строки подключения и раздела

Прежде всего этот код объявляет набор переменных, которые управляют выполнением остальных частей программы.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

Строка подключения и имя раздела передаются через параметры командной строки, как показано здесь, и затем считываются в метод `Main()`:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Удаление стандартных фильтров

При создании подписки в Служебной шине создается стандартный фильтр для каждой подписки. Этот фильтр позволяет получать каждое сообщение, отправленное в раздел. Если вы намерены использовать пользовательские фильтры, этот стандартный фильтр можно удалить, как показано в следующем коде:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        foreach (var subscription in Subscriptions)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            await s.RemoveRuleAsync(RuleDescription.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
            await s.CloseAsync();
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Создание фильтров

В следующем коде добавляются пользовательские фильтры, которые описаны в этом руководстве:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, Subscriptions[i]);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await s.AddRuleAsync(new RuleDescription
                        {
                            Filter = new SqlFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Удаление созданных пользовательских фильтров

Если нужно удалить из подписки все фильтры, воспользуйтесь следующим примером кода:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            SubscriptionClient s = new SubscriptionClient(ServiceBusConnectionString, TopicName, subscription);
            IEnumerable<RuleDescription> rules = await s.GetRulesAsync();
            foreach (RuleDescription r in rules)
            {
                await s.RemoveRuleAsync(r.Name);
                Console.WriteLine($"Rule {r.Name} has been removed.");
            }
            await s.CloseAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Отправка сообщений

Отправка сообщений в раздел происходит так же, как и отправка сообщений в очередь. В этом примере показано, как отправлять сообщения, используя список задач и асинхронную обработку:

```csharp
public async Task SendMessages()
{
    try
    {
        TopicClient tc = new TopicClient(ServiceBusConnectionString, TopicName);

        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(tc, Store[i]));
        }

        await Task.WhenAll(taskList);
        await tc.CloseAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(TopicClient tc, string store)
{
    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        Message message = item.AsMessage();
        message.To = store;
        message.UserProperties.Add("StoreId", store);
        message.UserProperties.Add("Price", item.getPrice().ToString());
        message.UserProperties.Add("Color", item.getColor());
        message.UserProperties.Add("Category", item.getItemCategory());

        await tc.SendAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Получение сообщений

Сообщения принимаются обратно через список задач. Этот код использует стратегию пакетной обработки. Пакетную обработку можно применять как для отправки, так и для получения. Но в нашем примере реализовано только пакетное получение. Для реальных систем этот цикл обычно не прерывается, а повторяется неограниченно долго и с более длительным периодом ожидания, например в одну минуту. На протяжении этого периода поддерживается подключение к брокеру. Когда появится новое сообщение, программа немедленно возвращает их и создает новый вызов для получения данных. Такой подход называется методом *продолжительного опроса*. Еще чаще применяется средство переноса полученных данных, которое реализовано в [этом кратком руководстве](service-bus-quickstart-portal.md) и еще нескольких примерах в репозитории.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    var entityPath = EntityNameHelper.FormatSubscriptionPath(TopicName, subscription);
    var receiver = new MessageReceiver(ServiceBusConnectionString, entityPath, ReceiveMode.PeekLock, RetryPolicy.Default, 100);

    while (true)
    {
        try
        {
            IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            if (messages.Any())
            {
                foreach (var message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IDictionary<string, object> myUserProperties = message.UserProperties;
                        Console.WriteLine($"StoreId={myUserProperties["StoreId"]}");

                        if (message.Label != null)
                        {
                            Console.WriteLine($"Label={message.Label}");
                        }

                        Console.WriteLine(
                            $"Item data: Price={item.getPrice()}, Color={item.getColor()}, Category={item.getItemCategory()}");
                    }

                    await receiver.CompleteAsync(message.SystemProperties.LockToken);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }

    await receiver.CloseAsync();
}
```

## <a name="next-steps"></a>Дополнительная информация

При работе с этим руководством вы подготовили ресурсы с помощью портала Azure, а затем отправили и получили сообщения через раздел Служебной шины и подписки на него. Вы научились выполнять следующие задачи:

> [!div class="checklist"]
> * создание раздела служебной шины и одной или нескольких подписок на этот раздел с помощью портала Azure;
> * добавление фильтров раздела в коде .NET;
> * создание двух сообщений с разным содержимым;
> * отправка сообщений и проверка их поступления в ожидаемые подписки;
> * получение сообщений из подписок.

Чтобы изучить дополнительные примеры отправки и получения сообщений, перейдите к [описанию примеров для служебной шины на GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Следующее руководство содержит дополнительные сведения об использовании возможностей публикации и подписки в cлужебной шине Azure.

> [!div class="nextstepaction"]
> [Обновление информации о запасах с помощью PowerShell, разделов и подписок](service-bus-tutorial-topics-subscriptions-powershell.md)

[бесплатная учетная запись]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[полному доменному имени]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png