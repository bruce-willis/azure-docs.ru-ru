---
title: Копирование данных из Netezza с помощью Фабрики данных Azure | Документация Майкрософт
description: Узнайте, как копировать данные из Netezza в поддерживаемые хранилища данных-приемники с помощью действия копирования в конвейере фабрики данных Azure.
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
ms.date: 06/15/2018
ms.author: jingwang
ms.openlocfilehash: f8c10e2200f830ea6e568e7b3fba1f0a6085cef2
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055660"
---
# <a name="copy-data-from-netezza-using-azure-data-factory"></a>Копирование данных из Netezza с помощью Фабрики данных Azure 

Из этой статьи вы узнаете, как с помощью действия копирования в фабрике данных Azure копировать данные из Netezza. Это продолжение [статьи об обзоре действия копирования](copy-activity-overview.md), в которой представлены общие сведения о действии копирования.

## <a name="supported-capabilities"></a>Поддерживаемые возможности

Данные из Netezza можно скопировать в любое поддерживаемое хранилище данных, используемое в качестве приемника. Список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования, приведен в таблице [Поддерживаемые хранилища данных и форматы](copy-activity-overview.md#supported-data-stores-and-formats).

Фабрика данных Azure имеет встроенный драйвер для настройки подключения. Поэтому с использованием этого соединителя вам не нужно устанавливать драйверы вручную.

## <a name="getting-started"></a>Приступая к работе

Вы можете создать конвейер с помощью операции копирования, используя пакет SDK для .NET, пакет SDK для Python, Azure PowerShell, API REST или шаблон Azure Resource Manager. Пошаговые инструкции по созданию конвейера с действием копирования см. в [руководстве по действию копирования](quickstart-create-data-factory-dot-net.md).

В разделах ниже приведены сведения о свойствах, которые используются для определения сущностей фабрики данных, относящихся к соединителю Netezza.

## <a name="linked-service-properties"></a>Свойства связанной службы

Для связанной службы Netezza поддерживаются следующие свойства.

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Для свойства type необходимо задать значение **Netezza**. | Yes |
| connectionString | Строка для подключения к Netezza через интерфейс ODBC. Пометьте это поле как SecureString, чтобы безопасно хранить его в фабрике данных, или [добавьте ссылку на секрет, хранящийся в Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Среда выполнения интеграции](concepts-integration-runtime.md), используемая для подключения к хранилищу данных. Вы можете использовать локальную среду выполнения интеграции или среду выполнения интеграции Azure (если хранилище данных является общедоступным). Если не указано другое, по умолчанию используется интегрированная среда выполнения Azure. |Нет  |

Типичная строка подключения — `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. Дополнительные свойства, которые вы можете установить в вашем случае:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |:--- |
| SecurityLevel | Уровень безопасности (SSL/TLS), который драйвер использует для подключения к хранилищу данных. (например, `SecurityLevel=preferredSecured`. Поддерживаемые значения:<br/>— Только незащищенный (**onlyUnSecured**): драйвер не использует SSL.<br/>- **Предпочтительно незащищенный (preferredUnSecured) (по умолчанию)**: если сервер предоставляет возможность выбора, драйвер не использует SSL. <br/>- **Предпочтительно защищенный (preferredSecured)**: если сервер предоставляет возможность выбора, драйвер использует SSL. <br/>- **Только защищенный (onlySecured)**: драйвер не подключается, если SSL-подключение недоступно. | Нет  |
| CaCertFile | Полный путь к SSL-сертификату, используемому сервером. (например, `CaCertFile=<cert path>;`| Да, если протокол SSL включен |

**Пример.**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
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

Полный список разделов и свойств, доступных для определения наборов данных, см. в статье о [наборах данных](concepts-datasets-linked-services.md). В этом разделе содержится список свойств, поддерживаемых набором данных Netezza.

Чтобы скопировать данные из Netezza, установите свойство type набора данных **NetezzaTable**. В этом типе набора данных нет дополнительных свойств для определенного типа.

**Пример**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Свойства действия копирования

Полный список разделов и свойств, используемых для определения действий, см. в статье [Конвейеры и действия в фабрике данных Azure](concepts-pipelines-activities.md). В этом разделе содержится список свойств, поддерживаемых Netezza как источником.

### <a name="netezza-as-source"></a>Netezza в качестве источника

Чтобы копировать данные из Netezza, установите тип источника **NetezzaSource** в действии копирования. В разделе **source** действия копирования поддерживаются следующие свойства:

| Свойство | ОПИСАНИЕ | Обязательно |
|:--- |:--- |:--- |
| Тип | Свойство type источника действия копирования должно иметь значение **NetezzaSource**. | Yes |
| query | Используйте пользовательский SQL-запрос для чтения данных. Например, `"SELECT * FROM MyTable"`. | Yes |

**Пример.**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Дополнительная информация
В таблице [Поддерживаемые хранилища данных](copy-activity-overview.md#supported-data-stores-and-formats) приведен список хранилищ данных, которые поддерживаются в качестве источников и приемников для действия копирования в фабрике данных Azure.
