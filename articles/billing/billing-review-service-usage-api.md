---
title: Просмотр использования ресурса службы Azure с помощью REST API | Документация Майкрософт
description: Узнайте, как использовать интерфейсы REST API для просмотра использования ресурса службы Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alleonar
ms.openlocfilehash: 2af87c87916dd272026a3bd7e1438507c655053b
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42616999"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Просмотр использования ресурса службы Azure с помощью REST API

API-интерфейсы управления затратами Azure помогают анализировать потребление ресурсов в Azure и управлять им.

Из этой статьи можно узнать, как создавать ежедневный отчет, который будет генерировать разделенный запятыми документ с информацией о почасовом использовании, а затем как использовать для настройки отчета фильтры, с помощью которых можно запрашивать использование виртуальных машин, баз данных и отмеченных ресурсов в группе ресурсов Azure.

>[!NOTE]
> Сейчас API-интерфейс управления затратами доступен в закрытой предварительной версии.

## <a name="create-a-basic-cost-management-report"></a>Создание базового отчета по управлению затратами

Чтобы определить, как генерируются отчеты о расходах и где они будут публиковаться, в API-интерфейсе управления затратами необходимо использовать операцию `reports`.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

Для этого также требуется параметр `{subscriptionGuid}`, который должен содержать идентификатор подписки, который может быть считан с помощью учетных данных, предоставленных в токене API. `{reportName}`

Ниже приведены обязательные заголовки. 

|Заголовок запроса|ОПИСАНИЕ|  
|--------------------|-----------------|  
|*Content-Type:*| Обязательный элемент. Задайте значение `application/json`. |  
|*Authorization:*| Обязательный элемент. Для токена `Bearer` необходимо задать допустимое значение. |

В теле запроса HTTP необходимо настроить параметры отчета. В приведенном ниже примере отчет настроен для ежедневного создания, представляет собой файл CSV, записанный в контейнер больших двоичных объектов хранилища Azure, и содержит почасовую информацию о стоимости всех ресурсов в группе ресурсов `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Атрибут 

## <a name="filtering-reports"></a>Фильтрация отчетов

При создании отчета разделы `filter` и `dimensions` тела запроса позволяют сосредоточиться на стоимости отдельных типов ресурсов. В предыдущем теле запроса показано, как фильтровать все ресурсы в регионе. 

### <a name="get-all-compute-usage"></a>Получение всех сведений об использовании вычислений

Чтобы создать отчет о стоимости виртуальной машины Azure в подписке для всех регионов, используйте измерение `ResourceType`.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Получение всех сведений об использовании базы данных

Чтобы создать отчет о стоимости Базы данных SQL Azure в подписке для всех регионов, используйте измерение `ResourceType`.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Отчеты об определенных экземплярах

Измерение `Resource` позволяет создавать отчеты о стоимости определенных ресурсов.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Изменение временных рамок

Чтобы установить временную рамку, которая будет находиться за пределами встроенных недели и месяца, задайте определению `timeframe` значение `Custom`.

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Дополнительная информация
- [Начало работы с Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
