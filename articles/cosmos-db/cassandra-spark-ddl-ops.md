---
title: Операции DDL в API Cassandra для Azure Cosmos DB из Spark
description: В этой статье описывается пространство ключей и операции DDL с таблицей в API Cassandra для Azure Cosmos DB из Spark.
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: a799d85cc27575badda6892ba7baf68ca1eb1dfb
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226081"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Операции DDL в API Cassandra для Azure Cosmos DB из Spark

В этой статье описывается пространство ключей и операции DDL с таблицей в API Cassandra для Azure Cosmos DB из Spark.

## <a name="cassandra-api-related-configuration"></a>Конфигурация API Cassandra 

```scala
import org.apache.spark.sql.cassandra._

//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="keyspace-ddl-operations"></a>Операции DDL для пространства ключей

### <a name="create-a-keyspace"></a>Создание пространства ключей

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Проверка в cqlsh

Выполните следующую команду в cqlsh, и вы увидите созданное ранее пространство ключей.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Удаление пространства ключей

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Проверка в cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Операции DDL для таблиц

**Рекомендации**  

- Пропускную способность можно назначить на уровне таблицы с помощью инструкции create table.  
- Один ключ секции может хранить 10 ГБ данных.  
- Одна запись может хранить до 2 МБ данных.  
- Один диапазон ключей секций может хранить несколько ключей секции.

### <a name="create-a-table"></a>Создание таблицы

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Проверка в cqlsh

Выполните следующую команду в cqlsh, и увидите таблицу с именем "books": 

```bash
USE books_ks;
DESCRIBE books;
```

Подготовленные значения пропускной способности и срока жизни по умолчанию не отображаются в выходных данных предыдущей команды. Эти значения можно получить на портале.

### <a name="alter-table"></a>Изменение таблицы

Следующие значения можно изменить с помощью команды alter table:

* Подготовленная пропускная способность 
* Срок жизни
<br>Изменения столбцов в настоящее время не поддерживаются.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>инструкция удаления таблицы;

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Проверка в cqlsh

Выполните следующую команду в cqlsh, и вы увидите, что таблица "books" больше не доступна:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>Дополнительная информация

После создания пространства ключей и таблицы перейдите к следующим статьям об операциях CRUD и других аспектах:
 
* [Операции создания и вставки](cassandra-spark-create-ops.md)  
* [Операции чтения](cassandra-spark-read-ops.md)  
* [Операции upsert](cassandra-spark-upsert-ops.md)  
* [Операции удаления](cassandra-spark-delete-ops.md)  
* [Операции агрегирования](cassandra-spark-aggregation-ops.md)  
* [Операции копирования таблиц](cassandra-spark-table-copy-ops.md)  
