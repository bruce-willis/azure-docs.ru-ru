---
title: Ведение журналов и аудит Azure | Документы Майкрософт
description: Узнайте, как использовать данные журналов для получения подробных сведений о приложении.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 93b0a7e382c27cab5f050166ec8fa89fc7cf6b96
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576108"
---
# <a name="azure-logging-and-auditing"></a>Ведение журналов и аудит Azure

Azure предоставляет множество настраиваемых параметров ведения журналов и аудита безопасности, которые помогают выявить недочеты в политиках и механизмах безопасности. Эта статья описывает создание, сбор и анализ журналов безопасности из служб, размещенных в Azure.

> [!Note]
> Выполнение некоторых рекомендаций в этой статье может привести к более интенсивному использованию данных, а также сетевых и вычислительных ресурсов, а значит, и к дополнительным затратам на лицензии или подписки.

## <a name="types-of-logs-in-azure"></a>Типы журналов в Azure
Облачные приложения являются сложными и содержат множество динамических частей. Журналы предоставляют данные для обеспечения непрерывной работы приложений. Журналы помогают устранять имеющиеся проблемы и предотвращать потенциальные. Кроме того, они могут помочь повысить производительность приложения и улучшить возможности обслуживания, а также автоматизировать действия, которые в противном случае выполнялись бы вручную.

Журналы Azure делятся по следующим категориям:
* **Журналы управления и контроля** содержат информацию об операциях CREATE, UPDATE и DELETE в Azure Resource Manager. Дополнительные сведения см. в разделе [Журналы действий Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).

* **Журналы плоскости данных** предоставляют информацию о событиях, возникающих при использовании ресурсов Azure. К этому типу относятся, например, журналы системы событий Windows, журналы безопасности и журналы приложений на виртуальной машине, а также [журналы диагностики](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), настроенные с помощью Azure Monitor.

* Журналы **обработанных событий** предоставляют сведения о проанализированных событиях и оповещениях, которые были обработаны от вашего имени. К примерам таких событий относятся [оповещения центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Обработав и проанализировав вашу подписку, [центр безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) предоставляет лаконичные оповещения безопасности.

В следующей таблице перечислены наиболее важные типы журналов, доступных в Azure:

| Категория журнала | Тип журнала | Использование | Интеграция |
| ------------ | -------- | ------ | ----------- |
|[Журналы действий](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|События уровня управления с ресурсами Azure Resource Manager.|   Предоставляет информацию об операциях, которые выполнялись с ресурсами в подписке.|    REST API, [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs).|
|[Журналы диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|Своевременные данные об операциях с ресурсами Azure Resource Manager в подписке.|    Информация об операциях, выполняемых самим ресурсом.| Azure Monitor, [Stream](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).|
|[Отчеты Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)|Журналы и отчеты. | Предоставляет сведения о действиях входа пользователей и системных действиях по управлению пользователями и группами.|[Graph API](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Виртуальные машины и облачные службы](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm)|Служба журнала событий Windows и системный журнал Linux|  Собирают системные данные и данные журналов, поступающие от виртуальных машин, и передает их в указанную вами учетную запись хранения.|   Windows (с использованием хранилища системы диагностики Microsoft Azure [[WAD](https://docs.microsoft.com/azure/azure-diagnostics)]) и Linux в Azure Monitor|
|[Аналитика службы хранилища Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|Обеспечивает ведение журнала хранилища, предоставляет данные метрик для учетной записи хранения.|Предоставляет сведения о трассировке запросов, анализирует тенденции использования и диагностирует проблемы учетной записи хранения.|   REST API или [клиентская библиотека](https://msdn.microsoft.com/library/azure/mt347887.aspx).|
|[Журналы потоков для группы безопасности сети (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Использует формат JSON, показывает входящие и исходящие потоки на основе правил.|Позволяет просмотреть сведения о входящем и исходящем IP-трафике в группе безопасности сети.|[Наблюдатель за сетями Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application insight](https://docs.microsoft.com/azure/application-insights/app-insights-overview)|Журналы, исключения и пользовательские системы диагностики.|   Служба наблюдения за производительностью приложений (APM) для веб-разработчиков на нескольких платформах.| REST API, [Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).|
|Обработка данных/оповещения системы безопасности|    Оповещения центра безопасности Azure, оповещения Azure Log Analytics.|   Предоставляет сведения о безопасности и оповещения.|  Интерфейсы REST API, JSON.|

### <a name="activity-logs"></a>Журналы действий
[Журналы действий Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) содержат информацию об операциях, которые выполнялись с ресурсами в подписке. Журналы действий раньше назывались "журналами аудита" или "операционными журналами", так как содержат связанные с подписками [события уровня управления](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/). 

Журналы действий помогают определить, что нужно сделать, с чем и когда, для операций записи (то есть PUT, POST или DELETE). Журналы действий также помогают отслеживать состояние операции и другие ее свойства. Журнал действий не содержит сведений об операциях чтения (GET).

В этой статье PUT, POST и DELETE означают все операции записи с ресурсами, зарегистрированные в журнале действий. Например, журналы действий можно использовать для поиска ошибки при устранении неполадок, а также для наблюдения за тем, как пользователь в вашей организации изменил ресурс.

![Схема журналов действий](./media/azure-log-audit/azure-log-audit-fig1.png)

События из журнала действий можно получить с помощью портала Azure, [Azure CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), командлетов PowerShell или [REST API Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Данные в журналах действий хранятся в течение 90 дней.

Сценарии интеграции для события журнала действий:

* [Создание оповещений электронной почты или веб-перехватчика, активируемого событием журнала действий](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email).

* [Потоковая передача журнала в концентратор событий](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) для приема сторонней службой или пользовательским аналитическим решением, например PowerBI.

* Анализ журнала в PowerBI с помощью [пакета содержимого PowerBI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).

* [Сохранение журнала в учетную запись хранения для архивации или проверки вручную](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log). В профилях журнала можно задать время хранения (в днях).

* Выполнение запросов и просмотр журнала на портале Azure.

* Обращение к журналу с помощью командлетов PowerShell, Azure CLI или REST API.

* Экспорт журнала действий с профилями журнала в [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

Вы можете использовать учетную запись хранения или [пространство имен концентратора событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive), не входящее в подписку, в которой создаются журналы. Пользователю, который настраивает этот параметр, должен быть предоставлен соответствующий уровень доступа [по управлению доступом на основе ролей RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) к обеим подпискам.

### <a name="azure-diagnostics-logs"></a>Журналы диагностики Azure
Журналы диагностики Azure выдаются ресурсом и содержат подробные и своевременные данные о работе этого ресурса. Содержимое этих журналов зависит от типа ресурса. Например, [системные журналы событий Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) — это категория журналов диагностики для виртуальных машин, а [журналы больших двоичных объектов, таблиц и очередей](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) — категории журналов диагностики для учетных записей хранения. Журналы диагностики отличаются от журналов действий, которые содержат информацию об операциях, выполняемых с ресурсами в подписке.

![Схемы журналов диагностики Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Журналы диагностики Azure можно настроить несколькими способами, в том числе с помощью портала Azure, PowerShell, Azure CLI и REST API.

**Сценарии интеграции**

* Сохранение журналов в [учетную запись хранения](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) для аудита или проверки вручную. В параметрах диагностики вы также можете задать время хранения (в днях).

* [Потоковая передача журналов в концентраторы событий](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) для обработки в сторонней службе или пользовательском аналитическом решении, например в [PowerBI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/).

* Анализ журналов с помощью [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

**Поддерживаемые службы, схема для журналов диагностики и поддерживаемые категории журналов для каждого типа ресурса**


| Service | Схема и документация | Тип ресурса | Категория |
| ------- | ------------- | ------------- | -------- |
|Azure Load Balancer| [Log Analytics для Load Balancer (предварительная версия)](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers<br>Microsoft.Network/loadBalancers| LoadBalancerAlertEvent<br>LoadBalancerProbeHealthStatus|
|группы сетевой безопасности;|[Log Analytics для групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups<br>Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent<br>NetworkSecurityGroupRuleCounter|
|Шлюз приложений Azure|[Ведение журнала диагностики для шлюза приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways<br>Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog<br>ApplicationGatewayPerformanceLog<br>ApplicationGatewayFirewallLog|
|Хранилище ключей Azure|[Журналы Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|поиск Azure;|[Включение и использование аналитики поискового трафика](https://docs.microsoft.com/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Хранилище озера данных Azure|[Доступ к журналам диагностики для Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts<br>Microsoft.DataLakeStore/accounts|Аудит<br>Requests|
|Аналитика озера данных Azure|[Доступ к журналам диагностики для Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts<br>Microsoft.DataLakeAnalytics/accounts|Аудит<br>Requests|
|Azure Logic Apps|[Настраиваемая схема отслеживания сообщений B2B для приложений логики](https://docs.microsoft.com/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows<br>Microsoft.Logic/integrationAccounts|WorkflowRuntime<br>IntegrationAccountTrackingEvents|
|Пакетная служба Azure|[Журналы диагностики пакетной службы Azure](https://docs.microsoft.com/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Служба автоматизации Azure|[Log Analytics для службы автоматизации Azure](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts<br>Microsoft.Automation/automationAccounts|JobLogs<br>JobStreams|
|Центры событий Azure|[Журналы диагностики Центров событий](https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces<br>Microsoft.EventHub/namespaces|ArchiveLogs<br>OperationalLogs|
|Azure Stream Analytics|[Журналы диагностики заданий](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs<br>Microsoft.StreamAnalytics/streamingjobs|Выполнение<br>Разработка|
|Azure Service Bus|[Журналы диагностики служебной шины](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Отчеты Azure Active Directory
Azure Active Directory (Azure AD) формирует отчеты о безопасности, активности и аудите для пользователей каталога. [Отчет аудита Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) позволяет вам определить привилегированные действия, выполняемые в пользовательском экземпляре Azure AD. К привилегированным действиям относятся изменение повышения прав (например, создание роли или сброс пароля), изменение конфигурации политики (например, политики паролей) или изменение конфигурации каталога (например, изменение параметров федерации домена).

Отчеты содержат записи аудита для имени события, выполнившего действие субъекта, подвергшегося изменению целевого ресурса, а также даты и времени (в формате UTC). Пользователи могут получить список событий аудита для Azure AD на [портале Azure](https://portal.azure.com/), как описано в разделе [Просмотр журналов аудита](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

В следующей таблице приведены включенные отчеты:

| Отчеты о безопасности | Отчеты об активности | Отчеты об аудите |
| :--------------- | :--------------- | :------------ |
|Попытки входа из неизвестных источников| Использование приложения: сводка| Отчет об аудите каталога|
|"Операции входа после нескольких неудачных попыток";|  Использование приложения: подробности||
|"Операции входа из нескольких географических регионов".|    Панель мониторинга приложений||
|Попытки входа с IP-адресов с подозрительными действиями|   Ошибки подготовки учетной записи||
|Нестандартные действия при входе|    Устройства отдельного пользователя||
|Попытки входа с возможно инфицированных устройств|   Активность отдельного пользователя||
|Пользователи с аномальными событиями при входе| Отчет о действиях групп||
||Отчет о событиях регистрации для сброса пароля||
||Действие сброса пароля|||

Данные этих отчетов могут быть полезными для приложений, например систем управления информационной безопасностью и событиями безопасности (SIEM), а также инструментов аудита и бизнес-аналитики. Интерфейсы API отчетов Azure AD предоставляют программный доступ к данным с помощью набора интерфейсов API на базе REST. Эти [интерфейсы API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) можно вызвать, используя различные языки программирования и инструменты.

События в отчете аудита Azure AD хранятся в течение 180 дней.

> [!Note]
> Дополнительные сведения о хранении отчетов см. в разделе [Политики хранения отчетов Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Если вам нужно хранить события аудита дольше, используйте API отчетов для регулярного извлечения [событий аудита](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) в отдельное хранилище данных.

### <a name="virtual-machine-logs-that-use-azure-diagnostics"></a>Журналы виртуальных машин, использующие систему диагностики Azure
[Система диагностики Azure](https://docs.microsoft.com/azure/azure-diagnostics) позволяет выполнять сбор диагностических данных развернутого приложения. Вы можете использовать расширение диагностики из любого из нескольких источников. Сейчас поддерживаются [веб-роли и рабочие роли в облачной службе Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me).

![Журналы виртуальных машин, использующие систему диагностики Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

### <a name="azure-virtual-machineshttpsazuremicrosoftcomdocumentationlearning-pathsvirtual-machines-that-are-running-microsoft-windows-and-service-fabrichttpsdocsmicrosoftcomazureservice-fabricservice-fabric-overview"></a>[Виртуальные машины Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) под управлением Microsoft Windows и [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

Включить систему диагностики Azure на виртуальной машине можно следующими способами:

* [Использование Visual Studio для трассировки виртуальных машин Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

* [Удаленная настройка системы диагностики Azure на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

* [Использование PowerShell для настройки диагностики на виртуальных машинах Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

* [Создание виртуальной машины Windows с мониторингом и диагностикой с использованием шаблона Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>аналитики хранилища
[Аналитика службы хранилища Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) ведет журналы и предоставляет данные метрик для учетной записи хранения. Эти данные можно использовать для трассировки запросов, анализа тенденций использования и диагностики проблем учетной записи хранения. Ведение журнала Аналитики службы хранилища доступно для [служб хранилищ BLOB-объектов, очередей и таблиц Azure](https://docs.microsoft.com/azure/storage/storage-introduction). В аналитике хранилища регистрируется подробная информация об успешных и неудачных запросах к службе хранилища.

Эту информацию можно использовать для мониторинга отдельных запросов и диагностики неполадок в службе хранилища. Запросы вносятся в журнал наилучшим возможным образом. Записи журнала создаются только при получении запроса к конечной точке службы. Например, при обнаружении действия учетной записи хранения в конечной точке службы BLOB-объектов, но не в ее конечных точках служб таблиц или очередей, создаются журналы, которые относятся только к службе хранилища BLOB-объектов.

Для использования Аналитики службы хранилища ее нужно включить отдельно для каждой из отслеживаемых служб. Вы можете включить эту функцию на [портале Azure](https://portal.azure.com/). Дополнительные сведения см. в статье [Мониторинг учетной записи хранения на портале Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Аналитику хранилища также можно включить программно через REST API или клиентскую библиотеку. Используйте операции Set Service Properties, чтобы отдельно включить Storage Analytics для каждой службы.

Объединенные данные хранятся в известном большом двоичном объекте (для ведения журнала) и в известных таблицах (для метрик), доступ к которым можно получить с помощью API служб хранилища BLOB-объектов и таблиц.

В Аналитике службы хранилища установлено ограничение в 20 ТБ для объема хранимых данных, не зависящее от общего ограничения на вашу учетную запись хранения. Все журналы хранятся в [блочных BLOB-объектах](https://docs.microsoft.com/azure/storage/storage-analytics) в контейнере $logs, который автоматически создается при включении Аналитики службы хранилища для учетной записи хранения.

> [!Note]
> * Дополнительные сведения о выставлении счетов и политиках хранения данных см. в разделе [Аналитика службы хранилища и выставление счетов](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> * Дополнительные сведения об ограничениях учетных записей хранения см. в разделе [Целевые показатели масштабируемости и производительности службы хранилища Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Аналитика службы хранилища регистрирует прошедшие проверку подлинности и анонимные запросы следующих типов:

| Аутентифицированные  | Анонимные|
| :------------- | :-------------|
| Успешные запросы. | Успешные запросы. |
|Неудачные запросы, в том числе из-за ошибок, связанных с временем ожидания, регулированием, сетью, авторизацией и др. | Запросы, в которых используется подписанный URL-адрес, в том числе неудачные и успешные запросы. |
| Запросы, в которых используется подписанный URL-адрес, в том числе неудачные и успешные запросы. |Ошибки времени ожидания для клиента и сервера. |
|   Запросы к данным аналитики. |    Неудачные запросы GET с кодом ошибки 304 (не изменено). |
| Запросы, выполненные в самой аналитике хранилища, такие как запросы на создание или удаление журнала, не регистрируются. Полный список регистрируемых данных приведен в разделах [Операции с протоколированием и сообщения о состоянии Аналитики службы хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) и [Формат журналов Аналитики службы хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). | Остальные неудачные анонимные запросы не регистрируются. Полный список регистрируемых данных приведен в разделах [Операции с протоколированием и сообщения о состоянии Аналитики службы хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) и [Формат журналов Аналитики службы хранилища](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Журналы сетей Azure
Мониторинг и ведение журнала сетей в Azure выполняется комплексно и охватывает две обширные категории.

* [Наблюдатель за сетями](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher). Мониторинг сети на основе сценариев обеспечивается с помощью функций Наблюдателя за сетями. Эта служба включает в себя запись пакетов, определение следующего прыжка, проверку IP-потока, представление групп безопасности и журналы потоков NSG. В отличие от мониторинга отдельных сетевых ресурсов, мониторинг на уровне сценария обеспечивает комплексное представление сетевых ресурсов.

* [Мониторинг ресурсов](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring). Мониторинг на уровне ресурсов базируется на четырех компонентах. Это журналы диагностики, метрики, устранение неполадок и работоспособность ресурсов. Все эти компоненты создаются на уровне сетевого ресурса.

![Журналы сетей Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

Наблюдатель за сетями — это региональная служба, обеспечивающая мониторинг и диагностику условий на уровне сетевого сценария на платформе Azure. Инструменты диагностики сети и визуализации, доступные в Наблюдателе за сетями, помогают понять, как работает сеть в Azure, диагностировать ее и получить ценную информацию.

### <a name="network-security-group-flow-logging"></a>Ведение журнала потоков для группы безопасности сети

[Журналы потоков для групп безопасности сети (NSG)](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) являются функцией Наблюдателя за сетями, которая позволяет просматривать сведения о входящем и исходящем IP-трафике через NSG. Эти журналы потоков сохраняются в формате JSON и содержат следующие сведения:
* Входящие и исходящие потоки для каждого правила.
* Сетевой адаптер, к которому относится данный поток.
* Информация о потоке из 5 кортежей: исходный и конечный IP-адрес, исходный и конечный порт, протокол.
* Разрешен или запрещен трафик.

Хотя журналы потоков предназначены для NSG, они отображаются не так, как другие журналы. Журналы потоков хранятся только в учетной записи хранения.

К журналам потоков применяются те же политики хранения, что и к другим журналам. Для журналов действует политика хранения, в которой можно задать период от 1 до 365 дней. Если политика хранения не задана, то журналы сохраняются неограниченно долго.

**Журналы диагностики**

Периодические и спонтанные события, создаваемые сетевыми ресурсами, регистрируются в журналах в учетных записях хранения, а затем отправляются в концентратор событий или в Log Analytics. Эти журналы содержат подробные сведения о работоспособности ресурса. Их можно просматривать в таких инструментах, как Power BI и Log Analytics. Чтобы узнать, как просматривать журналы диагностики, см. раздел [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

![Раздел "Журналы диагностики"](./media/azure-log-audit/azure-log-audit-fig5.png)

Доступны журналы диагностики для [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [групп безопасности сети](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), маршрутов и [шлюза приложений](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Наблюдатель за сетями имеет представление журналов диагностики. Оно содержит все сетевые ресурсы, поддерживающие ведение журналов диагностики. В этом представлении можно удобно и быстро включать и отключать сетевые ресурсы.


Кроме представленных выше возможностей ведения журнала, сейчас Наблюдатель за сетями обеспечивает следующие функции:
- [Топология](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview). Обеспечивает представление уровня сети, в котором показаны различные взаимодействия и связи между сетевыми ресурсами в группе ресурсов.

- [Изменяемая запись пакетов](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview). Позволяет записывать входящие и исходящие пакеты данных для виртуальной машины. Дополнительные параметры фильтрации и элементы управления, такие как возможность установить ограничения времени и размера, обеспечивают гибкость в работе. Данные пакетов могут храниться в хранилище BLOB-объектов или на локальном диске в виде файлов *CAP*.

* [Проверка IP-потока](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview). Позволяет узнать, разрешен или запрещен пакет, на основе информации о параметрах пакета, указанной в 5 кортежах (IP-адрес назначения, исходный IP-адрес, порт назначения, исходный порт и протокол). Если пакет отклонен группой безопасности, возвращаются правило и группа, которые запретили этот пакет.

* [Определение следующего прыжка](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview). Позволяет определить следующий прыжок для пакетов, маршрутизируемых в сетевой структуре Azure, что позволяет диагностировать любые неправильные определенные пользователем маршруты.

* [Представление групп безопасности](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview). Позволяет просмотреть действующие и примененные правила безопасности, которые применяются к виртуальной машине.

* [Устранение неполадок шлюза виртуальной сети и подключений](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest). Помогает устранять неполадки шлюза виртуальной сети и подключений.

* [Ограничения сетевых ресурсов подписки](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits). Возможность просмотреть данные об использовании сетевых ресурсов относительно установленных ограничений.

### <a name="application-insights"></a>Application Insights

[Azure Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) — это расширяемая служба APM для веб-разработчиков на нескольких платформах. Используйте ее для мониторинга работающих веб-приложений. Она автоматически обнаруживает аномалии производительности. Эта служба включает мощные аналитические средства, которые помогут вам диагностировать проблемы и понять, что пользователи фактически делают в вашем приложении.

Application Insights помогает постоянно улучшать производительность и удобство использования.

Эта служба работает с приложениями на самых разнообразных платформах, включая .NET, Node.js и J2EE, с локальным или облачным размещением. Она интегрируется с процессом DevOps и содержит точки подключения к различным средствам разработки.

![Схема Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

Служба Application Insights предназначена для команды разработчиков. Она позволяет получить сведения о производительности и использовании приложения. Она отслеживает следующее:

* **Частота запросов, время отклика и частота сбоев**. Узнайте, какие страницы наиболее популярны, в какое время дня их посещают чаще всего, а также узнайте о расположении пользователей. Узнайте, какие страницы работают лучше всего. Если при увеличении количества запросов повышается время отклика и частота сбоев, возможно, возникла проблема с ресурсами.

* **Частота зависимостей, время отклика и частота сбоев**. Узнайте, замедляют ли внешние службы вашу работу.

* **Исключения**. Проанализируйте сводные статистические данные или выберите конкретные экземпляры и выполните детализацию трассировки стека и связанных запросов. Исключения сервера и браузера регистрируются.

* **Просмотры страниц и производительность загрузки**. Вы можете получать отчеты из браузеров пользователей.

* **Вызовы AJAX**. Получайте данные о скорости веб-страниц, времени отклика и частоте сбоев.

* **Количество пользователей и сеансов.**

* **Счетчики производительности**. Получайте данные с компьютеров с сервером Windows или Linux, такие как загрузка ЦП, использование памяти и сети.

* **Размещение диагностики**. Получайте данные из Docker или Azure.

* **Журналы диагностики трассировки**. Получайте данные из своего приложения, позволяющие сопоставить события трассировки с запросами.

* **Пользовательские события и метрики**. Получайте данные, которые вы самостоятельно записываете в коде клиента или сервера, для отслеживания бизнес-событий, таких как количество проданных единиц или выигранных игр.

В следующей таблице перечислены и описаны сценарии интеграции:

| Сценарий интеграции | ОПИСАНИЕ |
| --------------------- | :---------- |
|[Сопоставление приложений](https://docs.microsoft.com/azure/application-insights/app-insights-app-map)|Компоненты приложения с основными метриками и оповещениями.||
|[Поиск данных экземпляра в системе диагностики](https://docs.microsoft.com/azure/application-insights/app-insights-diagnostic-search)| Поиск и фильтрация событий, таких как запросы, исключения, вызовы зависимостей, журналы трассировки и просмотры страниц.||
|[Обозреватель метрик для статистических данных](https://docs.microsoft.com/azure/application-insights/app-insights-metrics-explorer)|Просмотр, фильтрация и сегментирование объединенных данных, таких как частоты запросов, ошибок и исключений, время отклика и время загрузки страницы.||
|[Панели мониторинга](https://docs.microsoft.com/azure/application-insights/app-insights-dashboards#dashboards)|Объединение разнородных данных из нескольких ресурсов и их совместное использование с другими пользователями. Идеальное решение для многокомпонентных приложений, а также для непрерывного отображения в комнате команды.||
|[Динамический поток метрик](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)|При развертывании новой сборки просматривайте эти индикаторы производительности в режиме, близком к реальному времени, чтобы убедиться, что все работает правильно.||
|[Аналитика в Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)|Получите ответы на сложные вопросы о производительности и использовании приложения с помощью этого мощного языка запросов.||
|[Автоматические и ручные оповещения](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)|Автоматические оповещения адаптируются к стандартным шаблонам телеметрии приложения и активируются, когда что-то не соответствует стандартному шаблону. Также можно настроить оповещения для определенных уровней пользовательских или стандартных метрик.||
|[Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)|Просматривайте данные производительности в коде. Перейдите к коду из трассировки стека.||
|[Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi)|Интегрируйте метрики использования с другими метриками бизнес-аналитики.||
|[REST API](https://dev.applicationinsights.io/)|Напишите код для выполнения запросов с помощью метрик и необработанных данных.||
|[непрерывный экспорт.](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)|Выполняйте массовый экспорт необработанных данных в хранилище по мере их поступления.||

### <a name="azure-security-center-alerts"></a>оповещения центра безопасности Azure.
Функция обнаружения угроз в центре безопасности Azure автоматически собирает информацию о безопасности из ваших ресурсов Azure, сети и подключенных партнерских решений. Она анализирует эту информацию, часто сравнивая сведения из разных источников и определяя угрозы. Приоритет в центре безопасности получают оповещения безопасности и рекомендации по устранению угроз. Дополнительные сведения см. в статье [Центр безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-intro).

![Схема для центра безопасности Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

В центре безопасности используется расширенная аналитика безопасности, возможности которой значительно превышают возможности подходов, основанных на сигнатурах. В нем достижения в сфере больших данных и [машинного обучения](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) используются для оценки событий во всей облачной структуре. Это позволяет выявлять угрозы, которые невозможно обнаружить вручную, а также прогнозировать тенденции развития атак. Вот что входит в аналитику по вопросам безопасности:

* **Интегрированная аналитика угроз** — поиск вредного кода с помощью глобальной аналитики угроз, которая поступает от продуктов и служб Майкрософт, подразделения Microsoft Digital Crimes Unit (DCU), центра Microsoft Security Response Center (MSRC) и внешних веб-каналов.

* **Поведенческая аналитика** — вредоносное поведение обнаруживается путем сравнения с известными схемами поведения.

* **Обнаружение аномалий** — базовые показатели ретроспективно фиксируются с помощью статистического профилирования. В рамках обнаружения аномалий приходят оповещения о таких отклонениях от базовых показателей, которые свидетельствуют о потенциальных атаках.

Многие операции системы безопасности и групп реагирования на инциденты полагаются на решение SIEM в качестве отправной точки для рассмотрения и исследования оповещений системы безопасности. С помощью интеграции журналов Azure можно синхронизировать оповещения центра безопасности, а также события безопасности виртуальных машин, собранные системой диагностики Azure и журналами аудита, с решением Log Analytics или SIEM в режиме, близком к реальному времени.

## <a name="log-analytics"></a>Log Analytics 

Log Analytics — это служба в Azure, которая помогает собирать и анализировать данные, формируемые ресурсами в облачных и локальных средах. Она предоставляет аналитические данные в режиме реального времени, используя встроенный поиск и настраиваемые панели мониторинга для быстрого анализа миллионов записей по всем рабочим нагрузкам и серверам независимо от их физического местонахождения.

![Схема Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

Ключевым компонентом Log Analytics является рабочая область, которая размещена в Azure. Log Analytics собирает данные в рабочую область из подключенных источников посредством настройки источников данных и добавления решений в подписку. Отдельные источники данных и решения создают различные типы записей, которые имеют собственные наборы свойств. Но в запросах к рабочей области источники и решения могут анализироваться вместе. Это позволяет использовать одни и те же средства и методы для работы с разными видами данных, собранными из различных источников.

Подключенные источники — это компьютеры и другие ресурсы, создающие данные, собираемые службой Log Analytics. К этим источникам относятся агенты, установленные на компьютеры [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) и [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents), подключенные напрямую, или агенты в [подключенной группе управления System Center Operations Manager](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Log Analytics может также собирать данные из [учетной записи хранения Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage).

[Источники данных](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) — это различные виды данных, собираемые из каждого подключенного источника. К этим источникам относятся события и [данные о производительности](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) из агентов [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) и Linux, а также такие источники, как [журналы IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) и [журналы пользовательских текстов](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs). Вы настраиваете каждый источник, с которого нужно получать данные, и конфигурация передается на каждый подключенный источник автоматически.

[Сбор журналов и метрик для служб Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) можно выполнить четырьмя разными способами:
* Отправка диагностики Azure напрямую в Log Analytics (**диагностика** в следующей таблице).

* Отправка диагностики Azure в службу хранилища Azure, а затем — в Log Analytics (**хранилище** в следующей таблице).

* Использование соединителей для служб Azure (**соединители** в следующей таблице).

* Сценарии для сбора и публикации данных в Log Analytics (пустые ячейки в следующей таблице, а также для служб, которые не указаны).

| Service | Тип ресурса | Журналы | Метрики | Решение |
| :------ | :------------ | :--- | :------ | :------- |
|Шлюз приложений Azure| Microsoft.Network/<br>applicationGateways|  Диагностика|Диагностика|    [Анализ шлюзов](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [приложений Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application Insights||     Соединитель|  Соединитель|  [Соединитель Application Insights](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [(предварительная версия)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Учетные записи для службы автоматизации Azure| Microsoft.Automation/<br>AutomationAccounts|    Диагностика||       [Дополнительные сведения](https://docs.microsoft.com/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Учетные записи для пакетной службы Azure|  Microsoft.Batch/<br>batchAccounts|  Диагностика|    Диагностика||
|Классические облачные службы||       служба хранилища.||       [Дополнительные сведения](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Cognitive Services|    Microsoft.CognitiveServices/<br>accounts|       Диагностика|||
|Аналитика озера данных Azure| Microsoft.DataLakeAnalytics/<br>accounts|   Диагностика|||
|Хранилище озера данных Azure| Microsoft.DataLakeStore/<br>accounts|   Диагностика|||
|Пространство имен для концентратора событий Azure| Microsoft.EventHub/<br>namespaces|  Диагностика|    Диагностика||
|Центр Интернета вещей Azure| Microsoft.Devices/<br>IotHubs||     Диагностика||
|Хранилище ключей Azure|   Microsoft.KeyVault/<br>vaults|  Диагностика  || [Анализ хранилища ключей](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault)|
|Azure Load Balancer|   Microsoft.Network/<br>loadBalancers|    Диагностика|||
|Azure Logic Apps|  Microsoft.Logic/<br>workflows|  Диагностика|    Диагностика||
||Microsoft.Logic/<br>integrationAccounts||||
|группы сетевой безопасности;|   Microsoft.Network/<br>networksecuritygroups|Диагностика||   [Анализ групп безопасности сети Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Хранилища восстановления|   Microsoft.RecoveryServices/<br>vaults|||[Служба анализа служб восстановления Azure (предварительная версия)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Службы поиска|   Microsoft.Search/<br>searchServices|    Диагностика|    Диагностика||
|Пространство имен служебной шины| Microsoft.ServiceBus/<br>namespaces|    Диагностика|Диагностика|    [Служба анализа служебной шины (предварительная версия)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Хранилище||    [Анализ Service Fabric (предварительная версия)](https://docs.microsoft.com/azure/log-analytics/log-analytics-service-fabric)|
|SQL (версия 12)| Microsoft.Sql/<br>servers/<br>databases||       Диагностика||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|служба хранилища.|||         Скрипт| [Служба анализа службы хранилища Azure (предварительная версия)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Виртуальные машины Azure|    Microsoft.Compute/<br>virtualMachines|  Добавочный номер|  Добавочный номер||
||||Диагностика||
|наборы для масштабирования виртуальных машин|    Microsoft.Compute/<br>virtualMachines    ||Диагностика||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|фермы веб-серверов;|Microsoft.Web/<br>serverfarms||   Диагностика
|веб-сайты;|  Microsoft.Web/<br>sites ||      Диагностика|    [Дополнительные сведения](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Интеграция журналов с локальными системами SIEM
Служба [Интеграция журналов данных Azure](https://www.microsoft.com/download/details.aspx?id=53324) позволяет интегрировать необработанные журналы из ресурсов Azure с использованием локальной системы SIEM.

![Схема интеграции журналов](./media/azure-log-audit/azure-log-audit-fig9.png)

Служба интеграции журналов собирает данные диагностики Azure из виртуальных машин Windows, журналов действий Azure, оповещений центра безопасности Azure и журналов поставщика ресурсов Azure. С помощью такой интеграции вы можете получить доступ ко всем своим ресурсам, локальным или облачным, на единой панели мониторинга, что позволяет выполнять статистическую обработку, сопоставление и анализ, а также оповещать о событиях безопасности.

Сейчас служба интеграции журналов поддерживает интеграцию журналов действий Azure, журналов событий Windows из виртуальных машин Windows в подписке Azure, оповещений центра безопасности Azure, журналов диагностики Azure и журналов аудита Azure AD.

| Тип журнала | Log Analytics с поддержкой JSON (Splunk, ArcSight и IBM QRadar) |
| :------- | :-------------------------------------------------------- |
|Журналы аудита Azure AD|   Yes|
|Журналы действий| Yes|
|Оповещения центра безопасности |Yes|
|Журналы диагностики (журналы ресурсов)|  Yes|
|Журналы виртуальных машин|   Да, но посредством перенаправления событий, а не с помощью JSON.|

[Приступая к работе с Интеграцией журналов данных Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started). Это руководство описывает установку службы Интеграции журналов данных Azure и интеграцию журналов из службы хранилища Azure, журналов действий Azure, оповещений центра безопасности Azure и журналов аудита Azure AD.

Сценарии интеграции для SIEM:

* [Azure Log Integration SIEM configuration steps](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) (Настройка SIEM для службы интеграции журналов Azure). В этой записи блога показано, как настроить службу интеграции журналов Azure для работы с решениями таких партнеров, как Splunk, HP ArcSight и IBM QRadar.

* [Интеграция журналов Azure: часто задаваемые вопросы](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq). В этой статье содержатся ответы на часто задаваемые вопросы об интеграции журналов Azure.

* [Интеграция оповещений центра безопасности Azure с помощью Интеграции журналов данных Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration). Эта статья показывает, как синхронизировать оповещения центра безопасности, а также события безопасности виртуальных машин, собранные журналами диагностики Azure, и журналы аудита Azure с решением Log Analytics или SIEM.

## <a name="next-steps"></a>Дополнительная информация

- [Аудит и ведение журнала](https://docs.microsoft.com/azure/security/security-management-and-monitoring-overview). Защита данных и обеспечение видимости и быстрого реагирования на своевременные оповещения системы безопасности.

- [Ведение журнала безопасности и сбор журналов аудита в Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/). Описание параметров, которые нужно применить, чтобы экземпляры Azure собирали нужные журналы безопасности и аудита.

- [Настройка параметров аудита для семейства веб-сайтов](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US). Являясь администратором семейства веб-сайтов, вы можете получить журнал действий конкретных пользователей, а также журнал действий за определенный диапазон дат. 

- [Поиск по журналу аудита в Центре безопасности и соответствия требованиям Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US). Центр безопасности и соответствия требованиям Office 365 можно использовать для поиска в объединенном журнале аудита, а также для просмотра действий пользователей и администраторов в организации Office 365.


