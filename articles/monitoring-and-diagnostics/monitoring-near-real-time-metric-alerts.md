---
title: Поддерживаемые ресурсы для новой версии оповещений о метриках Azure Monitor
description: Справочник по поддерживаемым метрикам и журналам для новых оповещений на основе метрик Azure почти в реальном времени.
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: d5eaa4dafc9c155d3e6f85bc67c578c8a12da7cf
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264516"
---
# <a name="supported-metrics-and-creation-methods-for-new-metric-alerts"></a>Поддерживаемые метрики и способы создания новых оповещений о метриках
Azure Monitor теперь поддерживает [новый тип оповещений о метриках](monitoring-overview-unified-alerts.md), который имеет ряд преимуществ перед [классическими оповещениями](insights-alerts-portal.md). Старые оповещения поддерживают [большой список метрик](monitoring-supported-metrics.md). Новые же оповещения поддерживают не только этот список, но и постоянно добавляемые новые возможности. В этой статье рассматриваются новые возможности. 

## <a name="portal-powershell-cli-rest-support"></a>Поддержка портала, PowerShell, CLI и REST
В настоящее время новые оповещения на основе метрик можно создать только на портале Azure, с помощью [REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate) или [шаблонов диспетчера ресурсов](monitoring-create-metric-alerts-with-templates.md). Скоро будет доступна возможность настраивать новые оповещения с помощью PowerShell и интерфейса командной строки Azure (Azure CLI 2.0).

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Новые оповещения на основе метрик поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor (предварительная версия)](monitoring-metric-charts.md).

Вот полный список источников метрик Azure Monitor, которые поддерживаются в новых оповещениях:

|Тип ресурса  |Поддерживаемые измерения  | Доступные метрики|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Yes        | [Управление API](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Yes   | [Учетные записи службы автоматизации](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Недоступно| [Учетные записи пакетной службы](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis;     |    Недоступно     |[Кэш Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    Недоступно     | [Виртуальные машины](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets;     |   Недоступно      |[Масштабируемые наборы виртуальных машин](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Yes| [Группы контейнеров](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| Yes| [Фабрики данных V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories;     |   Yes     |[Фабрики данных V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Недоступно      |[База данных для MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Недоступно     | [База данных для PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Yes      |[Концентраторы событий](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Нет  | [Хранилища](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     Недоступно    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Недоступно     | [Шлюзы приложений](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones/* | Недоступно| [Зоны DNS](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (только для SKU "Стандартный")| Yes| [Подсистемы балансировки нагрузки.](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses;     |  Недоступно       |[Общедоступные IP-адреса](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | Недоступно | [Емкости](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   Недоступно      |[Службы поиска](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Yes       |[Служебная шина](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Yes     | [Учетные записи хранения](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Yes    | [Службы BLOB-объектов](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [службы файлов](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [службы очередей](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) и [службы таблиц](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Недоступно       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    Недоступно     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (предварительная версия) | Yes|[Рабочие области Log Analytics](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>Журналы Log Analytics в качестве метрик для оповещений 

Новые оповещения на основе метрик можно применять для популярных журналов Log Analytics, извлекаемых в виде метрик из данных предварительного просмотра журналов.  
- [Счетчики производительности](../log-analytics/log-analytics-data-sources-performance-counters.md) для компьютеров Windows и Linux.
- [Записи пульсов для решения "Работоспособность агентов"](../operations-management-suite/oms-solution-agenthealth.md).
- Записи [управления обновлениями](../operations-management-suite/oms-solution-update-management.md).
 
> [!NOTE]
> Конкретная метрика и (или) измерение отображается, только если для него есть данные за выбранный период. Эти метрики доступны для клиентов, у которых рабочие области расположены в восточной части США, западной центральной части США или Западной Европе, и которые зарегистрировались для использования предварительной версии. Если вы хотите поработать с этой предварительной версией, зарегистрируйтесь при помощи [этого опроса](https://aka.ms/MetricLogPreview).

Поддерживаются следующие источники метрик на основе журналов Log Analytics:

Имя и описание метрики  |Поддерживаемые измерения  | Тип журнала  |
|---------|---------|---------|
|Average_Avg. время чтения с диска (с)     |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Avg. время записи на диск (с)     |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Current Disk Queue Length   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Disk Reads/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Disk Transfers/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|   Average_% Free Space    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Available MBytes     |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_% Committed Bytes In Use    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
| Average_Bytes Received/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|  Average_Bytes Sent/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|  Average_Bytes Total/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|  Average_% Processor Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|   Average_Processor Queue Length    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Windows      |
|   Average_% Free Inodes   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Free Space   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Used Inodes  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Used Space   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Read Bytes/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Reads/sec |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Transfers/sec |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Write Bytes/sec   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Disk Writes/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Free Megabytes |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Logical Disk Bytes/sec |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Available Memory |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Available Swap Space |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Used Memory  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Used Swap Space  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Available MBytes Memory    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Available MBytes Swap  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Page Reads/sec |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Page Writes/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Pages/sec  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Used MBytes Swap Space |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Used Memory MBytes |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Bytes Transmitted    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Bytes Received   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Bytes    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Packets Transmitted  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Packets Received |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Rx Errors    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Tx Errors    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Total Collisions   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Avg. время чтения с диска (с) |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Avg. время обращения к диску (с) |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Avg. время записи на диск (с)    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Physical Disk Bytes/sec    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Pct Privileged Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Pct User Time  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Used Memory kBytes |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Virtual Shared Memory  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% DPC Time |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Idle Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Interrupt Time   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% IO Wait Time |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Nice Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Privileged Time  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% Processor Time   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_% User Time    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Free Physical Memory   |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Free Space in Paging Files |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Free Virtual Memory    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Processes  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Size Stored In Paging Files    |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Uptime |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Average_Users  |     Да — Computer, ObjectName, InstanceName, CounterPath и SourceSystem.    |   Счетчик производительности Linux      |
|    Пульс  |     Да — Computer, OSType, Version и SourceComputerId.    |   Записи пульсов |
|    Блокировка изменений |     Да — Computer, Product, Classification, UpdateState, Optional и Approved.    |   управление обновлениями; |



## <a name="payload-schema"></a>Схема полезных данных

Если используется соответствующим образом настроенная [группа действий](monitoring-action-groups.md), то операция POST содержит следующие полезные данные и схему JSON для всех новых оповещений на основе метрик:

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

* Получите дополнительные сведения об [интерфейсе оповещений](monitoring-overview-unified-alerts.md).
* Ознакомьтесь со сведениями об [оповещениях журналов в Azure](monitor-alerts-unified-log.md).
* [Подробнее об оповещениях в Azure](monitoring-overview-alerts.md)
