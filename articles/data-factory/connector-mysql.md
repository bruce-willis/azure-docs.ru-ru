---
title: Копирование данных из MySQL с помощью фабрики данных Azure | Документация Майкрософт
description: Дополнительные сведения о соединителе MySQL в фабрике данных Azure, который позволяет скопировать данные из базы данных MySQL в хранилище данных, поддерживаемое в качестве приемника.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/23/2018
ms.author: jingwang
ms.openlocfilehash: bb3179f1db077aacc7e36acf16486ee77a7f36e7
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051269"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Копирование данных из MySQL с помощью фабрики данных Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Версия 1](v1/data-factory-onprem-mysql-connector.md)
> * [Текущая версия](connector-mysql.md)

В этой статье описывается, как с помощью действия копирования в фабрике данных Azure копировать данные из базы данных MySQL. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из базы данных MySQL можно скопировать в любое хранилище данных, поддерживаемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

В частности, этот соединитель MySQL поддерживает MySQL **версии 5.1 и более поздних версий**.

## <a name="prerequisites"></a>предварительным требованиям

Если база данных MySQL не является общедоступной, необходимо настроить локальную среду выполнения интеграции. Дополнительные сведения о локальной среде IR см. в статье [How to create and configure Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) (Создание и настройка локальной среды IR). Начиная с версии 3.7 служба Integration Runtime предоставляет встроенный драйвер MySQL, поэтому вам не потребуется устанавливать его вручную.

Для Integration Runtime (Self-hosted) версии ниже, чем 3.7, установите [соединитель MySQL Connector/Net для Microsoft Windows](https://dev.mysql.com/downloads/connector/net/) версии 6.6.5–6.10.7 на компьютер со средой выполнения интеграции. Это 32-разрядная версия драйвера совместима с 64-разрядной версией среды IR.

## <a name="getting-started"></a>Приступая к работе

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Следующие разделы содержат сведения о свойствах JSON, которые используются для определения сущностей фабрики данных, относящихся к соединителю MySQL.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы MySQL поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **MySql** | Yes |
| connectionString | Укажите сведения, необходимые для подключения к экземпляру базы данных Azure для MySQL. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

Типичная строка подключения — `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`. Дополнительные свойства, которые вы можете установить в вашем случае:

| Свойство | ОПИСАНИЕ | Параметры | Обязательно |
|:--- |:--- |:--- |:--- |:--- |
| SSLMode | Этот параметр указывает, использует ли драйвер SSL-шифрование и проверку при подключении к MySQL. (например, `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(по умолчанию)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | Нет  |
| useSystemTrustStore | Этот параметр указывает, следует ли использовать сертификат ЦС из доверенного системного хранилища или из указанного PEM-файла. (например, `UseSystemTrustStore=<0/1>;`| Enabled (1) / Disabled (0) **(по умолчанию)** | Нет  |

**Пример.**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Ранее используемая связанная служба MySQL со приведенными ниже полезными данными по-прежнему поддерживается. Но мы рекомендуем в дальнейшем использовать более новую версию.

**Предыдущие полезные данные:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Свойства набора данных

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о наборах данных. В этом разделе содержится список свойств, поддерживаемых набором данных MySQL.

Чтобы скопировать данные из MySQL, установите свойство типа набора данных **RelationalTable**. Поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type для набора данных должно иметь значение **RelationalTable**. | Yes |
| tableName | Имя таблицы в базе данных MySQL. | Нет (если свойство query указано в источнике действия) |

**Пример**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых источником MySQL.

### <a name="mysql-as-source"></a>MySQL в качестве источника

Чтобы копировать данные из MySQL, установите тип источника в действии копирования **RelationalSource**. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **RelationalSource**. | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Нет (если для набора данных задано свойство tableName) |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-mysql"></a>Сопоставление типов данных для MySQL

При копировании данных из MySQL для промежуточных типов данных фабрики данных Azure используются следующие сопоставления из типов данных MySQL. Дополнительные сведения о том, как действие копирования сопоставляет исходную схему и типы данных для приемника, см. в статье [Сопоставление схем в действии копирования](copy-activity-schema-and-type-mapping.md).

| Тип данных MySQL | Тип промежуточных данных фабрики данных |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
