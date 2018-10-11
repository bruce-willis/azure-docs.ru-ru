---
title: Работа с API Cassandra для Azure Cosmos DB из оболочки Spark
description: Эта статья — главная страница для интеграции API Cassandra для Cosmos DB из оболочки Spark.
services: cosmos-db
author: anagha-microsoft
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: 38a972d39b845dca39bcc4dcf921c603301af582
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869658"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Подключитесь к API Cassandra для Azure Cosmos DB из оболочки Spark

Эта статья — одна из серии об интеграции API Cassandra для Azure Cosmos DB из оболочки Spark. В статье речь идет о подключении, операциях языка описания данных, основных операциях языка обработки данных DML и расширенной интеграции API Cassandra для Azure Cosmos DB из оболочки Spark. 

## <a name="prerequisites"></a>Предварительные требования
* [Создание учетной записи API Cassandra для Azure Cosmos DB.](create-cassandra-dotnet.md#create-a-database-account)

* Предоставьте свой выбор среды Spark [[Краткое руководство. Запуск задания Spark в Azure Databricks с помощью портала Azure](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Краткое руководство по созданию кластера Spark в HDInsight с помощью шаблона](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Другое].

## <a name="dependencies-for-connectivity"></a>Зависимости для подключения
* **Соединитель Spark для Cassandra.** Соединитель Spark используется для подключения к API Cassandra для Azure Cosmos DB.  Определите и используйте версию соединителя, расположенного в [Maven central]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector), которая совместима с версиями Spark и Scala вашей среды Spark.

* **Воспомогательная библиотека Azure Cosmos DB для API Cassandra.** В придачу к соединителю Spark вам потребуется другая библиотека Azure Cosmos DB, которая называется [azure-cosmos-cassandra-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar). Эта библиотека содержит производство подключений и классы пользовательской политики повтора.

  Политика повтора в Azure Cosmos DB настроена для обработки исключений кода состояния HTTP 429 ("Высокая частота запросов"). API Cassandra Azure Cosmos DB преобразует эти исключения в перегруженные ошибки в собственном протоколе Cassandra, и вы можете повторить попытку в пассивном режиме. Так как Azure Cosmos DB использует модель подготовленной пропускной способности, то исключения ограничения частоты запроса возникают тогда, когда увеличивается скорость входа/выхода. Политика повтора обеспечивает защиту заданий Spark от скачка данных, который кратковременно превышает пропускную способность, выделенную для вашей коллекции.

  > [!NOTE] 
  > Политика повтора может защитить задания Spark только от кратковременных скачков. Если вы не настроили достаточное количество ЕЗ, необходимых для выполнения вашей рабочей нагрузки, тогда политика повтора не применима, а класс политики повтора еще раз генерирует исключение.

* **Сведения о подключении учетной записи Azure Cosmos DB.** API Cassandra имени вашей учетной записи Azure, конечная точка учетной записи и ключ.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Параметры конфигурации пропускной способности соединителя Spark

Ниже перечислены параметры конфигурации пропускной способности API Cassandra Azure Cosmos DB, предоставленные соединителем. Подробный список всех параметров конфигурации см. на странице [Справочник по конфигурации](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) соединителя Cassandra Spark в репозитории GitHub.

| **Имя свойства** | **Значение по умолчанию** | **Описание** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Количество строк в одном пакете. Задайте для параметра значение 1. Этот параметр используется для улучшения пропускной способности для больших рабочих нагрузок. |
| spark.cassandra.connection.connections_per_executor_max  | None | Максимальное число подключений на узел для каждого исполнителя. 10*n равно 10 подключениям на узел для кластера Cassandra с n узлов. Итак, если вам требуется 5 подключений на узел для каждого исполнителя для кластера Cassandra с 5 узлами, то вы должны настроить эту конфигурацию на 25. Меняйте это значение в зависимости от степени параллелизма или количества исполнителей, для которых настроены ваши задания Spark.   |
| spark.cassandra.output.concurrent.writes  |  100 | Определяет количество параллельных записей, которые могут происходить на каждом исполнителе. Поскольку "batch.size.rows" равно 1, убедитесь, что вы увеличили масштаб этого значения соответствующим образом. Меняйте это значение в зависимости от степени параллелизма или пропускной способности, которую вы хотите получить для рабочей нагрузки. |
| spark.cassandra.concurrent.reads |  512 | Определяет количество параллельных процессов операций чтения, которые могут происходить на каждом исполнителе. Меняйте это значение в зависимости от степени параллелизма или пропускной способности, которую вы хотите получить для рабочей нагрузки  |
| spark.cassandra.output.throughput_mb_per_sec  | None | Определяет общую пропускную способность записи для каждого исполнителя. Этот параметр может использоваться как верхнее ограничение пропускной способности задания Spark, так и как основа подготовленной пропускной способности коллекции Cosmos DB.   |
| spark.cassandra.input.reads_per_sec| None   | Определяет общую пропускную способность чтения для каждого исполнителя. Этот параметр может использоваться как верхнее ограничение пропускной способности задания Spark, так и как основа подготовленной пропускной способности коллекции Cosmos DB.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | Определяет количество пакетов для одной задачи Spark, которая может сохраниться в памяти перед отправкой в API Cassandra |
| spark.cassandra.connection.keep_alive_ms | 60 000 | Определяет период времени, до которого доступны соединения, которые не используются. | 

Настройте пропускную способность и степень параллелизма этих параметров, учитывая ожидаемую рабочую нагрузку ваших заданий Spark и пропускную способность, которую вы предоставили для своей учетной записи Cosmos DB.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Подключение к API Cassandra для Azure Cosmos DB из оболочки Spark

### <a name="cqlsh"></a>cqlsh
Следующие команды подробно описывают, как подключиться к API Cassandra Azure CosmosDB из cqlsh.  Это можно использовать для проверки во время запуска образцов в оболочке Spark.<br>
**Из Linux, Unix и Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
В этой статье описывается создание кластеров Azure Databricks, конфигурация кластера для подключения к API Cassandra Azure Cosmos DB и несколько примеров записных книжек, которые охватывают операции DDL, DML и другое.<BR>
[Работа с API Cassandra для Azure Cosmos DB из Azure Databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Azure HDInsight — оболочка Spark
В этой статье описывается служба HDinsight-Spark, создание, конфигурация кластера для подключения к API Cassandra Azure Cosmos DB и несколько примеров записных книжек, которые охватывают операции DDL, DML и другое.<BR>
[Работа с API Cassandra для Azure Cosmos DB из Azure HDInsight-Spark](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Общие сведения о среде Spark
Хотя приведенные выше разделы концентрировались на службах PaaS, основанных на Azure Spark, этот раздел охватывает общие сведения о среде Spark.  Зависимости соединителя, импорт и конфигурация сеанса Spark приведены ниже. В разделе Next steps (Дальнейшие действия) рассматриваются примеры кода для операций DDL, DML и других.  

#### <a name="connector-dependencies"></a>Зависимости соединителя:

1. Добавить координаты Maven, чтобы получить [соединитель Cassandra для Spark](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Добавить координаты Maven, чтобы получить [вспомогательную библиотеку Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) для API Cassandra

#### <a name="imports"></a>Импорт:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Конфигурация сеанса Spark:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>Дополнительная информация

В следующих статьях показано интеграцию Spark с помощью API Cassandra Azure Cosmos DB. 
 
* [Операции DDL](cassandra-spark-ddl-ops.md)
* [Операции создания и вставки](cassandra-spark-create-ops.md)
* [Операции чтения](cassandra-spark-read-ops.md)
* [Операции upsert](cassandra-spark-upsert-ops.md)
* [Операции удаления](cassandra-spark-delete-ops.md)
* [Операции агрегирования](cassandra-spark-aggregation-ops.md)
* [Операции копирования таблиц](cassandra-spark-table-copy-ops.md)
