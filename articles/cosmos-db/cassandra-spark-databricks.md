---
title: Доступ к API Cassandra для Azure Cosmos DB из Azure Databricks
description: В этой статье описывается, как работать с API Cassandra для Azure Cosmos DB из Azure Databricks.
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: e1d8f41c55ffd453507804b005d10620665b512c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222040"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Доступ к данным API Cassandra для Azure Cosmos DB из Azure Databricks

В этой статье описывается, как работать с API Cassandra для Azure Cosmos DB из Spark в [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Предварительные требования

* [Предоставление учетной записи API Cassandra для Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account)

* [Просмотр основных сведений о подключении к API Cassandra для Azure Cosmos DB](cassandra-spark-generic.md)

* [Предоставление кластера Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Просмотр примеров кода для работы с API Cassandra](cassandra-spark-generic.md#next-steps)

* [Использование при необходимости cqlsh для проверки](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Конфигурация экземпляра API Cassandra для соединителя Cassandra:**

  Для инициализации соединителя для API Cassandra как части контекста Spark необходимы сведения о подключении соединителя Cassandra. При запуске записной книжки Databricks контекст Spark уже инициализирован, не рекомендуется останавливать и повторно инициализировать его. Одним из решений является добавление конфигурации экземпляра API Cassandra на уровне кластера в конфигурацию кластера Spark. Это разовое действие для каждого кластера. Добавьте в конфигурацию Spark следующий код как пару значений ключа, разделенных пробелами:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Добавление необходимых зависимостей

* **Соединитель Cassandra Spark:** с целью интеграции с API Cassandra для Azure Cosmos DB с помощью Spark соединитель Cassandra должен быть подключен к кластеру Azure Databricks. Чтобы подключить кластер:

  * Узнайте версию среды выполнения Databricks и версию Spark. Затем найдите [координаты Maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector), совместимые с соединителем Cassandra Spark, и подключите его к кластеру. См. статью ["Отправка пакета Maven или пакета Spark"](https://docs.databricks.com/user-guide/libraries.html), чтобы подключить библиотеку соединителя к кластеру. Например, координата Maven для объектов "Среда выполнения Azure Databricks Runtime версии 4.3", "Spark 2.3.1" и "Scala 2.11" равна `spark-cassandra-connector_2.11-2.3.1`

* **Библиотека API Cassandra для Azure Cosmos DB:** для настройки политики повтора из соединителя Cassandra Spark к API Cassandra для Azure Cosmos DB необходима фабрика настраиваемого подключения. Добавьте `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`[координаты Maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) для подключения библиотеки к кластеру.

## <a name="sample-notebooks"></a>Примеры записных книжек

Список [примеров записных книжек](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) Azure Databricks доступен для скачивания в репозитории Github. Среди них примеры подключения к API Cassandra для Azure Cosmos DB из Spark и выполнения с данными различных операций CRUD. Также можно [импортировать все записные книжки](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) в свою рабочую область кластера Databricks и запустить ее. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Доступ к API Cassandra для Azure Cosmos DB из программ Spark Scala

Программы Spark, выполняемые как автоматизированные процессы в Azure Databricks, передаются в кластер с помощью [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) и планируются для выполнения с помощью заданий Azure Databricks.

Следующие ссылки помогут приступить к созданию программы Spark Scala для взаимодействия с API Cassandra для Azure Cosmos DB.
* [Подключение к API Cassandra для Azure Cosmos DB из программ Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Запуск программы Spark Scala как автоматического задания для Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Полный список примеров кода для работы с API Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Дополнительная информация

Начните с [создания учетной записи API Cassandra, базы данных и таблицы](create-cassandra-api-account-java.md) с помощью приложения Java.
