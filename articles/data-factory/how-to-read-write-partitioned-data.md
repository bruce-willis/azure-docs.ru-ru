---
title: Чтение или запись секционированных данных в фабрике данных Azure | Документация Майкрософт
description: Узнайте больше о том, как выполнять чтение и запись секционированных данных в фабрике данных Azure версии 2.
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
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: cdf305e3607d7483186185a014883cff5458b89f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619088"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory-version-2"></a>Чтение или запись секционированных данных в фабрике данных Azure версии 2
В версии 1 фабрика данных Azure поддерживала чтение или запись секционированных данных с использованием системных переменных SliceStart/SliceEnd/WindowStart/WindowEnd. В версии 2 это достигается с помощью параметра конвейера и времени начала или запланированного времени запуска триггера в качестве значения параметра. 

## <a name="use-a-pipeline-parameter"></a>Использование параметра конвейера 
В версии 1 можно было использовать свойство partitionedBy и системную переменную SliceStart, как показано в следующем примере: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Дополнительные сведения о свойстве partitonedBy см. в статье о [соединителе больших двоичных объектов Azure версии 1](v1/data-factory-azure-blob-connector.md#dataset-properties). 

В версии 2 это можно достичь, выполнив следующие действия: 

1. Определите **параметр конвейера** строкового типа. В следующем примере параметр конвейера имеет имя **windowStartTime**. 
2. Задайте для параметра **folderPath** в определении набора данных значение параметра конвейера. 
3. Передайте фактическое значение параметра при вызове конвейера по запросу. Кроме того, вы можете передавать данные времени начала или запланированного времени запуска триггера динамически во время выполнения. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-value-from-a-trigger"></a>Передача значения из триггера
В следующем определении триггера "переворачивающегося" окна время запуска передается в виде значения параметра конвейера **windowStartTime**: 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>Пример

Ниже приведен пример определения набора данных.

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Определение конвейера: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация
См. пошаговое руководство по [созданию фабрики данных с конвейером](quickstart-create-data-factory-powershell.md). 
