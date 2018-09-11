---
title: Создание приложения Java с помощью асинхронного пакета SDK Java для Azure Cosmos DB | Документация Майкрософт
description: В этом руководстве показано, как использовать учетные записи API Azure Cosmos DB SQL для хранения и доступа к данным с помощью асинхронного приложения Java.
keywords: руководство nosql, оперативная база данных, консольное приложение java
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: sngun
ms.openlocfilehash: faa213caf415f98c230af741822e17a511b6fe43
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43696301"
---
# <a name="build-a-java-application-by-using-azure-cosmos-db-async-java-sdk"></a>Создание приложения Java с помощью асинхронного пакета SDK Java для Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Node.js (версия 2)](sql-api-nodejs-get-started-preview.md) 
> 

Azure Cosmos DB — это многомодельная глобально распределенная база данных. В этом руководстве показано, как использовать учетные записи API Azure Cosmos DB SQL для хранения и доступа к данным с помощью асинхронного приложения Java. 

Мы рассмотрим:

* создание учетной записи Azure Cosmos DB и подключение к ней;
* Настройка решения
* создание коллекции;
* создание документов JSON;
* выполнение запросов к коллекции;

А теперь приступим к работе!

## <a name="prerequisites"></a>Предварительные требования
Вам потребуются:

* Активная учетная запись Azure. Если у вас ее нет, зарегистрируйте [бесплатную учетную запись](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Комплект разработчика Java (JDK) 8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Maven](http://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. Создание учетной записи Azure Cosmos DB
Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к [клонированию проекта GitHub](#GitClone). При использовании эмулятора Azure Cosmos DB выполните действия, описанные в [этой статье](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [клонированию проекта GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Этап 2. Клонирование проекта GitHub
[Чтобы приступить к работе с Azure Cosmos DB и Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started), сначала клонируйте репозиторий GitHub. Например, чтобы получить образец проекта в локальной среде, в локальном каталоге выполните следующую команду.

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started.git

cd azure-cosmos-db-sql-api-async-java-getting-started
cd azure-cosmosdb-get-started

```
В этом каталоге находится файл проекта `pom.xml` и папка с исходным кодом Java, `src/main/java/com/microsoft/azure/cosmosdb/sample`. Кроме того, здесь также находится файл `Main.java` с инструкциями по выполнению простых операций с Azure Cosmos DB, таких как создание документов и запрос данных в коллекции. В файле `pom.xml` содержится зависимость от [пакета Java SDK для Azure Cosmos DB в Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-documentdb</artifactId>
  <version>2.0.0</version>
</dependency>
```

## <a id="Connect"></a>Шаг 3. Подключение к учетной записи Azure Cosmos DB
Далее вернитесь на [портал Azure](https://portal.azure.com), чтобы получить конечную точку и первичный главный ключ. Конечная точка и первичный ключ Azure Cosmos DB позволяют приложению предоставлять данные о расположении, в котором будет устанавливаться подключение, делая подключение вашего приложения доверенным для Azure Cosmos DB. Файл `AccountSettings.java` содержит первичный ключ и значения универсального кода ресурса (URI). 

На портале Azure перейдите к учетной записи Azure Cosmos DB, а затем щелкните **Ключи**. Скопируйте на портале значения полей URI и "Первичный ключ" и вставьте их в файл `AccountSettings.java`. 

```java
public class AccountSettings 
{
  // Replace MASTER_KEY and HOST with values from your Azure Cosmos DB account.
    
  // <!--[SuppressMessage("Microsoft.Security", "CS002:SecretInNextLine")]-->
  public static String MASTER_KEY = System.getProperty("ACCOUNT_KEY", 
          StringUtils.defaultString(StringUtils.trimToNull(
          System.getenv().get("ACCOUNT_KEY")), "<Fill your Azure Cosmos DB account key>"));

  public static String HOST = System.getProperty("ACCOUNT_HOST",
           StringUtils.defaultString(StringUtils.trimToNull(
           System.getenv().get("ACCOUNT_HOST")),"<Fill your Azure Cosmos DB URI>"));
}
```

![Снимок экрана портала Azure в ходе работы с руководством по NoSQL при создании консольного приложения Java. Отображается учетная запись Azure Cosmos DB со следующими выделенными элементами: активный концентратор, кнопка "Ключи" в колонке учетной записи Azure Cosmos DB, а также значение универсального кода ресурса (URI), первичный и вторичный ключи в колонке "Ключи".][keys]

## <a name="step-4-initialize-the-client-object"></a>Шаг 4. Инициализация клиентского объекта
Инициализация клиентского объекта с помощью значений узла универсального кода ресурса (URI) и первичного ключа, определенных в файле "AccountSettings.java"

```java
client = new AsyncDocumentClient.Builder()
         .withServiceEndpoint(AccountSettings.HOST)
         .withMasterKey(AccountSettings.MASTER_KEY)
         .withConnectionPolicy(ConnectionPolicy.GetDefault())
         .withConsistencyLevel(ConsistencyLevel.Session)
         .build();
```

## <a id="CreateDatabase"></a>Шаг 5. Создание базы данных

[Базу данных](sql-api-resources.md#databases) Azure Cosmos DB можно создать с помощью метода CreateDatabaseIfNotExists() класса DocumentClient. База данных представляет собой логический контейнер для хранения документов JSON, разделенных между коллекциями.

```java
private void createDatabaseIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if database " + databaseName + " exists.");

    String databaseLink = String.format("/dbs/%s", databaseName);

    Observable<ResourceResponse<Database>> databaseReadObs = client.readDatabase(databaseLink, null);

    Observable<ResourceResponse<Database>> databaseExistenceObs = databaseReadObs
                .doOnNext(x -> {System.out.println("database " + databaseName + " already exists.");}).onErrorResumeNext(e -> {
   // if the database doesn't already exists
   // readDatabase() will result in 404 error
   if (e instanceof DocumentClientException) 
   {
       DocumentClientException de = (DocumentClientException) e;
       // if database 
       if (de.getStatusCode() == 404) {
       // if the database doesn't exist, create it.
       System.out.println("database " + databaseName + " doesn't existed," + " creating it...");
       Database dbDefinition = new Database();
       dbDefinition.setId(databaseName);
       return client.createDatabase(dbDefinition, null);
     }
     }

    // some unexpected failure in reading database happened.
    // pass the error up.
    System.err.println("Reading database " + databaseName + " failed.");
        return Observable.error(e);     
    });

   // wait for completion
   databaseExistenceObs.toCompletable().await();

   System.out.println("Checking database " + databaseName + " completed!\n");
}
```

## <a id="CreateColl"></a>Шаг 6. Создание коллекции

> [!WARNING]
> Элемент **createCollection** создает коллекцию с зарезервированной пропускной способностью и соответствующей ценой. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 
Коллекцию можно создать, используя метод createDocumentCollectionIfNotExists() класса DocumentClient. Коллекция представляет собой контейнер документов JSON и связанную с ними логику в виде приложения JavaScript.

```java
private void createDocumentCollectionIfNotExists() throws Exception 
{
    
    writeToConsoleAndPromptToContinue("Check if collection " + collectionName + " exists.");

    // query for a collection with a given id
    // if it exists nothing else to be done
    // if the collection doesn't exist, create it.

    String databaseLink = String.format("/dbs/%s", databaseName);

    // we know there is only single page of result (empty or with a match)
    client.queryCollections(databaseLink, new SqlQuerySpec("SELECT * FROM r where r.id = @id", 
            new SqlParameterCollection(new SqlParameter("@id", collectionName))), null).single() 
            .flatMap(page -> {
            if (page.getResults().isEmpty()) {
             // if there is no matching collection create the collection.
             DocumentCollection collection = new DocumentCollection();
             collection.setId(collectionName);
             System.out.println("Creating collection " + collectionName);

             return client.createCollection(databaseLink, collection, null);
            } 
            else {
              // collection already exists, nothing else to be done.
              System.out.println("Collection " + collectionName + "already exists");
              return Observable.empty();
            }
      }).toCompletable().await();

 System.out.println("Checking collection " + collectionName + " completed!\n");
    }
```

## <a id="CreateDoc"></a>Шаг 7. Создание документов JSON

[Документ](sql-api-resources.md#documents) можно создать с помощью метода createDocument класса DocumentClient. Документы относятся к пользовательскому (произвольному) содержимому JSON. Теперь мы можем добавить один или несколько документов. Файл "src/main/java/com/microsoft/azure/cosmosdb/sample/Families.java" определяет семейство документов JSON 

```java
public static Family getJohnsonFamilyDocument() {
     Family andersenFamily = new Family();
     andersenFamily.setId("Johnson" + System.currentTimeMillis());
     andersenFamily.setLastName("Johnson");

      Parent parent1 = new Parent();
      parent1.setFirstName("John");

      Parent parent2 = new Parent();
      parent2.setFirstName("Lili");

      return andersenFamily;
    }
```

## <a id="Query"></a>Шаг 8. Запрос ресурсов Azure Cosmos DB

Azure Cosmos DB поддерживает [полнофункциональные запросы](sql-api-sql-query.md) к документам JSON, хранящимся в каждой коллекции. Ниже приведен образец кода, который позволяет запросить документы в Azure Cosmos DB, используя синтаксис SQL с методом [queryDocuments](/java/api/com.microsoft.azure.cosmosdb.rx._async_document_client.querydocuments).

```java
private void executeSimpleQueryAsyncAndRegisterListenerForResult(CountDownLatch completionLatch) 
{
    // Set some common query options
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setMaxItemCount(100);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(collectionLink,
           "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    queryObservable.subscribe(queryResultPage -> {
            System.out.println("Got a page of query result with " +
            queryResultPage.getResults().size() + " document(s)"
            + " and request charge of " + queryResultPage.getRequestCharge());
    },
   // terminal error signal
        e -> {
          e.printStackTrace();
          completionLatch.countDown();
        },

   // terminal completion signal
         () -> {
            completionLatch.countDown();
         });
}
```

## <a id="Run"></a>Этап 9. Запуск консольного приложения Java
Чтобы запустить приложение из консоли, перейдите в папку проекта и выполните компиляцию с помощью Maven:

```bash
mvn package
```

Команда `mvn package` позволяет скачать последнюю версию библиотеки Azure Cosmos DB из Maven и возвращает `GetStarted-0.0.1-SNAPSHOT.jar`. Затем запустите приложение, выполнив следующую команду:

```bash
mvn exec:java -DACCOUNT_HOST=<YOUR_COSMOS_DB_HOSTNAME> -DACCOUNT_KEY= <YOUR_COSMOS_DB_MASTER_KEY>
```
Поздравляем! Вы завершили работу с руководством по NoSQL и создали работающее консольное приложение Java.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о создании веб-приложения Java См. сведения в статье о [создании веб-приложения Java с использованием Azure Cosmos DB](sql-api-java-application.md).
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
