---
title: Действие поиска в фабрике данных Azure | Документация Майкрософт
description: Сведения об использовании действия поиска для поиска значений во внешнем источнике. На эти выходные данные можно затем ссылаться в последующих действиях.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: e437e7b7d5298af325ae2a5e2ba689b417bad022
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39002926"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Действие поиска в фабрике данных Azure

Действие поиска можно использовать для получения набора данных из любых источников данных, поддерживаемых Фабрикой данных Azure. Используйте его в следующих сценариях:
- Динамическое определение объектов для обработки в последующем действии вместо жесткого программирования имени объекта. Примерами объектов могут быть файлы и таблицы.

Действие поиска считывает и возвращает содержимое файла конфигурации или таблицы. Оно также возвращает результат выполнения запроса или хранимой процедуры. Выходные данные действия поиска можно использовать в последующем действии копирования или преобразования, если это отдельное значение. Кроме того, их можно использовать в действии ForEach, если это массив атрибутов.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Для действия поиска поддерживаются следующие источники данных. Максимальное число записей, которые могут быть возвращены действием поиска, — 5000 (до 2 МБ). Сейчас максимальная продолжительность действия поиска до истечения времени составляет один час.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Синтаксис

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Свойства типа
ИМЯ | ОПИСАНИЕ | type | Обязательный?
---- | ----------- | ---- | --------
dataset | Предоставляет ссылку на набор данных для поиска. Дополнительные сведения можно найти в разделе **Свойства набора данных** в каждой соответствующей статье о соединителе. | Пара "ключ — значение" | Yes
источник | Содержит свойства источника конкретного набора данных, аналогичные источнику действия копирования. Дополнительные сведения можно найти в разделе **Свойства действия копирования** в каждой соответствующей статье о соединителе. | Пара "ключ — значение" | Yes
firstRowOnly | Указывает, следует ли возвращать только первую или все строки. | Логическое | Нет. Значение по умолчанию — `true`.

> [!NOTE]

> * Исходные столбцы с типом **ByteArray** не поддерживаются.
> * **Структура** не поддерживается в определениях набора данных. Для текстовых файлов форматирования можно использовать строку заголовка, чтобы указать имя столбца.
> * Если ваш источник поиска — файл JSON, параметр `jsonPathDefinition` для изменения объекта JSON не поддерживается. Будут извлечены целые объекты.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Использование результата действия поиска в следующем действии

Результат поиска возвращается в раздел `output` результатов выполнения действия.

* **Когда для `firstRowOnly` задано значение `true` (по умолчанию)**, формат выходного значения выглядит, как показано в следующем коде. Результат поиска находится в предопределенном ключе `firstRow`. Чтобы использовать этот результат в последующем действии, примените шаблон `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Когда для `firstRowOnly` задано значение `false`**, формат выходного значения выглядит, как показано в следующем коде. В поле `count` указано количество возвращенных записей. Подробные значения отображаются в списке предопределенного массива `value`. В этом случае за действием поиска следует [действие Foreach](control-flow-for-each-activity.md). Укажите массив `value` в поле действия ForEach `items` с помощью шаблона `@activity('MyLookupActivity').output.value`. Для получения доступа к элементам массива `value` используйте следующий синтаксис: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Например, `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Пример действия копирования
В этом примере действие копирования копирует данные из таблицы SQL в экземпляре Базы данных SQL Azure в хранилище BLOB-объектов Azure. Имя таблицы SQL хранится в файле JSON в хранилище BLOB-объектов. Действие поиска ищет имя таблицы во время выполнения. Такой подход позволяет динамически изменять файл JSON. При этом нет необходимости в повторном развертывании конвейеров или наборов данных. 

В этом примере демонстрируется поиск только для первой строки. Примеры выполнения поиска всех строк и связывания результатов с действием ForEach см. в статье [Копирование нескольких таблиц в пакетном режиме с помощью фабрики данных Azure](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Конвейер
Этот конвейер содержит два действия: поиск и копирование. 

- Действие поиска настроено для использования **LookupDataset**, который ссылается на расположение в хранилище BLOB-объектов Azure. Действие поиска считывает имя таблицы SQL из файла JSON в этом расположении. 
- Действие копирования использует выходные данные действия поиска — имя таблицы SQL. Свойство **tableName** в наборе данных **SourceDataset** настроено для использования выходных данных действия поиска. Действие копирования копирует данные из таблицы SQL в расположение в хранилище BLOB-объектов Azure. Расположение задается свойством **SinkDataset**. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Набор данных поиска
Набор данных **поиска** — это файл **sourcetable.json** в папке поиска службы хранилища Azure, указанный типом **AzureStorageLinkedService**. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Исходный** набор данных для действия копирования
**Исходный** набор данных использует выходные данные действия поиска — имя таблицы SQL. Действие копирования копирует данные из этой таблицы SQL в расположение в хранилище BLOB-объектов Azure. Расположение задается набором данных **приемника**. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Набор данных **приемника** для действия копирования
Действие копирования копирует данные из таблицы SQL в файл **filebylookup.csv** в папке **csv** в хранилище Azure. Файл указан с помощью свойства **AzureStorageLinkedService**. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Связанная служба хранения Azure
Эта учетная запись хранения содержит файл JSON с именами таблиц SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Связанная служба базы данных SQL Azure
Этот экземпляр базы данных SQL Azure содержит данные, копируемые в хранилище BLOB-объектов. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.JSON

#### <a name="set-of-objects"></a>Набор объектов

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Массив объектов

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с другими действиями потока управления, которые поддерживаются фабрикой данных: 

- [Действие выполнения конвейера в фабрике данных Azure](control-flow-execute-pipeline-activity.md)
- [Действие ForEach](control-flow-for-each-activity.md)
- [Действие получения метаданных в Фабрике данных Azure](control-flow-get-metadata-activity.md)
- [Веб-действие](control-flow-web-activity.md)
