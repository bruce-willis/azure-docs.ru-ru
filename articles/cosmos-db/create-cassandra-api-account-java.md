---
title: Создание учетной записи API Cassandra для Azure Cosmos DB, базы данных и таблицы с помощью приложения Java
description: В этой статье показано, как создать учетную запись API Cassandra, добавить базу данных (также называемую пространством ключей) и таблицу в эту учетную запись с помощью приложения Java.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
services: cosmos-db
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: af25c52d1300a2daed3542c5cb1db080c505f715
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953129"
---
# <a name="create-an-azure-cosmos-db-cassandra-api-account-database-and-a-table-by-using-a-java-application"></a>Создание учетной записи API Cassandra для Azure Cosmos DB, базы данных и таблицы с помощью приложения Java

В этом руководстве описывается, как использовать приложение Java для создания учетной записи API Cassandra в Azure Cosmos DB, добавить базу данных (также называемую пространством ключей) и добавить таблицу. Приложение Java использует [драйвер Datastax Java](https://github.com/datastax/java-driver), чтобы создать базу данных пользователя, которая содержит такие сведения, как идентификатор пользователя, имя пользователя, город пользователя.  

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Создание учетной записи базы данных Cassandra
> * Получение строки подключения к учетной записи
> * Создание проекта Maven и зависимостей
> * Добавление базы данных и таблицы
> * Запуск приложения

## <a name="prerequisites"></a>Предварительные требования 

* Если у вас еще нет подписки Azure,  [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , прежде чем начинать работу. Кроме того, можно воспользоваться  [бесплатной пробной версией Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)  без подписки Azure, оплаты и каких-либо обязательств. 

* Получите последнюю версию [пакета средств разработки Java (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 

* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив [Maven](http://maven.apache.org/) 
  - В Ubuntu выполните команду  `apt-get install maven` , чтобы установить Maven. 

## <a name="create-a-database-account"></a>Создание учетной записи базы данных 

1. Войдите на  [портал Azure](https://portal.azure.com/). 

2. Последовательно выберите  **Создать ресурс** > **Базы данных** > **Azure Cosmos DB**. 

3. На панели  **Новая учетная запись**  введите параметры для новой учетной записи Azure Cosmos DB. 

   |Параметр   |Рекомендуемое значение  |ОПИСАНИЕ  |
   |---------|---------|---------|
   |ИД   |   Укажите уникальное имя    | Введите уникальное имя для идентификации этой учетной записи Azure Cosmos DB. <br/><br/>Так как элемент cassandra.cosmosdb.azure.com добавляется к указанному идентификатору для создания точки контакта, используйте уникальный, но узнаваемый идентификатор.         |
   |API    |  Cassandra   |  API определяет тип учетной записи, которую нужно создать. <br/> Выберите **Cassandra**, так как в этой статье вы создадите базу данных с широкими столбцами, к которой можно отправлять запросы с помощью синтаксиса CQL.  |
   |Подписка    |  Ваша подписка        |  Выберите подписку Azure, которую планируете использовать для этой учетной записи Azure Cosmos DB.        |
   |Группа ресурсов   | Введите имя.    |  Выберите  **Создать** и введите новое имя группы ресурсов для учетной записи. Для удобства можно использовать то же имя, которое присвоено идентификатору.    |
   |Расположение    |  Выберите ближайший к пользователям регион    |  Выберите географическое расположение, в котором будет размещена учетная запись Azure Cosmos DB. Используйте ближайшее к пользователям расположение, чтобы предоставить им максимально быстрый доступ к данным.    |

   ![Создание учетной записи с помощью портала](./media/create-cassandra-api-account-java/create-account.png)

4. Затем выберите  **Создать**. <br/>Создание учетной записи займет несколько минут. После создания ресурса вы увидите уведомление **об успешном развертывании** в правом углу портала.

## <a name="get-the-connection-details-of-your-account"></a>Получение сведений о подключении учетной записи  

Получите сведения о строке подключения с портала Azure и скопируйте их в файл конфигурации Java. Так вы обеспечите обмен данными между приложением и размещенной базой данных. 

1. На  [портале Azure](http://portal.azure.com/) перейдите к учетной записи Cosmos DB. 

2. Откройте панель  **Строка подключения**.  

3. Скопируйте значения **CONTACT POINT**, **PORT**, **USERNAME** и **PRIMARY PASSWORD**, которые пригодятся вам на следующих этапах.

## <a name="create-maven-project-dependencies-and-utility-classes"></a>Создайте проект Maven, зависимости и служебные классы 

Пример проекта Java, используемый в этой статье, размещен в GitHub. Его можно загрузить из репозитория [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started). 

После загрузки файлов обновите строку подключения в файле `java-examples\src\main\resources\config.properties` и запустите его.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_host = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Кроме того, можно создать пример с нуля.  

1. Из терминала или командной строки создайте новый проект Maven с именем Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Найдите папку `cassandra-demo`. Откройте в текстовом редакторе созданный файл `pom.xml`. 

   Добавьте зависимости Cassandra datastax и создайте подключаемые модули, необходимые в проекте, как показано в файле [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml).  

3. В папке `cassandra-demo\src\main` создайте папку с именем `resources`.  В папке ресурсов добавьте файлы config.properties и log4j.properties:

   - Файл [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) хранит значения конечной точки и ключа подключения API Cassandra Azure Cosmos DB. 
   
   - Файл [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) определяет уровень ведения журнала, необходимый при взаимодействии с API Cassandra.  

4. Затем перейдите в папку `src/main/java/com/azure/cosmosdb/cassandra/`. В папке cassandra создайте вторую папку с именем `utils`. Новая папка хранит служебные классы, необходимые для подключения к учетной записи API Cassandra. 

   Добавьте класс [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) для создания кластера и открытия и закрытия сеансов Cassandra. Кластер подключается к API Cassandra для Azure Cosmos DB и возвращает сеанс для доступа. Используйте класс [Configurations](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) для чтения строки подключения из файла config.properties. 

5. Пример Java создает базу данных со сведениями о пользователе, такими как имя пользователя, идентификатор пользователя, город пользователя. Необходимо определить методы get и set для доступа к сведениям о пользователе в основной функции.
 
   Создайте класс [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) в папке `src/main/java/com/azure/cosmosdb/cassandra/` с методами get и set. 

## <a name="add-a-database-and-a-table"></a>Добавление базы данных и таблицы  

В этом разделе описывается добавление базы данных (пространства ключей) и таблицы с помощью языка запросов Cassandra (CQL). Для получения дополнительных сведений о синтаксисе CQL для этих команд см. синтаксис запросов [создание пространства ключей](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateKeyspace.html) и [создание таблицы](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateTable.html#cqlCreateTable). 

1. В папке `src\main\java\com\azure\cosmosdb\cassandra` создайте папку с именем `repository`. 

2. Затем создайте класс Java `UserRepository` и добавьте в него следующий код: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Найдите папку `src\main\java\com\azure\cosmosdb\cassandra` и создайте новую вложенную папку с именем `examples`.

4. Затем создайте класс Java `UserProfile`. Этот класс содержит основной метод, который вызывает методы createKeyspace и createTable, определенные ранее: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Запуск приложения 

1. Откройте командную строку или окно терминала. Вставьте следующий блок кода. 

   Этот код изменяет каталог (cd) для пути к папке, где создан проект. Затем он выполняет команду `mvn clean install`, чтобы создать файл `cosmosdb-cassandra-examples.jar` в целевой папке. Наконец, он запускает приложение Java.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Окно терминала отображает уведомления о создании пространства ключей и таблицы. 
   
2. Теперь на портале Azure откройте **обозреватель данных** и убедитесь, что пространство ключей и таблица созданы.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как создать учетную запись API Cassandra для Azure Cosmos DB, базу данных и таблицу с помощью приложения Java. Теперь вы можете перейти к следующей статье:

> [!div class="nextstepaction"]
> [Загрузка образца данных в таблицу API Cassandra](cassandra-api-load-data.md).
