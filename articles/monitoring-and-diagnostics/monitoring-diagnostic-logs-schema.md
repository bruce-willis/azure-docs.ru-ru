---
title: Поддерживаемые службы и схемы в журналах Системы диагностики Azure
description: Узнайте о поддерживаемых схемах служб и событий для журналов диагностики Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 6/08/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 45595893a199b845c8b010bc1e2545b89aa688cd
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264985"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Поддерживаемые службы, схемы и категории для журналов диагностики Azure

[Журналы диагностики ресурсов Azure](monitoring-overview-of-diagnostic-logs.md) — это журналы, созданные ресурсом Azure, описывающие работу данного ресурса. Эти журналы создаются для конкретного типа ресурсов. В этой статье представлены поддерживаемые службы и схема событий для событий, созданных в каждой службе. В эту статью также включен полный список доступных категорий журналов для каждого типа ресурсов.

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>Поддерживаемые службы и схемы для журналов диагностики ресурсов
Схема для журналов диагностики ресурсов зависит от типа ресурса и категории журнала.   

| Service | Схемы и документы |
| --- | --- |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| Управление API | [Журналы диагностики управления API](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| Шлюзы приложений |[Ведение журнала диагностики для шлюза приложений](../application-gateway/application-gateway-diagnostics.md) |
| Служба автоматизации Azure |[Log Analytics для службы автоматизации Azure](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Пакетная служба Azure |[Ведение журналов диагностики пакетной службы Azure](../batch/batch-diagnostics.md) |
| Сеть доставки содержимого | [Журналы Системы диагностики Azure для CDN](../cdn/cdn-azure-diagnostic-logs.md) |
| Cosmos DB | [Журнал ведения диагностики Azure Cosmos DB](../cosmos-db/logging.md) |
| Фабрика данных | [Мониторинг фабрик данных с помощью Azure Monitor](../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[Доступ к журналам диагностики для Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[Доступ к журналам диагностики Azure Data Lake Store](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| База данных для PostgreSQL |  Схема недоступна. |
| Концентраторы событий |[Журналы диагностики концентраторов событий Azure](../event-hubs/event-hubs-diagnostic-logs.md) |
| ExpressRoute | Схема недоступна. |
| Центр Интернета вещей | [Использование Azure Monitor](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Ведение журнала хранилища ключей Azure](../key-vault/key-vault-logging.md) |
| Load Balancer |[Log Analytics для Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[Настраиваемая схема отслеживания сообщений B2B для приложений логики](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| группы сетевой безопасности; |[Аналитика журналов для групп безопасности сети](../virtual-network/virtual-network-nsg-manage-log.md) |
| Защита от атак DDoS | [Управление службой "Защита от атак DDoS Azure" уровня "Стандартный"](../virtual-network/manage-ddos-protection.md) |
| Выделенная служба PowerBI | Схема недоступна. |
| Службы восстановления | [Модель данных для Azure Backup](../backup/backup-azure-reports-data-model.md)|
| поиска |[Включение и использование аналитики поискового трафика](../search/search-traffic-analytics.md) |
| Служебная шина Azure |[Журналы диагностики служебной шины Azure](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| База данных SQL | [Метрики и журналы диагностики базы данных SQL Azure](../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[Журналы диагностики задания](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| Диспетчер трафика | Схема недоступна. |
| Виртуальные сети | Схема недоступна. |
| Шлюзы виртуальной сети | Схема недоступна. |

## <a name="supported-log-categories-per-resource-type"></a>Поддерживаемые категории журнала для каждого типа ресурса
|Тип ресурса|Категория|Отображаемое имя категории|
|---|---|---|
|Microsoft.AnalysisServices/servers|Двигатель|Двигатель|
|Microsoft.AnalysisServices/servers|Service|Service|
|Microsoft.ApiManagement/service|GatewayLogs|Журналы, относящихся к шлюзу ApiManagement.|
|Microsoft.Automation/automationAccounts|JobLogs|Журналы заданий|
|Microsoft.Automation/automationAccounts|JobStreams|Потоки заданий|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Состояние узла DSC.|
|Microsoft.Batch/batchAccounts|ServiceLog|Журналы служб|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|Возвращает метрики конечной точки, например пропускную способность, исходящий трафик и т. д.|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories;|ActivityRuns|Журнал выполнения действий конвейера|
|Microsoft.DataFactory/factories;|PipelineRuns|Журнал запусков конвейера|
|Microsoft.DataFactory/factories;|TriggerRuns|Журнал запусков триггера|
|Microsoft.DataLakeAnalytics/accounts|Аудит|Журналы аудита|
|Microsoft.DataLakeAnalytics/accounts|Запросы|Журналы запросов|
|Microsoft.DataLakeStore/accounts|Аудит|Журналы аудита|
|Microsoft.DataLakeStore/accounts|Requests|Журналы запросов|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|Журналы сервера PostgreSQL|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLBackupEvents|События резервного копирования PostgreSQL|
|Microsoft.Devices/IotHubs|Подключения|Подключения|
|Microsoft.Devices/IotHubs|DeviceTelemetry|Телеметрия устройства|
|Microsoft.Devices/IotHubs|C2DCommands|Команды, отправляемые из облака на устройство|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|Операции с удостоверениями устройства|
|Microsoft.Devices/IotHubs|FileUploadOperations|Операции отправки файлов|
|Microsoft.Devices/IotHubs|Маршруты|Маршруты|
|Microsoft.Devices/IotHubs|Операции переноса с двойника устройства в облако|Операции переноса с двойника устройства в облако|
|Microsoft.Devices/IotHubs|C2DTwinOperations|Операции переноса из облака на двойник устройства|
|Microsoft.Devices/IotHubs|TwinQueries|Запросы к двойникам|
|Microsoft.Devices/IotHubs|JobsOperations|Операции заданий|
|Microsoft.Devices/IotHubs|DirectMethods|Прямые методы|
|Microsoft.Devices/IotHubs|E2EDiagnostics|Диагностика E2E (предварительная версия)|
|Microsoft.Devices/provisioningServices|DeviceOperations|Операции с устройствами|
|Microsoft.Devices/provisioningServices|ServiceOperations|Операции со службой|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|Журналы архивации|
|Microsoft.EventHub/namespaces|OperationalLogs|Журналы операций|
|Microsoft.EventHub/namespaces|AutoScaleLogs|Журналы автомасштабирования.|
|Microsoft.KeyVault/vaults|AuditEvent|Журналы аудита|
|Microsoft.Logic/workflows|WorkflowRuntime|События диагностики среды выполнения рабочего процесса|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|Integration Account track events|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|Событие группы безопасности сети|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|Счетчик правил группы безопасности сети|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|События оповещения балансировщика нагрузки|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|Состояние работоспособности балансировщика нагрузки|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|Уведомления о защите от атак DDoS|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|Оповещения о защите виртуальной машины|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|Журнал доступа к шлюзу приложений|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|Журнал производительности шлюза приложений|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|Журнал брандмауэра шлюза приложений|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|Журналы диагностики шлюзов|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|Журналы диагностики туннелей|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|Журналы диагностики маршрутов|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|Журналы диагностики IKE|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|Журналы диагностики P2S|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|Событие вывода результатов проверки работоспособности диспетчера трафика|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|Таблица счетчиков GWM.|
|Microsoft.PowerBIDedicated/capacities|Двигатель|Двигатель|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Данные отчетов службы архивации Azure|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Задания Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|События Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Реплицированные элементы Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Статистика репликации Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Точки восстановления Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Периодичность отправки данных репликации Azure Site Recovery|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Обновление данных защищенного диска Azure Site Recovery|
|Microsoft.Search/searchServices|OperationLogs|Журналы операций|
|Microsoft.ServiceBus/namespaces|OperationalLogs|Журналы операций|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|Статистика среды выполнения хранилища запросов|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|Статистика времени ожидания хранилища запросов|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|Статистика времени ожидания базы данных|
|Microsoft.Sql/servers/databases|Время ожидания|Время ожидания|
|Microsoft.Sql/servers/databases|Блоки|Блоки|
|Microsoft.Sql/servers/databases|SQLInsights|Аналитика SQL|
|Microsoft.Sql/servers/databases|Аудит|Журналы аудита|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|Событие аудита безопасности SQL|
|Microsoft.StreamAnalytics/streamingjobs|Выполнение|Выполнение|
|Microsoft.StreamAnalytics/streamingjobs|Разработка|Разработка|

## <a name="next-steps"></a>Дальнейшие действия

* [Сбор и использование данных журнала из ресурсов Azure](monitoring-overview-of-diagnostic-logs.md)
* [Потоковая передача журналов диагностики Azure в **концентраторы событий**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Создание или обновление диагностического параметра](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Сбор журналов и метрик для служб Azure для использования в Log Analytics](../log-analytics/log-analytics-azure-storage.md)
