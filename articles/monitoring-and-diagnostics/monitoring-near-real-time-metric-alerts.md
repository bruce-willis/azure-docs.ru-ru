---
title: Поддерживаемые ресурсы для новой версии оповещений о метриках Azure Monitor
description: Справочник по поддерживаемым метрикам и журналам для новых оповещений на основе метрик Azure почти в реальном времени.
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 019f245a7cc1feedcbd520c336f53a60ed1e9ddc
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45579095"
---
# <a name="supported-resources-for-newer-azure-monitor-metric-alerts"></a>Поддерживаемые ресурсы для новой версии оповещений о метриках Azure Monitor
Azure Monitor теперь поддерживает [новый тип оповещений о метриках](monitoring-overview-unified-alerts.md), который имеет ряд преимуществ перед [классическими оповещениями](insights-alerts-portal.md). Метрики доступны для [большого числа служб Azure](monitoring-supported-metrics.md). Список типов ресурсов, поддерживаемых новыми оповещениями, постоянно расширяется. В этой статье рассматриваются новые возможности. 

Новые оповещения на основе метрик можно применять для популярных журналов Log Analytics, извлекаемых в виде метрик из журналов (предварительная версия)  
- [Счетчики производительности](../log-analytics/log-analytics-data-sources-performance-counters.md) для компьютеров Windows и Linux.
- [Записи пульсов для решения "Работоспособность агентов"](../operations-management-suite/oms-solution-agenthealth.md).
- Записи [управления обновлениями](../operations-management-suite/oms-solution-update-management.md).
- Журналы [данных событий](../log-analytics/log-analytics-data-sources-windows-events.md)
 
> [!NOTE]
> Конкретная метрика и (или) измерение отображается, только если для него есть данные за выбранный период. Эти метрики доступны для клиентов с рабочими областями Azure Log Analytics в восточной части США, центрально-западной части США и в Западной Европе. Метрики из Log Analytics сейчас находятся в общедоступной предварительной версии и могут быть изменены.

## <a name="portal-powershell-cli-rest-support"></a>Поддержка портала, PowerShell, CLI и REST
В настоящее время новые оповещения на основе метрик можно создать только на портале Azure, с помощью [REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate) или [шаблонов диспетчера ресурсов](monitoring-create-metric-alerts-with-templates.md). Возможность настройки новых оповещений с помощью PowerShell и интерфейса командной строки Azure (Azure CLI 2.0) будет реализована в ближайшее время.

## <a name="metrics-and-dimensions-supported"></a>Поддерживаемые метрики и измерения
Новые оповещения на основе метрик поддерживают оповещения для метрик, использующих измерения. Измерения можно использовать для фильтрации метрик до необходимого уровня. Все поддерживаемые метрики с применимыми измерениями можно изучить и визуализировать с помощью [обозревателя метрик Azure Monitor (предварительная версия)](monitoring-metric-charts.md).

Вот полный список источников метрик Azure Monitor, которые поддерживаются в новых оповещениях:

|Тип ресурса  |Поддерживаемые измерения  | Доступные метрики|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | Yes        | [Управление API](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     Yes   | [Учетные записи службы автоматизации](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | Недоступно| [Учетные записи пакетной службы](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis;     |    Недоступно     |[Кэш Redis](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    Недоступно     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    Недоступно     | [Виртуальные машины](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets;     |   Недоступно      |[Масштабируемые наборы виртуальных машин](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | Yes| [Группы контейнеров](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | Yes | [Управляемые кластеры](monitoring-supported-metrics.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| Yes| [Фабрики данных V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories;     |   Yes     |[Фабрики данных V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   Недоступно      |[База данных для MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    Недоступно     | [База данных для PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  Yes      |[Центры событий](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| Нет  | [Хранилища](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     Недоступно    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    Недоступно     | [Шлюзы приложений](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | Недоступно |  [Цепи Express Route](monitoring-supported-metrics.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | Недоступно| [Зоны DNS](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (только для SKU "Стандартный")| Yes| [Подсистемы балансировки нагрузки.](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses;     |  Недоступно       |[Общедоступные IP-адреса](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | Недоступно | [Емкости](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | Yes | [Профили диспетчера трафика](monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   Недоступно      |[Службы поиска](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  Yes       |[Служебная шина](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    Yes     | [Учетные записи хранения](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     Yes    | [Службы BLOB-объектов](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices), [службы файлов](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices), [службы очередей](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices) и [службы таблиц](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  Недоступно       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | Yes | [Планы службы приложений](monitoring-supported-metrics.md#microsoftwebserverfarms)  |
|Microsoft.OperationalInsights/workspaces (предварительная версия) | Yes|[Рабочие области Log Analytics](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)|



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
