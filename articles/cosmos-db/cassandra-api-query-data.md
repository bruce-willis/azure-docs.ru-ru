---
title: Запрос данных из учетной записи API Cassandra для Azure Cosmos DB
description: В этой статье описывается запрос данных пользователя из учетной записи API Cassandra для Azure Cosmos DB с помощью приложения Java.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: c1fb4c27f897e3c0952ed6419e167613ac8204f7
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223497"
---
# <a name="query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Запрос данных из учетной записи API Cassandra для Azure Cosmos DB

В этом руководстве описывается запрос данных пользователя из учетной записи API Cassandra для Azure Cosmos DB с помощью приложения Java. Приложение Java использует [драйвер Java](https://github.com/datastax/java-driver) и запрашивает данные пользователя, например идентификатор пользователя, имя пользователя, город пользователя. 

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Запрос данных из таблицы Cassandra
> * Запуск приложения

## <a name="prerequisites"></a>Предварительные требования

* Эта статья относится к руководству из нескольких частей. Прежде чем начать, обязательно выполните предшествующие шаги, чтобы [создать учетную запись API Cassandra, пространство ключей и таблицу](create-cassandra-api-account-java.md), а также [загрузить пример данных в эту таблицу](cassandra-api-load-data.md). 

## <a name="query-data"></a>Запрос данных

Откройте файл `UserRepository.java`, расположенный в папке `src\main\java\com\azure\cosmosdb\cassandra`. Добавьте в него приведенный ниже блок кода. Этот код обеспечивает три функции: для запроса всех пользователей в базе данных, для запроса конкретного пользователя по идентификатору пользователя и для удаления таблицы. 

```java
/**
* Select all rows from user table
*/
public void selectAllUsers() {

    final String query = "SELECT * FROM uprofile.user";
    List<Row> rows = session.execute(query).all();

    for (Row row : rows) {
       LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
}

/**
* Select a row from user table
*
* @param id user_id
*/
public void selectUser(int id) {
    final String query = "SELECT * FROM uprofile.user where user_id = 3";
    Row row = session.execute(query).one();

    LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
}

/**
* Delete user table.
*/
public void deleteTable() {
   final String query = "DROP TABLE IF EXISTS uprofile.user";
   session.execute(query);
}
```

Откройте файл `UserProfile.java`, расположенный в папке `src\main\java\com\azure\cosmosdb\cassandra`. Этот класс содержит основной метод, который вызывает методы createKeyspace и createTable, а также метод вставки данных, определенные вами ранее. Теперь добавьте приведенный ниже код, который запрашивает всех пользователей или конкретного пользователя.

```java
LOGGER.info("Select all users");
repository.selectAllUsers();

LOGGER.info("Select a user by id (3)");
repository.selectUser(3);

LOGGER.info("Delete the users profile table");
repository.deleteTable();
```

## <a name="run-the-java-app"></a>Запуск приложения Java
1. Откройте командную строку или окно терминала. Вставьте следующий блок кода. 

   Этот код изменяет каталог (cd) для пути к папке, где создан проект. Затем он выполняет команду `mvn clean install`, чтобы создать файл `cosmosdb-cassandra-examples.jar` в целевой папке. Наконец, он запускает приложение Java.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Теперь на портале Azure откройте **обозреватель данных** и убедитесь, что таблица пользователя удалена.

## <a name="next-steps"></a>Дополнительная информация

* В этом руководстве вы узнали, как запросить данные из учетной записи API Cassandra для Azure Cosmos DB. Теперь вы можете перейти к следующей статье:

> [!div class="nextstepaction"]
> [Перенос данных в учетную запись API Cassandra](cassandra-import-data.md)


