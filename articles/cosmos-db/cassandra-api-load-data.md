---
title: Загрузка примера данных в таблицу Azure Cosmos DB Cassandra API с помощью приложения Java | Документы Майкрософт
description: Данная статья описывает загрузку примера данных пользователя в таблицу в учетной записи Azure Cosmos DB Cassandra API с помощью приложения java.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d659004e94c51f173bc1e9ae42fd9c62ae45912f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972544"
---
# <a name="load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>Загрузка примера данных в таблицу Azure Cosmos DB Cassandra API

Данное руководство описывает, как загружать пример данных пользователя в таблицу в учетной записи Azure Cosmos DB Cassandra API с помощью приложения java. Приложение java использует [драйвер Datastax Java](https://github.com/datastax/java-driver) и загружает данные пользователя, например идентификатор пользователя, имя пользователя, город пользователя. 

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Загрузка данных в таблицу Cassandra
> * Запуск приложения

## <a name="prerequisites"></a>Предварительные требования

* Эта статья относится к руководству из нескольких частей. Перед началом чтения документа убедитесь, что [вы создали учетную запись Cassandra API, ключевое пространство и таблицу](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Загрузка данных в таблицу

Откройте файл "UserRepository.java" в папке "src\main\java\com\azure\cosmosdb\cassandra" и добавьте код для вставки полей user_id и user_name user_bcity в таблицу:

```java
/**
* Insert a row into user table
*
* @param id   user_id
* @param name user_name
* @param city user_bcity
*/
public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
}

/**
* Create a PrepareStatement to insert a row to user table
*
* @return PreparedStatement
*/
public PreparedStatement prepareInsertStatement() {
    final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
    return session.prepare(insertStatement);
}
```
 
Откройте файл "UserProfile.java" в папке "src\main\java\com\azure\cosmosdb\cassandra". Этот класс содержит основной метод, который вызывает методы createKeyspace и createTable, определенные вами ранее. Теперь добавьте следующий код, чтобы вставить демонстрационные данные в таблицу Cassandra API.

```java
//Insert rows into user table
PreparedStatement preparedStatement = repository.prepareInsertStatement();
    repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
    repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
    repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
    repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
    repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
```

## <a name="run-the-app"></a>Запуск приложения

Откройте командную строку или окно терминала и измените путь к папке, в которой вы создали проект. Выполните команду "mvn clean install", чтобы создать файл cosmosdb-cassandra-examples.jar в целевой папке и запустить приложение. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Теперь вы можете открыть обозреватель данных на портале Azure, чтобы убедиться, что сведения о пользователе добавлены в таблицу.
    
## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как загрузить демонстрационные данные в учетную запись Azure Cosmos DB Cassandra API. Теперь вы можете перейти к следующей статье:

> [!div class="nextstepaction"]
> [Запрос данных из учетной записи Cassandra API](cassandra-api-query-data.md)
