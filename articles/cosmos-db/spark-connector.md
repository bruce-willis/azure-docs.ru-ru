---
title: Подключение Apache Spark к Azure Cosmos DB | Документация Майкрософт
description: Сведения о соединителе Spark для Azure Cosmos DB, который позволяет подключать Apache Spark к Azure Cosmos DB. Вы можете выполнять распределенные агрегирования в мультитенантной, глобально распределенной системе баз данных от корпорации Майкрософт.
keywords: apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: 57ad621006a79148aaa6e9435d6ede446539c648
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714759"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Ускорение аналитики больших данных с помощью соединителя Apache Spark для Azure Cosmos DB
 
Соединитель Apache Spark для Azure Cosmos DB дает возможность использовать Azure Cosmos DB в качестве входных или выходных данных для заданий Apache Spark. Подключение [Spark](http://spark.apache.org/) к [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ускоряет возможность решения проблем обработки и анализа данных. Вы можете использовать Azure Cosmos DB для быстрого сохранения и запроса данных. Соединитель эффективно использует собственные управляемые индексы Azure Cosmos DB. Эти индексы позволяют использовать обновляемые столбцы при выполнении анализа, применять фильтры предиката к быстро меняющимся глобально распределенным данным, начиная от Интернета вещей и заканчивая сценариями аналитики и обработки и анализа данных.

Дополнительные сведения о соединителе см. в этом видео:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>Компоненты соединителя

Соединитель Spark для Azure Cosmos DB содержит следующие компоненты.

* [Azure Cosmos DB](http://documentdb.com) позволяет вам подготавливать и эластично масштабировать пропускную способность и ресурсы хранилища в любом количестве географических регионов.  

* [Apache Spark](http://spark.apache.org/) — это высокопроизводительная подсистема обработки с открытым исходным кодом, призванная ускорить разработку, повысить удобство использования и реализовать сложную аналитику.  

* [Кластер Apache Spark в Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) позволяет выполнять задания Spark в кластере Spark.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Подключение Apache Spark к Azure Cosmos DB

Существует два способа подключения Apache Spark и Azure Cosmos DB:

- С помощью [пакета SDK SQL для Python в Azure Cosmos DB](https://github.com/Azure/azure-documentdb-python), представляющего соединитель на основе Python, который также называют *pyDocumentDB*.  

- [С помощью пакета SDK SQL для Java в Azure Cosmos DB](https://github.com/Azure/azure-documentdb-java), представляющего соединитель на основе Java.


**Поддерживаемые версии**

| Компонент | Version (версия) |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Версия среды выполнения Azure Databricks | > 3.4 |
| Пакет SDK SQL для Java в Azure Cosmos DB | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Подключение с помощью Python или пакета SDK pydocumentdb

На следующей схеме приведена реализация архитектуры пакета SDK pyDocumentDB:

![Схема потока данных из Spark в Azure Cosmos DB через базу данных pyDocumentDB](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Поток данных

Поток данных в pydocumentdb выглядит следующим образом.

* Главный узел Spark подключается к узлу шлюза Azure Cosmos DB через pyDocumentDB. Вы указываете только подключения Spark и Azure Cosmos DB. Подключения в соответствующих главном узле и узле шлюза прозрачны.  

* Узел шлюза делает запрос к Azure Cosmos DB. Далее запрос отправляется к разделам коллекции в узлах данных. Ответ на эти запросы возвращается в узел шлюза, а результирующий набор — в главный узел Spark.  

* Последующие запросы (например, к таблице данных Spark) отправляются в рабочие узлы Spark для обработки.  

Взаимодействие между Spark и Azure Cosmos DB происходит в пределах главного узла Spark и узлов шлюза Azure Cosmos DB. Скорость запросов зависит от транспортного уровня между двумя этими узлами.

Чтобы подключить Spark к Azure Cosmos DB с помощью пакета SDK для pyDocumentDB, выполните следующие действия.

1. Создайте [рабочую область Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) и [кластер Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Среда выполнения Azure Databricks версии 4.0 включает Apache Spark 2.3.0 и Scala 2.11 в этой рабочей области.  

2. После создания и запуска кластера выберите **Рабочая область** > **Создать** > **Библиотека**.  
3. В диалоговом окне создания библиотеки в качестве источника выберите **Upload Python Egg or PyPi** (Загрузить ZIP-архивы, содержащие Python-пакет или PyPi), а в качестве имени укажите **pydocumentdb** и выберите **Install Library** (Установить библиотеку). Пакет SDK для pyDocumentdb можно найти и установить, так как он уже был опубликован в пакетах pip. 

   ![Снимок экрана диалогового окна создания библиотеки](./media/spark-connector/create-library.png)

4. После установки библиотеки, присоедините ее к кластеру, созданному ранее.  

5. Выберите **Рабочая область** > **Создать** > **Записная книжка**.  

6. В диалоговом окне **создания записной книжки** введите понятное имя, а в качестве языка выберите **Python**. Из раскрывающегося списка выберите кластер, который был создан ранее, а затем щелкните **Создать**.  

7. Запустите несколько запросов Spark, используя пример данных о полетах, размещенный в учетной записи Azure Cosmos DB "doctorwho". (Эта учетная запись является общедоступной.) HTML-версия записной книжки находится в репозитории GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Загрузите файлы репозитория и перейдите в `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html`. Вы можете импортировать записную книжку в свою учетную запись Azure Databricks и запустить ее. В следующем разделе указаны подробные сведения о функциях блоков кода.

В следующем фрагменте кода указано, как в контексте Spark можно импортировать пакет SDK для pyDocumentDB и запустить запрос. Во фрагменте кода указано, что пакет SDK для pydocumentdb содержит параметры подключения, которые необходимы для соединения с учетной записью Azure Cosmos DB. Он импортирует необходимые библиотеки, настраивает главный ключ и узел для создания клиента Azure Cosmos DB (pydocumentdb.document_client).


```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]

# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)

```

Далее вы можете выполнить запросы. Следующий фрагмент кода подключается к коллекции airports.codes в учетной записи "doctorwho" и выполняет запрос для извлечения городов с аэропортами в штате Вашингтон. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

После выполнения запроса в результате отобразится значение "query_iterable.QueryIterable", преобразованное в список Python. Этот список, в свою очередь, преобразуется в кадр данных Spark. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

### <a name="considerations-when-using-pydocumentdb-sdk"></a>Рекомендации при использовании пакета SDK для pydocumentdb
Подключение Spark к Azure Cosmos DB с помощью пакета SDK pyDocumentDB рекомендуется в следующих сценариях:

* Необходимо использовать Python.  

* Необходимо вернуть относительно небольшой набор результатов из Azure Cosmos DB в Spark. Обратите внимание, что базовый набор данных в Azure Cosmos DB может быть достаточно большим, что вызывает необходимость применения фильтров или запуска фильтров предиката для источника Azure Cosmos DB.

## <a name="connect-by-using-the-java-sdk"></a>Подключение с помощью пакета SDK для Java

На следующем рисунке приведена реализация архитектуры пакета SDK SQL для Java в Azure Cosmos DB и перемещение данных между рабочими узлами Spark.

![Схема потока данных в соединителе Spark для Azure Cosmos DB](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Поток данных

Поток данных пакета SDK для Java выглядит следующим образом.

* Главный узел Spark подключается к узлу шлюза Azure Cosmos DB, чтобы получить схему секционирования. Вы указываете только подключения Spark и Azure Cosmos DB. Подключения в соответствующих главном узле и узле шлюза прозрачны.  

* Эта информация возвращается в главный узел Spark. На этом этапе вы можете проанализировать запрос, чтобы определить разделы (и их расположения) в Azure Cosmos DB, к которым требуется получить доступ.  

* Эта информация передается в рабочие узлы Spark.  

* Таким образом, рабочие узлы Spark подключаются к разделам Azure Cosmos DB, чтобы извлечь и вернуть данные в разделы рабочих узлов Spark.  

Взаимодействие между Spark и Azure Cosmos DB происходит значительно быстрее, так как данные перемещаются между рабочими узлами Spark и узлами данных Azure Cosmos DB (разделами). В этом примере вы узнаете, как отправлять некоторые из образцов данных Twitter в учетную запись Azure Cosmos DB и как с их помощью выполнять запросы Spark. Чтобы записать образцы данных Twitter в Azure Cosmos DB, выполните следующие действия.

1. Создайте [учетную запись API SQL Azure Cosmos DB](create-sql-api-dotnet.md#create-a-database-account) и [добавьте коллекцию](create-sql-api-dotnet.md#add-a-collection) к учетной записи.  

2. Скачайте из GitHub пример [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed). Вы используете этот веб-канал, чтобы записать образцы данных Twitter в Azure Cosmos DB.  

3. Откройте командную строку и установите модули Tweepy и pyDocumentdb с помощью следующей команды.

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Извлеките содержимое образца веб-канала Twitter и откройте файл "config.py". Обновите значения masterKey, host, databaseId, collectionId и preferredLocations.  

5. Перейдите в `http://apps.twitter.com/` и зарегистрируйте сценарий веб-канала Twitter в качестве нового приложения. **Ключ клиента, секрет клиента, маркер доступа и секрет маркера доступа** будут предоставлены после выбора имени и приложения. Чтобы предоставить программный доступ к Twitter, скопируйте эти значения и обновите их в файле "config.py".   

6. Сохраните файл config.py. Откройте командную строку и запустите приложение Python с помощью следующей команды.

   ```bash
   Python driver.py
   ```

7. Перейдите к коллекции Azure Cosmos DB на портале и убедитесь, что данные, которые поступают от Twitter, записываются в коллекцию.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Поиск и подключение пакета SDK для Java к кластеру Spark

1. Создайте [рабочую область Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) и [кластер Spark](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Среда выполнения Azure Databricks версии 4.0 включает Apache Spark 2.3.0 и Scala 2.11 в этой рабочей области.  

2. После создания и запуска кластера выберите **Рабочая область** > **Создать** > **Библиотека**.  

3. В диалоговом окне **создания библиотеки** выберите **координату Maven** как источник. Укажите значение координаты **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**, а затем выберите **Создать библиотеку**. Зависимости Maven устранены и пакет добавляется в рабочую область. В приведенном выше формате координат Maven значение 2.3.0 соответствует версии Spark, 2.11 — версии Scala, а 1.2.0 — версии соединителя Azure Cosmos DB. 

4. После установки библиотеки, присоедините ее к кластеру, созданному ранее. 

В этой статье показано использование соединителя Spark пакета SDK для Java в следующих сценариях.

* Чтение данных Twitter из Azure Cosmos DB.  

* Чтение данных Twitter, которые передаются в Azure Cosmos DB.  

* Запись данных Twitter в Azure Cosmos DB. 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Чтение данных Twitter из Azure Cosmos DB
 
В этом разделе описано выполнение запросов Spark на чтение пакета данных Twitter из Azure Cosmos DB. HTML-версия записной книжки находится в репозитории GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Загрузите файлы репозитория и перейдите в `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html`. Вы можете импортировать записную книжку в свою учетную запись Azure Databricks и обновить значения универсального кода ресурса (URI) учетной записи, основного ключа, базы данных и коллекции. Вы можете запустить записную книжку или создать ее следующим образом:

1. Перейдите к учетной записи Azure Databricks и последовательно выберите **Рабочая область** > **Создать** > **Записная книжка**. 

2. В диалоговом окне **создания записной книжки** введите понятное имя, а в качестве языка выберите **Python**. Из раскрывающегося списка выберите кластер, который был создан ранее, а затем щелкните **Создать**.  

3. Чтобы подключиться к учетной записи, обновите значения конечной точки, главного ключа, базы данных и коллекции. Прочитать твиты можно с помощью команды spark.read.format().

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. Чтобы получить количество твитов с разными хэш-тегами из кэшированных данных, необходимо выполнить запрос. 

   ```python
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

Пакет SDK для Java поддерживает следующие значения для сопоставления конфигурации. 

|Параметр  |ОПИСАНИЕ  |
|---------|---------|
|query_maxdegreeofparallelism  | Установка числа параллельных операций, выполняемых на стороне клиента во время выполнения параллельных запросов. Если ему соответствует число больше 0, система автоматически ограничивает число параллельных операций, которые подлежат буферизации, на указанное значение. Если оно имеет значение меньше 0, система автоматически определяет число параллельных операций для выполнения. После того как соединитель сопоставит все секции коллекции с исполнителями, это значение перестанет оказывать воздействие на операцию чтения.        |
|query_maxbuffereditemcount     |    Установка максимального количества элементов, которые можно поместить в буфер на стороне клиента во время выполнения параллельных запросов. Если ему соответствует число больше 0, система автоматически ограничивает число элементов, которые подлежат буферизации, на указанное значение. Если ему соответствует число меньше 0, система автоматически определяет число элементов, которые подлежат буферизации.     |
|query_enablescan    |   Установка параметра, отвечающего за включение сканирования запросов, которые не удалось обработать, так как из запрошенных путей было исключено индексирование.       |
|query_disableruperminuteusage  |  Отключение производительности единицы запроса за минуту для обслуживания запроса, если будет исчерпана обычная подготовленная единица запроса за секунду.       |
|query_emitverbosetraces   |   Установка параметра, позволяющего запросам передавать подробные трассировки для исследования.      |
|query_pagesize  |   Установка размера страницы результата запроса для каждого запроса. Чтобы оптимизировать пропускную способность, используйте страницы большого размера, чтобы сократить число круговых путей, необходимых для получения результатов.      |
|query_custom  |  Установка запроса Azure Cosmos DB, который при получении данных из Azure Cosmos DB будет перезаписывать запросы по умолчанию. Обратите внимание, когда указано это значение, оно также будет использоваться вместо запроса с принудительно переданными предикатами.     |

В зависимости от сценария вы должны использовать разные значения конфигурации для оптимизации производительности и пропускной способности. Обратите внимание, что в настоящее время ключ конфигурации не учитывает регистр, а значение конфигурации всегда является строкой.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Чтение данных Twitter, которые передаются в Azure Cosmos DB

В этом разделе описано выполнение запросов Spark на чтение веб-канала изменений в потоковых данных Twitter. При выполнении запросов из этого раздела убедитесь, что приложение веб-канала Twitter запущено и оно наполняет Azure Cosmos DB данными. HTML-версия записной книжки находится в репозитории GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Загрузите файлы репозитория и перейдите в `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html`. Вы можете импортировать записную книжку в свою учетную запись Azure Databricks и обновить значения универсального кода ресурса (URI) учетной записи, основного ключа, базы данных и коллекции. Вы можете запустить записную книжку или создать ее следующим образом:

1. Перейдите к учетной записи Azure Databricks и последовательно выберите **Рабочая область** > **Создать** > **Записная книжка**.  

2. В диалоговом окне **создания записной книжки** введите понятное имя, а в качестве языка выберите **Scala**. Из раскрывающегося списка выберите кластер, который был создан ранее, а затем щелкните **Создать**.  

3. Чтобы подключиться к учетной записи, обновите значения конечной точки, главного ключа, базы данных и коллекции.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Начните считывать данные из канала изменений в виде потока, используя команду spark.readStream.format().

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Запустите потоковую передачу запросов в консоль:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

Пакет SDK для Java поддерживает следующие значения для сопоставления конфигурации.

|Параметр  |ОПИСАНИЕ  |
|---------|---------|
|readchangefeed   |  Указывает, что содержимое коллекции извлекается из веб-канала изменений CosmosDB. По умолчанию для этого параметра используется значение false.       |
|changefeedqueryname |   Настраиваемая строка для определения запроса. Соединитель отдельно отслеживает маркеры продолжения коллекции для различных запросов веб-канала. Если значение readchangefeed — "true", то эта конфигурация является обязательной и она не может принимать пустое значение.      |
|changefeedcheckpointlocation  |   Путь к локальному хранилищу файлов, где в случае ошибок узла будут храниться маркеры продолжения.      |
|changefeedstartfromthebeginning  |  Определяет, должен ли канал изменений начинаться с начала (true) или с текущей точки (false). По умолчанию он начинается с текущего положения (false).       |
|rollingchangefeed  |   Логическое значение, указывающее, должен ли использоваться веб-канал изменений последнего запроса. Если значение по умолчанию — "false", то это значит, что изменения будут учитываться при первом чтении коллекции.      |
|changefeedusenexttoken  |   Логическое значение поддерживаемое в сценариях обработки сбоев. Оно используется для указания того, что текущие изменения в пакете веб-канала были корректно обработаны. Устойчивый распределенный набор данных должен использовать следующие маркеры продолжения для получения последующих пакетов изменений.      |
| InferStreamSchema | Логическое значение, указывающее, следует ли выбрать схему потоковой передачи данных в качестве образца в начале потоковой передачи. По умолчанию установлено значение true. Если значение параметра соответствует "true", а схема потоковой передачи данных изменится после выбора данных, то недавно добавленные свойства будут удалены в кадре данных потоковой передачи. <br/><br/> Если установить значение этого параметра "false", то кадр потоковой передачи данных будет бессхемным. В этом режиме тексты документов, считываемых из веб-канала изменений Azure Cosmos DB, помещаются в свойство "body" в итоговом кадре потоковой передачи данных вместе со значениями свойств системы.
 |

### <a name="connection-settings"></a>Параметры подключения

Пакет SDK для Java поддерживает следующие параметры подключения.

|Параметр  |ОПИСАНИЕ  |
|---------|---------|
|connectionmode   |  Установка режима подключения, который будет использоваться внутренним DocumentClient для установки связи с Azure Cosmos DB. Допустимыми значениями являются **DirectHttps** (значение по умолчанию) и **Gateway**. Режим подключения DirectHttps направляет запросы непосредственно к разделам CosmosDB и предоставляет определенные преимущества при задержке.       |
|connectionmaxpoolsize   |  Установка значения размера пула подключений, который используется внутренним DocumentClient. По умолчанию используется значение 100.       |
|connectionidletimeout  |  Установка времени ожидания для неиспользуемых подключений в секундах. Значение по умолчанию — 60.       |
|query_maxretryattemptsonthrottledrequests    |  Установка максимального числа попыток. Это значение используется в случае, когда возникает ошибка запроса из-за ограничения скорости на стороне клиента. Если оно не задано, то количество попыток повтора по умолчанию равно 1000.       |
|query_maxretrywaittimeinseconds   |  Установка максимального времени повтора в секундах. По умолчанию значение этого параметра равно 1000 секунд.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Запись данных Twitter в Azure Cosmos DB 

В этом разделе описано выполнение запросов Spark на запись пакета данных Twitter в новую коллекцию в той же базе данных. HTML-версия записной книжки находится в репозитории GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Загрузите файлы репозитория и перейдите в `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html`. Вы можете импортировать записную книжку в свою учетную запись Azure Databricks и обновить значения универсального кода ресурса (URI) учетной записи, основного ключа, базы данных и коллекции. Вы можете запустить записную книжку или создать ее следующим образом:

1. Перейдите к учетной записи Azure Databricks и последовательно выберите **Рабочая область** > **Создать** > **Записная книжка**.  

2. В диалоговом окне **создания записной книжки** введите понятное имя, а в качестве языка выберите **Scala**. Из раскрывающегося списка выберите кластер, который был создан ранее, а затем щелкните **Создать**.  

3. Чтобы подключиться к коллекции базы данных для чтения и записи данных Twitter, обновите значения конечной точки, главного ключа, базы данных и коллекции.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. Чтобы получить количество твитов с разными хэш-тегами из кэшированных данных, необходимо выполнить запрос. 

   ```scala
   %sql
   select hashtags.text, count(distinct id) as tweets
   from (
   select 
     explode(hashtags) as hashtags,
     id
   from tweets
   ) a
   group by hashtags.text
   order by tweets desc
   limit 10
   ```

5. Создайте кадр данных твитов тегов и сохраните данные в новой коллекции. Выполнив следующий код, можно вернуться на портал и убедиться, что данные записываются в коллекцию. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

Пакет SDK для Java поддерживает следующие значения для сопоставления конфигурации.

|Параметр  |ОПИСАНИЕ  |
|---------|---------|
| BulkImport | Логическое значение, указывающее, следует ли импортировать данные с помощью библиотеки BulkExecutor. По умолчанию установлено значение true. |
|WritingBatchSize  |   Указывает размер пакета, который будет использован при записи данных в коллекцию Azure Cosmos DB. <br/><br/> Если значение параметра BulkImport установлено как "true", то параметр WritingBatchSize указывает размер пакета документов, предоставленных в качестве входных данных для интерфейса API importAll библиотеки BulkExecutor. По умолчанию установлено значение 100 000. <br/><br/> Если значение параметра BulkImport — "false", то параметр WritingBatchSize указывает размер пакета, который будет использован при записи в коллекцию Azure Cosmos DB. Запросы createDocument или upsertDocument будут асинхронно отправлены соединителем в пакет. Чем больше размер пакета, тем большей пропускной способности можно достичь. Это правило работает до тех пор, пока доступны ресурсы кластера. С другой стороны для ограничения скорости и уменьшения потребления единицы запросов можно указать пакеты меньшего размера. Размер пакета записи по умолчанию — 500.  |
|Upsert   |  Строка логического значения указывает, следует ли использовать upsertDocument вместо CreateDocument при записи в коллекцию Azure Cosmos DB.   |
| WriteThroughputBudget |  Целочисленная строка, представляющая количество единиц запросов за секунду, которые необходимо выделить для задания Spark по массовому приему из общей пропускной способности, выделенной для коллекции. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Запись данных, которые передаются из Twitter в Azure Cosmos DB 

В этом разделе описано выполнение запросов Spark на запись веб-канала изменений потоковых данных Twitter в новую коллекцию в той же базе данных. HTML-версия записной книжки находится в репозитории GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master). Загрузите файлы репозитория и перейдите в `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html`. Вы можете импортировать записную книжку в свою учетную запись Azure Databricks и обновить значения универсального кода ресурса (URI) учетной записи, основного ключа, базы данных и коллекции. Вы можете запустить записную книжку или создать ее следующим образом:

1. Перейдите к учетной записи Azure Databricks и последовательно выберите **Рабочая область** > **Создать** > **Записная книжка**.  

2. В диалоговом окне **создания записной книжки** введите понятное имя, а в качестве языка выберите **Scala**. Из раскрывающегося списка выберите кластер, который был создан ранее, а затем щелкните **Создать**.  

3. Чтобы подключиться к коллекции базы данных для чтения и записи данных Twitter, обновите значения конечной точки, главного ключа, базы данных и коллекции.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Начните считывать данные из канала изменений в виде потока, используя команду spark.readStream.format().
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. Определите конфигурацию конечной коллекции и запустите задание потоковой передачи с помощью метода writeStream.format().

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
    ```

Пакет SDK для Java поддерживает следующие значения для сопоставления конфигурации.

|Параметр  |ОПИСАНИЕ  |
|---------|---------|
|Upsert   |  Строка логического значения указывает, следует ли использовать upsertDocument вместо CreateDocument при записи в коллекцию Azure Cosmos DB.   |
|checkpointlocation  |   Путь к локальному хранилищу файлов, где в случае ошибок узла будут храниться маркеры продолжения.   |
|WritingBatchSize  |  Указывает размер пакета, который будет использован при записи данных в коллекцию Azure Cosmos DB. Запросы createDocument или upsertDocument будут асинхронно отправлены соединителем в пакет. Чем больше размер пакета, тем большей пропускной способности можно достичь. Это правило работает до тех пор, пока доступны ресурсы кластера. С другой стороны для ограничения скорости и уменьшения потребления единицы запросов можно указать пакеты меньшего размера. Размер пакета записи по умолчанию — 500.  |


### <a name="considerations-when-using-java-sdk"></a>Рекомендации при использовании пакета SDK для Java

Рекомендуется применять следующие сценарии при подключении Spark к Azure Cosmos DB с помощью пакета SDK для Java.

* Вам нужно использовать Python или Scala.  

* Необходимо передать большой объем данных между Apache Spark и Azure Cosmos DB. Пакет SDK для Java работает лучше, чем pyDocumentDB. Чтобы получить представление о разнице в производительности запросов, см. сведения в статье о [тестовых выполнениях запросов](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>Дополнительная информация

Если у вас еще нет соединителя Spark для Azure Cosmos DB, скачайте его из репозитория GitHub [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark). Изучите следующие дополнительные ресурсы в репозитории:

* [Примеры агрегирований](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Примеры скриптов и записных книжек](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Дополнительные сведения см. в [руководстве по Apache Spark SQL, таблицам и наборам данных](http://spark.apache.org/docs/latest/sql-programming-guide.html), а также статье [Краткое руководство по созданию кластера Spark в HDInsight с помощью шаблона](../hdinsight/spark/apache-spark-jupyter-spark-sql.md).
