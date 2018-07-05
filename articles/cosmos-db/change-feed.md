---
title: Работа с поддержкой веб-канала изменений в Azure Cosmos DB | Документация Майкрософт
description: Используйте поддержку веб-канала изменений Azure Cosmos DB, чтобы отслеживать изменения в документах и выполнять обработку на основе событий, например триггеров, и поддерживать кэши и аналитические системы в обновленном состоянии.
keywords: веб-канал изменений
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 8475c79782730e989f9590566c31ccd50af9f144
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302052"
---
# <a name="working-with-the-change-feed-support-in-azure-cosmos-db"></a>Работа с поддержкой веб-канала изменений в Azure Cosmos DB

[Azure Cosmos DB](../cosmos-db/introduction.md) — это быстрая и гибкая реплицируемая база данных. Она хорошо подходит для приложений Интернета вещей, розничной торговли, игр и приложений для ведения операционного журнала. Распространенный конструктивный шаблон в этих приложениях заключается в использовании изменений в данных для запуска дополнительных действий, в том числе: 

* активирование уведомления или вызова API при вставке или изменении документа;
* потоковая обработка для Интернета вещей или выполнение аналитики;
* дополнительное перемещение данных путем их синхронизации с кэшем, поисковой системой или хранилищем данных либо архивирование данных в автономное неструктурированное защищенное хранилище.

**Поддержка канала изменений** в Azure Cosmos DB дает возможность создавать эффективные и масштабируемые решения для каждого из этих шаблонов, как показано на следующем рисунке:

![Использование веб-канала изменений Azure Cosmos DB для аналитики в реальном времени и вычислений на основе событий](./media/change-feed/changefeedoverview.png)

> [!NOTE]
> Поддержка канала изменений предоставляется для всех моделей данных и контейнеров в Azure Cosmos DB. Однако канал изменений считывается с помощью клиента SQL и сериализует элементы в формат JSON. Из-за форматирования в JSON клиенты MongoDB будут сталкиваться с несоответствием между документами в формате BSON и каналом изменений в формате JSON.

В следующем видео руководитель программы Azure Cosmos DB Эндрю Лю (Andrew Liu) покажет вам, как работает канал изменений Azure Cosmos DB.

> [!VIDEO https://www.youtube.com/embed/mFnxoxeXlaU]
>
>

## <a name="how-does-change-feed-work"></a>Как работает канал изменений?

Канал изменений, поддерживаемый в Azure Cosmos DB, прослушивает изменения в коллекции Azure Cosmos DB. Затем он выводит отсортированный список документов в порядке, в котором они были изменены. Изменения сохраняются и обрабатываются асинхронно и пошагово, а выходные данные могут распределяться в один или несколько объектов-получателей для параллельной обработки. 

Вы можете считывать канал изменений тремя разными способами, как описано далее в этой статье:

*   [используя Функции Azure](#azure-functions);
*   [используя пакет SDK для Azure Cosmos DB](#sql-sdk);
*   [используя библиотеку обработчика для канала изменений Azure Cosmos DB](#change-feed-processor).

Канал изменений доступен для каждого диапазона ключей разделов в коллекции документов, и поэтому его можно распределить в один или несколько потребителей для параллельной обработки, как показано на следующем рисунке.

![Распределенная обработка веб-канала изменений Azure Cosmos DB](./media/change-feed/changefeedvisual.png)

Дополнительная информация
* Канал изменений включен по умолчанию для всех учетных записей.
* Вы можете использовать свою [подготовленную пропускную способность](request-units.md) в своем регионе записи или любом [регионе чтения](distribute-data-globally.md) для чтения с веб-канала изменений так же, как и любую другую операцию Azure Cosmos DB.
* Веб-канал изменений также отслеживает операции вставки и обновления, внесенные в документы в коллекции. Вы можете отслеживать операции удаления, установив флажок soft-delete в документах вместо удаления. Кроме того, с помощью [функции срока жизни](time-to-live.md) для документов можно задать ограниченный срок действия, например 24 часа, и использовать значение этого свойства для отслеживания операций удаления. При использовании этого решения необходимо обрабатывать изменения в течение более короткого промежутка времени, чем срок жизни.
* Каждое изменение в документе отображается только один раз в веб-канале изменений, а клиенты управляют логикой контрольных точек. Библиотека обработчика веб-канала изменений предоставляет возможность автоматического создания контрольных точек и семантику типа "не менее одного раза".
* В журнал изменений вносится только самое последнее изменение в конкретном документе. Промежуточные изменения могут быть недоступны.
* Веб-канал изменений сортируется в порядке изменения в каждом значении ключа раздела. В значениях ключа раздела нет установленного порядка.
* Изменения можно синхронизировать в любой момент времени. Это означает, что фиксированный период хранения данных, для которого доступны изменения, отсутствует.
* Изменения доступны в виде фрагментов диапазонов ключей разделов. Эта возможность позволяет нескольким потребителям и серверам параллельно обрабатывать изменения из больших коллекций.
* Приложения могут запросить несколько каналов изменений в одной коллекции одновременно.
* Свойство ChangeFeedOptions.StartTime можно использовать для предоставления начальной точки, например, чтобы найти токен продолжения, соответствующий заданному времени. Если значение ContinuationToken указано, оно имеет приоритет перед значениями StartTime и StartFromBeginning. Точность ChangeFeedOptions.StartTime равняется приблизительно 5 секундам. 

## <a name="use-cases-and-scenarios"></a>Варианты использования и сценарии

Канал изменений обеспечивает эффективную обработку больших наборов данных с большим объемом операций записи и представляет собой альтернативу отправке запросов на все наборы данных, чтобы определить, какие изменения были внесены. 

Например, с помощью канала изменений можно эффективно выполнить следующие задачи:

* Обновление кэша, индекса поиска или хранилища данными, хранящимися в Azure Cosmos DB.
* Реализация распределения данных по уровням и их архивирование на уровне приложений, то есть хранение "горячих данных" в Azure Cosmos DB и удаление "холодных данных" в [хранилище BLOB-объектов Azure](../storage/common/storage-introduction.md) или [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md).
* Выполнение переносов в другую учетную запись Azure Cosmos DB с использованием другой схемы секционирования без простоев.
* Реализация [лямбда-конвейеров в Azure](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) с помощью Azure Cosmos DB. Azure Cosmos DB предоставляет масштабируемое решение базы данных, которое может обрабатывать операции приема и запроса, а также реализовывать лямбда-архитектуры с низкой совокупной стоимостью владения. 
* Получение и хранение данных о событиях с устройств, датчиков, инфраструктуры и приложений и обработка этих событий в реальном времени с помощью [Azure Stream Analytics](../stream-analytics/stream-analytics-documentdb-output.md), [Apache Storm](../hdinsight/storm/apache-storm-overview.md) или [Apache Spark](../hdinsight/spark/apache-spark-overview.md). 

На следующем рисунке показано, как лямбда-конвейеры, которые выполняют прием и запрос с помощью Azure Cosmos DB, используют канал изменений: 

![Лямбда-конвейер для приема и запроса на основе Azure Cosmos DB](./media/change-feed/lambda.png)

Кроме того, в [бессерверных](http://azure.com/serverless) веб-приложениях и мобильных приложениях вы можете отслеживать события, такие как изменения в профиле, настройке или местоположении для активирования определенных действий, например отправки push-уведомлений на свои устройства, с помощью [Функций Azure](#azure-functions). При использовании Azure Cosmos DB для создания игр веб-канал изменений можно использовать, например, для создания списков лидеров в режиме реального времени на основе очков в завершенных играх.

<a id="azure-functions"></a>
## <a name="using-azure-functions"></a>Использование Функций Azure 

Если вы используете службу "Функции Azure", самый простой способ подключиться к каналу изменений Azure Cosmos DB — это добавить триггер Azure Cosmos DB в приложение-функцию Azure. При создании триггера Azure Cosmos DB в приложении-функции Azure необходимо выбрать коллекцию Azure Cosmos DB для подключения, и функция будет запускаться каждый раз, когда будут внесены изменения в коллекцию. 

Триггеры можно создать на портале Функций Azure, на портале Azure Cosmos DB или программным способом. Дополнительные сведения см. в статье [Azure Cosmos DB: обработка данных бессерверных баз данных с помощью службы "Функции Azure"](serverless-computing-database.md).

<a id="sql-sdk"></a>
## <a name="using-the-sdk"></a>Использование пакета SDK

[Пакет SDK SQL](sql-api-sdk-dotnet.md) для Azure Cosmos DB предоставляет все возможности для считывания канала изменений и управления им. Однако чем больше возможностей, тем больше ответственности. Если вы хотите управлять контрольными точками, обрабатывать порядковые номера документа и детально управлять ключами секций, то использование пакета SDK будет верным решением.

Этот раздел содержит сведения о том, как использовать пакет SDK для SQL для работы с каналом изменений.

1. Начните с изучения следующих ресурсов из Appconfig. Сведения о получении конечной точки и ключа авторизации см. в разделе [Обновление строки подключения](create-sql-api-dotnet.md#update-your-connection-string).

    ``` csharp
    DocumentClient client;
    string DatabaseName = ConfigurationManager.AppSettings["database"];
    string CollectionName = ConfigurationManager.AppSettings["collection"];
    string endpointUrl = ConfigurationManager.AppSettings["endpoint"];
    string authorizationKey = ConfigurationManager.AppSettings["authKey"];
    ```

2. Создайте клиент следующим образом:

    ```csharp
    using (client = new DocumentClient(new Uri(endpointUrl), authorizationKey,
    new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp }))
    {
    }
    ```

3. Получите диапазоны ключей секций:

    ```csharp
    FeedResponse pkRangesResponse = await client.ReadPartitionKeyRangeFeedAsync(
        collectionUri,
        new FeedOptions
            {RequestContinuation = pkRangesResponseContinuation });
     
    partitionKeyRanges.AddRange(pkRangesResponse);
    pkRangesResponseContinuation = pkRangesResponse.ResponseContinuation;
    ```

4. Вызовите ExecuteNextAsync для каждого диапазона ключей секций:

    ```csharp
    foreach (PartitionKeyRange pkRange in partitionKeyRanges){
        string continuation = null;
        checkpoints.TryGetValue(pkRange.Id, out continuation);
        IDocumentQuery<Document> query = client.CreateDocumentChangeFeedQuery(
            collectionUri,
            new ChangeFeedOptions
            {
                PartitionKeyRangeId = pkRange.Id,
                StartFromBeginning = true,
                RequestContinuation = continuation,
                MaxItemCount = -1,
                // Set reading time: only show change feed results modified since StartTime
                StartTime = DateTime.Now - TimeSpan.FromSeconds(30)
            });
        while (query.HasMoreResults)
            {
                FeedResponse<dynamic> readChangesResponse = query.ExecuteNextAsync<dynamic>().Result;
    
                foreach (dynamic changedDocument in readChangesResponse)
                    {
                         Console.WriteLine("document: {0}", changedDocument);
                    }
                checkpoints[pkRange.Id] = readChangesResponse.ResponseContinuation;
            }
    }
    ```

> [!NOTE]
> Вместо `ChangeFeedOptions.PartitionKeyRangeId` можно использовать `ChangeFeedOptions.PartitionKey`, чтобы указать один ключ секции, для которого нужно получить канал изменений. Например, `PartitionKey = new PartitionKey("D8CFA2FD-486A-4F3E-8EA6-F3AA94E5BD44")`.
> 
>

Если у вас несколько читателей, можно использовать **ChangeFeedOptions**, чтобы распределить нагрузку чтения среди разных потоков или разных клиентов.

Вот и все. С помощью этих нескольких строк кода вы можете начать считывание канала изменений. Полный код, используемый в этой статье, можно получить из [репозитория GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeed).

В коде на шаге 4 **ResponseContinuation** в последней строке имеет последний регистрационный номер транзакции в журнале (LSN) документа, который будет использоваться при следующем чтении новых документов после этого порядкового номера. С помощью свойства **StartTime** в разделе **ChangeFeedOption** вы можете расширить свою сеть, чтобы получить документы. Если свойство **ResponseContinuation** имеет значение NULL, а **StartTime** обращается к данным за прошедшие периоды, то вы получите все документы, которые изменены со **времени начала**. Но если свойство **ResponseContinuation** имеет значение, то система предоставит все ваши документы после номера LSN.

Таким образом массив контрольной точки будет просто хранить номер LSN для каждой секции. Если вы не хотите работать с секциями, контрольными точками, номерами LSN, временем начала и т. д., проще всего использовать библиотеку обработчика для канала изменений.

<a id="change-feed-processor"></a>
## <a name="using-the-change-feed-processor-library"></a>Использование библиотеки обработчика для канала изменений 

[Библиотека обработчика для канала изменений Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet-changefeed) позволяет без усилий распределить обработку событий между несколькими объектами-получателями. Эта библиотека упрощает считывание изменений в секциях и нескольких потоках, работающих параллельно.

Основным преимуществом библиотеки обработчика для канала изменений является то, что вам не нужно управлять каждой секцией и маркером продолжения. Вам также не нужно опрашивать каждую коллекцию вручную.

Эта библиотека упрощает считывание изменений в секциях и нескольких потоках в параллельном режиме.  Она автоматически управляет считыванием изменений между секциями с помощью механизма аренды. Как видно на следующей схеме, если запустить два клиента, которые используют библиотеку обработчика для канала изменений, работа будет разделена между ними. По мере увеличения клиентов они будут точно так же разделять работу между собой.

![Распределенная обработка веб-канала изменений Azure Cosmos DB](./media/change-feed/change-feed-output.png)

Сперва был запущен левый клиент, и он начал отслеживать все секции. Затем был запущен второй клиент, и первый передал ему некоторые аренды. Как видите, это отличный способ распределить работу между разными компьютерами и клиентами.

Обратите внимание, что при наличии двух бессерверных функций Azure, которые отслеживают одну и ту же коллекцию и используют одинаковую аренду, эти функции могут получить разные документы в зависимости от того, как библиотека обработчика решит обработать секции.

<a id="understand-cf"></a>
### <a name="understanding-the-change-feed-processor-library"></a>Основные сведения о библиотеке обработчика для канала изменений

При реализации библиотеки обработчика для канала изменений задействуется четыре основных компонента: отслеживаемая коллекция, коллекция аренд, узел обработчика и объекты-получатели. 

> [!WARNING]
> Создание коллекции связано с ценовыми требованиями, так как для взаимодействия с базой данных Azure Cosmos DB для приложения резервируется пропускная способность. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

**Отслеживаемая коллекция** — это данные, из которых формируется веб-канал изменений. Все операции вставки и изменения в отслеживаемой коллекции отражаются в веб-канале изменений коллекции. 

**Коллекция аренд** — это коллекция, которая координирует обработку веб-канала изменений для нескольких рабочих ролей. Отдельная коллекция используется для хранения аренд на каждую секцию. Эту коллекцию аренд следует хранить в другой учетной записи, чтобы связанный с ней регион записи был ближе к расположению, в котором выполняется обработчик канала изменений. Объект аренды содержит следующие атрибуты: 
* Владелец: определяет узел, которому принадлежит аренда.
* Продолжение: определяет положение (маркер продолжения) определенной секции в веб-канале изменений.
* Отметка времени: время последнего обновления аренды. Отметка времени может использоваться для проверки истечения срока действия аренды. 

**Узел обработчика.** Каждый узел определяет, сколько секций нужно обработать в зависимости от того, сколько других экземпляров узлов имеют активные аренды. 
1.  После запуска узла он получает аренды для распределения рабочей нагрузки по всем узлам. Узел периодически обновляет аренды, чтобы они оставалась активными. 
2.  Узел сопоставляет последний маркер продолжения с арендой перед каждой операцией чтения. Чтобы обеспечить безопасность параллелизма, узел проверяет значение ETag для каждого обновления аренды. Также поддерживаются другие стратегии контрольных точек.  
3.  После завершения работы узел отменяет все аренды, но сохраняет сведения о продолжении, чтобы иметь позже возможность возобновить чтение с сохраненной контрольной точки. 

В настоящее время количество узлов не может превышать количество секций (аренд).

**Потребители:** объекты-получатели или рабочие роли являются потоками, которые выполняют обработку веб-канала изменений, инициированную каждым узлом. Каждый узел обработчика может иметь несколько объектов-получателей. Каждый объект-получатель считывает веб-канал изменений из секции, которой он присвоен, и уведомляет узел об изменениях и окончании срока действия аренд.

Чтобы лучше разобраться, как взаимодействуют эти четыре элемента обработчика для канала изменений, давайте рассмотрим пример, приведенный на схеме ниже. Отслеживаемая коллекция хранит документы и использует city в качестве ключа секции. Мы видим, что синяя секция содержит документы с полем city от A до E и т. д. Существует два узла, каждый с двумя объектами-получателями, которые выполняют операцию чтения из четырех секций в параллельном режиме. Стрелки показывают конкретные точки в веб-канале изменений, в которых объекты-получатели выполняют операцию чтения. В первой секции синим цветом представлены непрочитанные изменения, а голубым — уже прочитанные изменения в веб-канале изменений. Узлы используют коллекцию аренд для хранения значения "продолжение", чтобы отслеживать текущую позицию чтения каждого объекта-получателя. 

![Использование узла обработчика веб-канала изменений Azure Cosmos DB](./media/change-feed/changefeedprocessornew.png)

### <a name="working-with-the-change-feed-processor-library"></a>Работа с библиотекой обработчика для канала изменений

Прежде чем устанавливать пакет NuGet обработчика канала изменений, установите следующие компоненты: 

* Microsoft.Azure.DocumentDB, последняя версия.
* Newtonsoft.Json, последняя версия

Затем установите [пакет Nuget Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/) и добавьте его в качестве ссылки.

Чтобы реализовать библиотеку для обработчика канала изменений, сделайте следующее:

1. Создайте объект **DocumentFeedObserver**, который реализует **IChangeFeedObserver**.
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;
    using Microsoft.Azure.Documents.Client;

    /// <summary>
    /// This class implements the IChangeFeedObserver interface and is used to observe 
    /// changes on change feed. ChangeFeedEventHost will create as many instances of 
    /// this class as needed. 
    /// </summary>
    public class DocumentFeedObserver : IChangeFeedObserver
    {
    private static int totalDocs = 0;

        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserver" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        /// <param name="client"> Client connected to destination collection </param>
        /// <param name="destCollInfo"> Destination collection information </param>
        public DocumentFeedObserver()
        {
            
        }

        /// <summary>
        /// Called when change feed observer is opened; 
        /// this function prints out observer partition key id. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task OpenAsync(IChangeFeedObserverContext context)
        {
            Console.ForegroundColor = ConsoleColor.Magenta;
            Console.WriteLine("Observer opened for partition Key Range: {0}", context.PartitionKeyRangeId);
            return Task.CompletedTask;
        }

        /// <summary>
        /// Called when change feed observer is closed; 
        /// this function prints out observer partition key id and reason for shut down. 
        /// </summary>
        /// <param name="context">The context specifying partition for this observer, etc.</param>
        /// <param name="reason">Specifies the reason the observer is closed.</param>
        /// <returns>A Task to allow asynchronous execution</returns>
        public Task CloseAsync(IChangeFeedObserverContext context, ChangeFeedObserverCloseReason reason)
        {
            Console.ForegroundColor = ConsoleColor.Cyan;
            Console.WriteLine("Observer closed, {0}", context.PartitionKeyRangeId);
            Console.WriteLine("Reason for shutdown, {0}", reason);
            return Task.CompletedTask;
        }

        public Task ProcessChangesAsync(IChangeFeedObserverContext context, IReadOnlyList<Document> docs, CancellationToken cancellationToken)
        {
            Console.ForegroundColor = ConsoleColor.Green;
            Console.WriteLine("Change feed: PartitionId {0} total {1} doc(s)", context.PartitionKeyRangeId, Interlocked.Add(ref totalDocs, docs.Count));
            foreach (Document doc in docs)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(doc.Id.ToString());
            }

            return Task.CompletedTask;
        }
    }
    ```

2. Создайте **DocumentFeedObserverFactory**, который реализует **IChangeFeedObserverFactory**.
    ```csharp
     using Microsoft.Azure.Documents.ChangeFeedProcessor.FeedProcessing;

    /// <summary>
    /// Factory class to create instance of document feed observer. 
    /// </summary>
    public class DocumentFeedObserverFactory : IChangeFeedObserverFactory
    {
        /// <summary>
        /// Initializes a new instance of the <see cref="DocumentFeedObserverFactory" /> class.
        /// Saves input DocumentClient and DocumentCollectionInfo parameters to class fields
        /// </summary>
        public DocumentFeedObserverFactory()
        {
        }

        /// <summary>
        /// Creates document observer instance with client and destination collection information
        /// </summary>
        /// <returns>DocumentFeedObserver with client and destination collection information</returns>
        public IChangeFeedObserver CreateObserver()
        {
            DocumentFeedObserver newObserver = new DocumentFeedObserver();
            return newObserver as IChangeFeedObserver;
        }
    }
    ```

3. Определите *CancellationTokenSource* и *ChangeFeedProcessorBuilder*

    ```csharp
    private readonly CancellationTokenSource cancellationTokenSource = new CancellationTokenSource();
    private readonly ChangeFeedProcessorBuilder builder = new ChangeFeedProcessorBuilder();
    ```

5. Скомпилируйте **ChangeFeedProcessorBuilder** после определения соответствующих объектов 

    ```csharp
            string hostName = Guid.NewGuid().ToString();
      
            // monitored collection info 
            DocumentCollectionInfo documentCollectionInfo = new DocumentCollectionInfo
            {
                Uri = new Uri(this.monitoredUri),
                MasterKey = this.monitoredSecretKey,
                DatabaseName = this.monitoredDbName,
                CollectionName = this.monitoredCollectionName
            };
            
            DocumentCollectionInfo leaseCollectionInfo = new DocumentCollectionInfo
                {
                    Uri = new Uri(this.leaseUri),
                    MasterKey = this.leaseSecretKey,
                    DatabaseName = this.leaseDbName,
                    CollectionName = this.leaseCollectionName
                };
            DocumentFeedObserverFactory docObserverFactory = new DocumentFeedObserverFactory();
            ChangeFeedOptions feedOptions = new ChangeFeedOptions();

            /* ie customize StartFromBeginning so change feed reads from beginning
                can customize MaxItemCount, PartitonKeyRangeId, RequestContinuation, SessionToken and StartFromBeginning
            */

            feedOptions.StartFromBeginning = true;
        
            ChangeFeedProcessorOptions feedProcessorOptions = new ChangeFeedProcessorOptions();

            // ie. customizing lease renewal interval to 15 seconds
            // can customize LeaseRenewInterval, LeaseAcquireInterval, LeaseExpirationInterval, FeedPollDelay 
            feedProcessorOptions.LeaseRenewInterval = TimeSpan.FromSeconds(15);

            this.builder
                .WithHostName(hostName)
                .WithFeedCollection(documentCollectionInfo)
                .WithLeaseCollection(leaseCollectionInfo)
                .WithProcessorOptions (feedProcessorOptions)
                .WithObserverFactory(new DocumentFeedObserverFactory());               
                //.WithObserver<DocumentFeedObserver>();  If no factory then just pass an observer

            var result =  await this.builder.BuildAsync();
            await result.StartAsync();
            Console.Read();
            await result.StopAsync();    
            ```

That’s it. After these few steps documents will start showing up into the **DocumentFeedObserver.ProcessChangesAsync** method.

Above code is for illustration purpose to show different kind of objects and their interaction. You have to define proper variables and initiate them with correct values. You can get the complete code used in this article from the [GitHub repo](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor).

> [!NOTE]
> You should never have a master key in your code or in config file as shown in above code. Please see [how to use Key-Vault to retrive the keys](https://sarosh.wordpress.com/2017/11/23/cosmos-db-and-key-vault/).


## FAQ

### What are the different ways you can read Change Feed? and when to use each method?

There are three options for you to read change feed:

* **[Using Azure Cosmos DB SQL API .NET SDK](#sql-sdk)**
   
   By using this method, you get low level of control on change feed. You can manage the checkpoint, you can access a particular partition key etc. If you have multiple readers, you can use [ChangeFeedOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.changefeedoptions?view=azure-dotnet) to distribute read load to different threads or different clients. .

* **[Using the Azure Cosmos DB change feed processor library](#change-feed-processor)**

   If you want to outsource lot of complexity of change feed then you can use change feed processor library. This library hides lot of complexity, but still gives you complete control on change feed. This library follows an [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern), your processing function is called by the SDK. 

   If you have a high throughput change feed, you can instantiate multiple clients to read the change feed. Because you are using “change feed processor library”, it will automatically divide the load among different clients. You do not have to do anything. All the complexity is handled by SDK. However, if you want to have your own load balancer, then you can implement IParitionLoadBalancingStrategy for custom partition strategy. Implement IPartitionProcessor – for custom processing changes on a partition. However, with SDK, you can process a partition range but if you want to process a particular partition key then you have to use SDK for SQL API.

* **[Using Azure Functions](#azure-functions)** 
   
   The last option Azure Function is the simplest option. We recommend using this option. When you create an Azure Cosmos DB trigger in an Azure Functions app, you select the Azure Cosmos DB collection to connect to and the function is triggered whenever a change to the collection is made. watch a [screen cast](https://www.youtube.com/watch?v=Mnq0O91i-0s&t=14s) of using Azure function and change feed

   Triggers can be created in the Azure Functions portal, in the Azure Cosmos DB portal, or programmatically. Visual Studio and VS Code has great support to write Azure Function. You can write and debug the code on your desktop, and then deploy the function with one click. For more information, see [Azure Cosmos DB: Serverless database computing using Azure Functions](serverless-computing-database.md) article.

### What is the sort order of documents in change feed?

Change feed documents comes in order of their modification time. This sort order is guaranteed only per partition.

### For a multi-region account, what happens to the change feed when the write-region fails-over? Does the change feed also failover? Would the change feed still appear contiguous or would the fail-over cause change feed to reset?

Yes, change feed will work across the manual failover operation and it will be contiguous.

### How long change feed persist the changed data if I set the TTL (Time to Live) property for the document to -1?

Change feed will persist forever. If data is not deleted, it will remain in change feed.

### How can I configure Azure functions to read from a particular region, as change feed is available in all the read regions by default?

Currently it’s not possible to configure Azure Functions to read from a particular region. There is a GitHub issue in the Azure Functions repo to set the preferred regions of any Azure Cosmos DB binding and trigger.

Azure Functions uses the default connection policy. You can configure connection mode in Azure Functions and by default, it reads from the write region, so it is best to co-locate Azure Functions on the same region.

### What is the default size of batches in Azure Functions?

100 documents at every invocation of Azure Functions. However, this number is configurable within the function.json file. Here is complete [list of configuration options](../azure-functions/functions-run-local.md). If you are developing locally, update the application settings within the [local.settings.json](../azure-functions/functions-run-local.md) file.

### I am monitoring a collection and reading its change feed, however I see I am not getting all the inserted document, some documents are missing. What is going on here?

Please make sure that there is no other function reading the same collection with the same lease collection. It happened to me, and later I realized the missing documents are processed by my other Azure functions, which is also using the same lease.

Therefore, if you are creating multiple Azure Functions to read the same change feed then they must use different lease collection or use the “leasePrefix” configuration to share the same collection. However, when you use change feed processor library you can start multiple instances of your function and SDK will divide the documents between different instances automatically for you.

### My document is updated every second, and I am not getting all the changes in Azure Functions listening to change feed.

Azure Functions polls change feed for every 5 seconds, so any changes made between 5 seconds are lost. Azure Cosmos DB stores just one version for every 5 seconds so you will get the 5th change on the document. However, if you want to go below 5 second, and want to poll change Feed every second, You can configure the polling time “feedPollTime”, see [Azure Cosmos DB bindings](../azure-functions/functions-bindings-cosmosdb.md#trigger---configuration). It is defined in milliseconds with a default of 5000. Below 1 second is possible but not advisable, as you will start burning more CPU.

### I inserted a document in the Mongo API collection, but when I get the document in change feed, it shows a different id value. What is wrong here?

Your collection is Mongo API collection. Remember, change feed is read using the SQL client and serializes items into JSON format. Because of the JSON formatting, MongoDB clients will experience a mismatch between BSON formatted documents and the JSON formatted change feed. You are seeing is the representation of a BSON document in JSON. If you use binary attributes in a Mongo accounts, they are converted to JSON.

### Is there a way to control change feed for updates only and not inserts?

Not today, but this functionality is on roadmap. Today, you can add a soft marker on the document for updates.

### Is there a way to get deletes in change feed?

Currently change feed doesn’t log deletes. Change feed is continuously improving, and this functionality is on roadmap. Today, you can add a soft marker on the document for delete. Add an attribute on the document called “deleted” and set it to “true” and set a TTL on the document so that it can be automatically deleted.

### Can I read change feed for historic documents(for example, documents that were added 5 years back) ?

Yes, if the document is not deleted you can read the change feed as far as the origin of your collection.

### Can I read change feed using JavaScript?

Yes, Node.js SDK initial support for change feed is recently added. It can be used as shown in the following example, please update documentdb module to current version before you run the code:

```js

var DocumentDBClient = require('documentdb').DocumentClient;
const host = "https://your_host:443/";
const masterKey = "your_master_key==";
const databaseId = "db";
const collectionId = "c1";
const dbLink = 'dbs/' + databaseId;
const collLink = dbLink + '/colls/' + collectionId;
var client = new DocumentDBClient(host, { masterKey: masterKey });
let options = {
    a_im: "Incremental feed",
    accessCondition: {
        type: "IfNoneMatch",        // Use: - empty condition (or remove accessCondition entirely) to start from beginning.
        //      - '*' to start from current.
        //      - specific etag value to start from specific continuation.
        condition: ""
    }
};
 
var query = client.readDocuments(collLink, options);
query.executeNext((err, results, headers) =&gt; {
    // Now we have headers.etag, which can be used in next readDocuments in accessCondition option.
    console.log(results);
    console.log(headers.etag);
    console.log(results.length);
    options.accessCondition = { type: "IfNoneMatch", condition: headers.etag };
    var query = client.readDocuments(collLink, options);
    query.executeNext((err, results, headers) =&gt; {
        console.log("next one:", results[0]);
    });
});<span id="mce_SELREST_start" style="overflow:hidden;line-height:0;"></span>

```

### <a name="can-i-read-change-feed-using-java"></a>Можно ли считывать данные канала изменений с помощью Java?

Библиотека Java для считывания данных из канала изменений доступна в [репозитории Github](https://github.com/Azure/azure-documentdb-changefeedprocessor-java). Сейчас эта библиотека на несколько версий отстает от библиотеки .NET. Вскоре мы синхронизируем обе библиотеки.

### <a name="can-i-use-etag-lsn-or-ts-for-internal-bookkeeping-which-i-get-in-response"></a>Можно ли использовать отображаемые в ответе идентификаторы _etag, _lsn или _ts для внутреннего бухгалтерского учета?

_etag является внутренним идентификатором. Его не следует использовать или анализировать, так как он может измениться в любое время.
_ts представляет метку времени создания или изменения. Этот идентификатор можно использовать для сравнения в хронологическом порядке.
_lsn — это идентификатор пакета, который добавляется только для канала изменений и представляет идентификатор транзакций из хранилища. Многие документы могут иметь одинаковые идентификаторы _lsn.
Кроме того, обратите внимание, что ETag в FeedResponse отличается от _etag, который отображается в документе. _etag — это внутренний идентификатор, который используется при параллельной обработке. Он указывает на версию документа, тогда как ETag используется для упорядочивания канала.

### <a name="does-reading-change-feed-add-any-additional-cost-"></a>Взимается ли за чтение данных из канала изменений дополнительная плата?

Плата взимается за использованные единицы запросов (ЕЗ), а перемещение данных из коллекции Azure Cosmos DB всегда подразумевает использование ЕЗ. С пользователей будет взиматься плата за ЕЗ, использованные при операциях с данными в коллекции аренд.

### <a name="can-multiple-azure-functions-read-one-collections-change-feed"></a>Могут ли несколько функций Azure считывать данные одной коллекции канала изменений?

Да. Несколько функций Azure могут считывать данные одной коллекции канала изменений. При этом для каждой функции Azure следует отдельно определить leaseCollectionPrefix.

### <a name="should-the-lease-collection-be-partitioned"></a>Нужно ли секционировать коллекцию аренд?

Нет, коллекцию аренд можно не секционировать. Секционированные коллекции аренд сейчас не используются и не поддерживаются.

### <a name="can-i-read-change-feed-from-spark"></a>Можно ли считывать данные канала изменений с помощью Spark?

Да, можно. См. руководство по использованию [соединителя Spark для Azure Cosmos DB](spark-connector.md). См. дополнительные сведения об [обработке канала изменений как структурированного потока](https://www.youtube.com/watch?v=P9Qz4pwKm_0&t=1519s).

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-say-a-batch-of-10-documents-and-i-get-an-error-at-7th-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-documentie-7th-document-in-my-next-feed"></a>Я обрабатываю канал изменений, который включает пакет из 10 документов, с помощью решения "Функции Azure". При обработке седьмого документа поступает сообщение об ошибке. После этого три последних документа не обрабатываются. Как запустить обработку документа с ошибкой (т. е. седьмого документа) в следующем канале?

Для обработки ошибки рекомендуется включить в код блок try-catch. Перехватите ошибку и поместите документ в очередь недоставленных сообщений, а затем определите логику обработки документов с ошибками. Так, если у вас есть пакет из 200 документов и в одном документе возникает ошибка, вам не придется использовать урезанный пакет.

В случае ошибки вам не нужно выполнять откат до контрольной точки, так как вы будете и дальше получать эти документы из канала изменений. Обратите внимание, что в канале изменений сохраняется последний моментальный снимок документов. Поэтому предыдущий моментальный снимок документа может не сохраниться. В канале изменений сохраняется только последняя версия документа. При этом между операциями документ может быть изменен другими процессами.

По мере исправления кода документы будут удаляться из очереди недоставленных сообщений.
Решение "Функции Azure" автоматически вызывается системой канала изменений. При этом контрольная точка внутренне поддерживается решением "Функции Azure". Чтобы выполнить контролируемый откат до контрольной точки, используйте пакет SDK обработчика канала изменений.


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об использовании Azure Cosmos DB с Функциями Azure см. в статье [Azure Cosmos DB: обработка данных бессерверных баз данных с помощью службы "Функции Azure"](serverless-computing-database.md).

Дополнительные сведения об использовании библиотеки обработчика для канала изменений см. в следующих ресурсах:

* [Пакет SDK для обработчика веб-канала изменений для DocumentDB .NET: скачивание и заметки о выпуске](sql-api-sdk-dotnet-changefeed.md) 
* [Microsoft.Azure.DocumentDB.ChangeFeedProcessor](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)
* [Пример кода, иллюстрирующий шаги 1–6 выше](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/ChangeFeedProcessor)
* [Дополнительные примеры на GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/ChangeFeedProcessor)

Дополнительные сведения об использовании канала изменений с помощью пакета SDK см. в следующих ресурсах:

* [Пакет SDK для Azure Cosmos DB .NET: скачивание и заметки о выпуске](sql-api-sdk-dotnet.md)
