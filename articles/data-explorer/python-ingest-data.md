---
title: Краткое руководство. Прием данных с помощью библиотеки Python в обозревателе данных Azure
description: В этом кратком руководстве описывается, как выполнять прием данных (загрузка) в обозреватель данных Azure с помощью Python.
services: data-explorer
author: mgblythe
ms.author: mblythe
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 09/24/2018
ms.openlocfilehash: b35cbb65c32336b0021a2004f4237a9784db8ea7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969093"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-python-library"></a>Краткое руководство. Прием данных с помощью библиотеки Python в обозревателе данных Azure

Обозреватель данных Azure — это быстрая и высокомасштабируемая служба для изучения данных журналов и телеметрии. Обозреватель данных Azure предоставляет две клиентские библиотеки для Python: [библиотеку приема](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest) и [библиотеку данных](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data). Они позволяют принимать (загружать) данные в кластер и запрашивать данные из кода. В этом кратком руководстве вы создадите таблицу и сопоставление данных в кластере тестирования. Затем вы поставите в очередь прием данных в кластер и проверите результаты.

Это краткое руководство также доступно в виде [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb).

Если у вас еще нет подписки Azure, создайте [бесплатную учетную запись](https://azure.microsoft.com/free/) Azure, прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Кроме подписки Azure, для прохождения этого краткого курса необходимо следующее:

* [Тестовый кластер и база данных](create-cluster-database-portal.md)

* [Python](https://www.python.org/downloads/) на компьютере, на котором ведется разработка

## <a name="install-the-data-and-ingest-libraries"></a>Установка библиотек данных и приема

Установите *azure-kusto-data* и *azure-kusto-ingest*.

```python
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>Добавление операторов и констант импорта

Импортируйте классы из библиотек, а также *datetime* и *pandas*, библиотеку анализа данных.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
import pandas as pd
import datetime
```

Чтобы проверить подлинность приложения, обозреватель данных Azure использует идентификатор клиента AAD. Чтобы найти идентификатор клиента, используйте следующий URL-адрес, заменив домен на *имя_вашего_домена*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Например, если ваш домен называется *contoso.com*, URL-адрес будет следующим: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Щелкните этот URL-адрес, чтобы просмотреть результаты. Первая строка выглядит следующим образом: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

В данном случае идентификатор клиента — `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. Прежде чем выполнять этот код, задайте значения для параметров AAD_TENANT_ID, KUSTO_URI, KUSTO_INGEST_URI и KUSTO_DATABASE.

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE  = "<DatabaseName>"
```

Создайте строку подключения. В этом примере используется проверка подлинности устройства для доступа к кластеру. Можно также использовать сертификат приложения AAD, ключ приложения AAD и пользователя и пароль AAD.

Целевую таблицу и сопоставление вы создадите позднее.

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>Определение данных исходного файла

Импортируйте дополнительные классы и задайте константы для исходного файла данных. В этом примере используется пример файла, размещенный в хранилище BLOB-объектов Azure. Набор демонстрационных данных **StormEvents** содержит данные о погоде из [Национальных центров Соединенных Штатов по экологической информации](https://www.ncdc.noaa.gov/stormevents/).

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-test-cluster"></a>Создание таблицы в тестовом кластере

Создайте таблицу, которая соответствует схеме данных в файле StormEvents.csv. При выполнении код возвращает примерно следующее сообщение: *Чтобы войти, откройте страницу https://microsoft.com/devicelogin с помощью веб-браузера и введите код F3W4VWZDM для проверки подлинности*. Следуйте инструкциям по входу, а затем выполните следующий блок кода. Для выполнения последующих блоков кода, устанавливающих соединение, необходимо повторно выполнить вход.

```python
KUSTO_CLIENT = KustoClient(KCSB_ENGINE)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

df_table_create_output = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND).primary_results[0].to_dataframe()

df_table_create_output
```

## <a name="define-ingestion-mapping"></a>Определение сопоставления приема

Сопоставьте входящие данные CSV с именами столбцов и типами данных, которые использовались при создании таблицы.

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

df_mapping_create_output = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND).primary_results[0].to_dataframe()

df_mapping_create_output
```

## <a name="queue-a-message-for-ingestion"></a>Отправка сообщения в очередь на прием

Поставьте в очередь сообщение, чтобы получить данные из хранилища BLOB-объектов и получить эти данные в обозреватель данных Azure.

```python
KUSTO_INGEST_CLIENT = KustoIngestClient(KCSB_INGEST)

INGESTION_PROPERTIES  = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv, mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
KUSTO_INGEST_CLIENT.ingest_from_multiple_blobs([BlobDescriptor(BLOB_PATH,FILE_SIZE)],delete_sources_on_success=False,ingestion_properties=INGESTION_PROPERTIES)

print('Done queueing up ingestion with Kusto')
```

## <a name="validate-that-data-was-ingested-into-the-table"></a>Проверка приема данных в таблицу

Подождите 5–10 минут, пока поставленные в очередь прием запланирует прием и загрузку данных в обозреватель данных Azure. Затем выполните следующий код, чтобы получить количество записей в таблице StormEvents.

```python
QUERY = "StormEvents | count"

df = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY).primary_results[0].to_dataframe()

df
```

## <a name="run-troubleshooting-queries"></a>Выполнение запросов по устранению неполадок

Войдите в [https://dataexplorer.azure.com](https://dataexplorer.azure.com) и подключитесь к кластеру. Выполните в своей базе данных следующую команду, чтобы проверить, не было ли в ней сбоев приема за последние четыре часа. Замените имя базы данных перед запуском.
    
```Kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Выполните следующую команду, чтобы узнать состояние всех операций приема за последние четыре часа. Замените имя базы данных перед запуском.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы планируете работать с другими нашими краткими и подробными руководствами, сохраните созданные ресурсы. В противном случае выполните в своей базе данных следующую команду, чтобы очистить таблицу StormEvents.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Написание запросов](write-queries.md)
