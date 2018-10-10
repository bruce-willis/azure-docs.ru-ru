---
title: Краткое руководство. Использование API Cassandra с Java в Azure Cosmos DB
description: В этом руководстве показано, как использовать API Cassandra Azure Cosmos DB для создания приложения профиля с помощью портала Azure и Java.
services: cosmos-db
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.component: cosmosdb-cassandra
ms.custom: quick start connect, mvc
ms.devlang: java
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: fcb707f886b960335e69a6af3e485634ebb0a506
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974040"
---
# <a name="quickstart-build-a-cassandra-app-with-java-and-azure-cosmos-db"></a>Краткое руководство. Создание приложения Cassandra с помощью Java и Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [Java](create-cassandra-java.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

В этом руководстве показано, как с помощью Java и [API Cassandra](cassandra-introduction.md) Azure Cosmos DB создать приложение профиля в ходе клонирования примера с сайта GitHub. Кроме того, здесь показано, как создать учетную запись Azure Cosmos DB на веб-портале Azure.

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, таблицы, пары "ключ — значение" и базы данных графов, используя возможности глобального распределения и горизонтального масштабирования Azure Cosmos DB. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Кроме того, можно воспользоваться [бесплатной пробной версией Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) без подписки Azure, оплаты и каких-либо обязательств.

Кроме того, вам потребуется:

* [Комплект разработчика Java (JDK 1.7+)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * В Ubuntu выполните команду `apt-get install default-jdk`, чтобы установить JDK.
    * Обязательно настройте переменную среды JAVA_HOME так, чтобы она указывала на папку, в которой установлен пакет JDK.
* [Скачайте](http://maven.apache.org/download.cgi) и [установите](http://maven.apache.org/install.html) двоичный архив [Maven](http://maven.apache.org/).
    * В Ubuntu выполните команду `apt-get install maven`, чтобы установить Maven.
* [Git](https://www.git-scm.com/)
    * В Ubuntu выполните команду `sudo apt-get install git`, чтобы установить Git.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Прежде чем создавать базу данных документов, необходимо создать в Azure Cosmos DB учетную запись Cassandra.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь перейдем к работе с кодом. Клонируйте приложение API Cassandra с GitHub, укажите строку подключения и запустите это приложение. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте окно командной строки. Создайте папку с именем `git-samples`. Затем закройте командную строку.

    ```bash
    md "C:\git-samples"
    ```

2. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в новую папку для установки примера приложения.

    ```bash
    cd "C:\git-samples"
    ```

3. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Просмотр кода

Этот шаг не является обязательным. Если вы хотите узнать, как создавать ресурсы базы данных в коде, изучите приведенные ниже фрагменты кода. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string). Эти фрагменты кода взяты из файла `src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java`.  

* Здесь для Cassandra задаются узел, порт, имя пользователя, пароль и параметры SSL. Строку подключения вы можете получить на странице строки подключения на портале Azure.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* `cluster` подключается к API-интерфейсу Cassandra для Azure Cosmos DB и возвращает сеанс для доступа.

    ```java
    return cluster.connect();
    ```

Приведенные ниже фрагменты кода взяты из файла `src/main/java/com/azure/cosmosdb/cassandra/repository/UserRepository.java`.

* Создайте пространство ключей.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Создайте таблицу.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Вставьте пользовательские сущности, используя подготовленный объект инструкций.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Выполните запрос на получение информации обо всех пользователях.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Выполните запрос на получение информации об одном пользователе.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение. Данные строки подключения обеспечивают обмен данными между вашим приложением и размещенной базой данных.

1. На [портале Azure](http://portal.azure.com/) выберите **Строка подключения**. 

    ![Просмотрите и скопируйте имя пользователя на странице "Строка подключения" на портале Azure.](./media/create-cassandra-java/keys.png)

2. Вы можете использовать ![кнопку "Копировать"](./media/create-cassandra-java/copy.png) в правой части экрана, чтобы скопировать значение параметра CONTACT POINT.

3. Откройте файл `config.properties` в папке `C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples\src\main\resources`. 

3. Вставьте полученное на портале значение параметра CONTACT POINT вместо элемента `<Cassandra endpoint host>` в строке 2.

    Теперь строка 2 в файле config.properties будет выглядеть примерно так: 

    `cassandra_host=cosmos-db-quickstarts.cassandra.cosmosdb.azure.com`

3. Вернитесь на портал и скопируйте значение USERNAME. Вставьте полученное на портале значение USERNAME вместо элемента `<cassandra endpoint username>` в строке 4.

    Теперь строка 4 в файле config.properties будет выглядеть примерно так: 

    `cassandra_username=cosmos-db-quickstart`

4. Вернитесь на портал и скопируйте значение PASSWORD. Вставьте полученное на портале значение PASSWORD вместо элемента `<cassandra endpoint password>` в строке 5.

    Теперь строка 5 в файле config.properties будет выглядеть примерно так: 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Если вы хотите использовать определенный сертификат SSL, замените элемент `<SSL key store file location>` в строке 6 расположением своего SSL-сертификата. Если не указать это значение, будет использоваться сертификат JDK, установленный в папке <JAVA_HOME>/jre/lib/security/cacerts. 

6. Если вы изменили строку 6, чтобы использовать свой сертификат SSL, укажите пароль для этого сертификата в строке 7. 

7. Сохраните файл `config.properties`.

## <a name="run-the-java-app"></a>Запуск приложения Java

1. В окне терминала git с помощью команды `cd` перейдите к папке `azure-cosmosdb-cassandra-java-getting-started\java-examples`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started\java-examples"
    ```

2. В окне терминала Git используйте приведенную ниже команду, чтобы создать файл `cosmosdb-cassandra-examples.jar`.

    ```git
    mvn clean install
    ```

3. В окне терминала Git выполните следующие команды, чтобы запустить приложение Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Окно терминала отображает уведомления о создании пространства ключей и таблицы. Затем оно выбирает и отображает список всех пользователей в таблице, а затем выбирает строку по идентификатору и отображает значение.  

    Нажмите клавиши CTRL+C, чтобы остановить выполнение программы и закрыть окно консоли.

4. На портале Azure откройте **обозреватель данных**, чтобы запросить, изменить и обработать новые данные. 

    ![Просмотр данных в обозревателе данных](./media/create-cassandra-java/data-explorer.png)

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого краткого руководства вы узнали, как создать учетную запись Azure Cosmos DB, базу данных Cassandra и контейнер с помощью обозревателя данных, а также как запустить приложение, чтобы эти операции выполнялись программными средствами. Теперь вы можете импортировать дополнительные данные в контейнер Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных Cassandra в Azure Cosmos DB](cassandra-import-data.md)
