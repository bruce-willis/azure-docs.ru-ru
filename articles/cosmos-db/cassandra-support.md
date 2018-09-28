---
title: Функции и команды Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB
description: Сведения о поддержке функций Apache Cassandra в API Cassandra для Azure Cosmos DB
services: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 70b2c3252380440a491e6d21a8b8a96a2390525b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986276"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Функции Apache Cassandra, поддерживаемые API Cassandra для Azure Cosmos DB 

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Взаимодействие с API Cassandra для Azure Cosmos DB осуществляется посредством клиентских [драйверов](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) Cassandra с открытым исходным кодом, совместимым с [сетевым протоколом](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec) Cassandra Query Language (CQL) v4. 

С помощью API Cassandra для Azure Cosmos DB вы можете воспользоваться преимуществами API-интерфейсов Apache Cassandra, а также корпоративными возможностями, доступными в Azure Cosmos DB. В их число входят [глобальное распределение](distribute-data-globally.md), [секционирование автоматического масштабирования](partition-data.md), гарантии доступности и задержки, шифрование при хранении, создание резервных копий и многое другое.

## <a name="cassandra-protocol"></a>Протокол Cassandra 

API Cassandra для Azure Cosmos DB совместим с CQL **v4**. Ниже перечислены поддерживаемые команды CQL, средства, ограничения и исключения. Любой драйвер клиента, который распознает эти протоколы, должен иметь возможность подключения к API Cassandra для Azure Cosmos DB.

## <a name="cassandra-driver"></a>Драйвер Cassandra

API Cassandra для Azure Cosmos DB поддерживает следующие версии драйверов DataStax Cassandra:

* [Java 3.5 и выше](https://github.com/datastax/java-driver)  
* [C# 3.5 и выше](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5 и выше](https://github.com/datastax/nodejs-driver)  
* [Python 3.15 и выше](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>Типы данных CQL 

API Cassandra для Azure Cosmos DB поддерживает следующие типы данных CQL:

* ascii  
* bigint  
* blob-объект  
* Логическое  
* Счетчик  
* дата  
* decimal  
* Double  
* float;  
* frozen  
* inet  
* int  
* list  
* set  
* smallint;  
* текст  
* Twitter в режиме реального  
* timestamp  
* timeuuid  
* tinyint;  
* tuple  
* uuid  
* varchar.  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>Функции CQL

API Cassandra для Azure Cosmos DB поддерживает следующие функции CQL:

* по маркеру  
* Функции преобразования BLOB-объектов 
  * typeAsBlob(value)  
  * blobAsType(value)
* Функции UUID и timeuuid 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date)  


## <a name="cassandra-query-language-limits"></a>Ограничения CQL

API Cassandra для Azure Cosmos DB не имеет ограничений на размер данных, хранящихся в таблице. В таблицах могут храниться сотни терабайт или петабайт данных с соблюдением ограничений для ключей секций. Каждый эквивалент сущности или строки не имеет никаких ограничений на число столбцов, однако общий размер сущности не должен превышать 2 МБ.

## <a name="tools"></a>Средства 

API Cassandra для Azure Cosmos DB — это платформа управляемой службы. Ей не требуются расходы на управление или такие служебные программы, как сборщик мусора, виртуальная машина Java (JVM) и Nodetool для управления кластером. Она поддерживает средства, такие как cqlsh, использующее совместимость с двоичным CQLv4. 

* Кроме того, для управления учетной записью применяются обозреватель данных на портале Azure, метрики, журналы диагностики, PowerShell и CLI.

## <a name="cql-shell"></a>Оболочка CQL  

Программа командной строки CQLSH входит в состав Apache Cassandra 3.1.1 и работает без дополнительной настройки со следующими включенными переменными среды.

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to balitmore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```
**UNIX/Linux и Mac:**

```bash
export SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to balitmore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```

## <a name="cql-commands"></a>Команды CQL

Azure Cosmos DB поддерживает следующие команды базы данных для учетных записей API Cassandra.

* CREATE KEYSPACE 

* CREATE TABLE 

* ALTER TABLE 

* USE 

* INSERT 

* SELECT 

* UPDATE 

* BATCH — поддерживаются только нерегистрируемые команды 

* УДАЛИТЬ

Все операции CRUD, выполняемые с помощью пакета SDK, совместимого с CQLV4, возвращают дополнительные сведения об ошибке, использованных единицах запроса и идентификаторе действия. Команды удаления и обновления необходимо выполнять с учетом принципов управления ресурсов, что позволит избежать чрезмерного использования подготовленных ресурсов. 

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>Сопоставление согласованности 

API Cassandra для Azure Cosmos DB предоставляет согласованные операции чтения. Все операции записи независимо от согласованности учетной записи всегда записываются в соответствии с соглашениями SLA о производительности записи.

## <a name="permission-and-role-management"></a>Управление разрешениями и ролями

Azure Cosmos DB поддерживает управление доступом на основе ролей (RBAC), а также пароли (ключи) для чтения и записи или только для чтения, которые можно получить с помощью [портала Azure](https://portal.azure.com). Azure Cosmos DB пока не поддерживает концепцию пользователей и ролей для действий на плоскости данных. 

## <a name="next-steps"></a>Дополнительная информация

- Начните с [создания учетной записи API Cassandra, базы данных и таблицы](create-cassandra-api-account-java.md) с помощью приложения Java.

