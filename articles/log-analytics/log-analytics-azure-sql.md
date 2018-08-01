---
title: Решение служб анализа SQL Azure в Log Analytics | Документация Майкрософт
description: Как решение "Аналитика SQL Azure" поможет вам управлять базами данных SQL Azure
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/03/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 440e16416b8567178c61c3d6ce2155e0e331521c
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216331"
---
# <a name="monitor-azure-sql-databases-using-azure-sql-analytics-preview"></a>Мониторинг Баз данных SQL Azure с помощью службы "Аналитика SQL Azure" (предварительная версия)

![Символ службы "Аналитика SQL Azure"](./media/log-analytics-azure-sql/azure-sql-symbol.png)

Аналитика SQL Azure — это облачное решение для наблюдения за производительностью Баз данных SQL Azure в нужном масштабе в нескольких эластичных пулах и подписках. Эта служба собирает и отображает важные метрики производительности Базы данных SQL Azure благодаря встроенным средствам аналитики, которые помогают устранять соответствующие неполадки. 

На основе этих метрик можно создавать пользовательские правила мониторинга и оповещения. Решение также поможет вам определить проблемы на каждом уровне стека приложений. Оно использует метрики диагностики Azure с представлениями Log Analytics, чтобы представить данные обо всех базах данных SQL Azure и эластичных пулах в единой рабочей области Log Analytics. Log Analytics помогает собирать, коррелировать и визуализировать структурированные и неструктурированные данные.

Сейчас эта предварительная версия решения поддерживает 150 000 баз данных SQL Azure и 5 000 эластичных пулов SQL на каждую рабочую область.

Практические сведения об использовании решения "Аналитика SQL Azure" и типичные сценарии его применения приведены в видео:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

## <a name="connected-sources"></a>Подключенные источники

Аналитика SQL Azure — это облачное решение для мониторинга с поддержкой потоковой передачи диагностических данных телеметрии для Баз данных SQL Azure и эластичных пулов. Так как эта служба не использует агенты для подключения к Log Analytics, она не поддерживает возможность подключения к ресурсам SCOM, Windows или Linux (сведения о совместимости см. в следующей таблице).

| Подключенный источник | Поддержка | ОПИСАНИЕ |
| --- | --- | --- |
| **[Система диагностики Azure](log-analytics-azure-storage.md)** | **Да** | Данные метрик и журнала Azure отправляются в Log Analytics непосредственно из Azure. |
| [Учетная запись хранения Azure](log-analytics-azure-storage.md) | Нет  | Log Analytics не считывает данные из учетной записи хранения. |
| [Агенты Windows](log-analytics-windows-agent.md) | Нет  | Решение не использует прямые агенты Windows. |
| [Агенты Linux](log-analytics-linux-agents.md) | Нет  | Решение не использует прямые агенты Linux. |
| [Группы управления SCOM](log-analytics-om-agents.md) | Нет  | Решение не использует прямое подключение агента SCOM к Log Analytics. |

## <a name="configuration"></a>Параметр Configuration

Чтобы добавить решение "Аналитика SQL Azure" в рабочую область, сделайте следующее.

1. Добавьте решение "Аналитика SQL Azure" в рабочую область из [Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview).
2. На портале Azure щелкните **+Создать ресурс** и выберите **Аналитика SQL Azure**.  
    ![Мониторинг и управление](./media/log-analytics-azure-sql/monitoring-management.png)
3. Выберите в списке **Аналитика SQL Azure (предварительная версия)**.
4. В области **Службы анализа SQL Azure (предварительная версия)** щелкните **Создать**.  
    ![Создание](./media/log-analytics-azure-sql/portal-create.png)
5. В области **Создание решения** выберите или создайте рабочую область, в которую необходимо добавить решение, а затем нажмите кнопку **Создать**.  
    ![Добавление в рабочую область](./media/log-analytics-azure-sql/add-to-workspace.png)

### <a name="configure-azure-sql-databases-and-elastic-pools-to-stream-diagnostics-telemetry"></a>Настройка Баз данных SQL Azure и эластичных пулов для потоковой передачи диагностических данных телеметрии

Когда вы создадите решение "Аналитика SQL Azure" в рабочей области для наблюдения за производительностью Баз данных SQL Azure и (или) эластичных пулов, **настройте** каждую соответствующую Базу данных SQL Azure и каждый соответствующий ресурс в эластичном пуле для выполнения потоковой передачи диагностических данных телеметрии в решение.

- Включите систему диагностики Azure для баз данных SQL Azure и эластичных пулов и [настройте для них отправку данных в Log Analytics](../sql-database/sql-database-metrics-diag-logging.md).

### <a name="to-configure-multiple-azure-subscriptions"></a>Настройка нескольких подписок Azure

Чтобы обеспечить поддержку нескольких подписок, используйте сценарий PowerShell. Дополнительные сведения см. в статье [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Включение ведения журнала метрик ресурсов Azure с помощью PowerShell). Укажите идентификатор ресурса рабочей области в качестве параметра при выполнении скрипта для отправки диагностических данных из ресурсов в одной подписке Azure в рабочую область в другой подписке Azure.

**Пример**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Использование решения

Вместе с решением в рабочую область добавляется элемент служб анализа SQL Azure. Кроме того, он появляется в общих сведениях. В элементе отображаются сведения о количестве баз данных и эластичных пулов SQL Azure, к которым подключено решение.

![Элемент служб анализа SQL Azure](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Просмотр данных службы "Аналитика SQL Azure"

Щелкните плитку **Azure SQL Analytics** (Аналитика SQL Azure), чтобы открыть панель мониторинга службы "Аналитика SQL Azure". На панели мониторинга отображаются общие сведения обо всех базах данных, мониторинг которых осуществляется через различные перспективы. Для работы различных перспектив необходимо включить соответствующие метрики или журналы в ресурсах SQL для потоковой передачи в рабочую область Log Analytics.

![Обзор службы "Аналитика SQL Azure"](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

При выборе любой плитки открывается подробный отчет о конкретной перспективе. Выбрав перспективу, вы увидите подробный отчет.

![Время ожидания службы "Аналитика SQL Azure"](./media/log-analytics-azure-sql/azure-sql-sol-metrics.png)

Каждая перспектива предоставляет сводки по подписке, серверу, эластичному пулу и уровню базы данных. Кроме того, справа в каждой перспективе отображается отчет о ней. Выбор подписки, сервера, пула или базы данных из списка позволяет продолжить подробное изучение.

| Перспектива | ОПИСАНИЕ |
| --- | --- |
| Resource by type (Ресурсы по типу) | Перспектива, в которой представлено число всех отслеживаемых ресурсов. При подробном изучении вы получаете сводку о метриках DTU и ГБ. |
| Аналитика | Предоставляет подробные сведения о Intelligent Insights в иерархическом виде. Получите дополнительные сведения об Intelligent Insights. |
| Errors | Предоставляет подробные данные об ошибках SQL, возникших в базах данных, в иерархическом виде. |
| Время ожидания | Предоставляет подробные данные о времени ожидания SQL в базах данных в иерархическом виде. |
| Blockings (Блокировки) | Предоставляет подробные данные о блокировках SQL в базах данных в иерархическом виде. |
| Database waits (Время ожидания базы данных) | Предоставляет подробную статистику времени ожидания SQL на уровне базы данных в иерархическом виде. Включает сводку по общему времени ожидания и времени ожидания для каждого типа ожидания. |
| Query duration (Длительность запросов) | Предоставляет подробную статистику о выполнении запросов, такую как продолжительность запроса, загрузку ЦП, число операций ввода-вывода данных, число операций ввода-вывода журнала, в иерархическом виде. |
| Query waits (Время ожидания запроса) | Предоставляет подробную статистику времени ожидании запросов по категории ожидания в иерархическом виде. |

### <a name="intelligent-insights-report"></a>Отчет Intelligent Insights

[Intelligent Insights](../sql-database/sql-database-intelligent-insights.md) для базы данных SQL Azure позволяет узнать, что происходит с производительностью базы данных. Все собранные данные Intelligent Insights можно визуализировать в перспективе Intelligent Insights, а также получить к ним доступ.

![Аналитические сведения службы "Аналитика SQL Azure"](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Отчеты эластичных пулов и базы данных

Для эластичных пулов и баз данных предусмотрены собственные определенные отчеты, в которых можно просмотреть все данные, собранные для ресурса в указанное время.

![База данных службы "Аналитика SQL Azure"](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Эластичный пул службы "Аналитика SQL Azure"](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Отчеты о запросах

С помощью перспективы Query duration (Длительность запроса) и Query waits (Время ожидания запроса) можно сопоставить производительность любого запроса в отчете о запросах. В этом отчете сравнивается производительность запросов в различных базах данных. Кроме того, он упрощает точное определение баз данных с высокой и низкой производительностью запросов.

![Запросы службы "Аналитика SQL Azure"](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Анализ данных и создание оповещений

[Оповещения можно легко создать](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) с помощью данных, поступающих из ресурсов службы "База данных SQL Azure". Вот несколько полезных запросов [поиска по журналам](log-analytics-log-searches.md), которые можно использовать с оповещениями журналов:



*Высокий уровень DTU в базе данных SQL Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```

*Высокий уровень DTU в эластичном пуле базы данных SQL Azure*

```
AzureMetrics 
| where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" 
| summarize AggregatedValue = max(Maximum) by bin(TimeGenerated, 5m)
| render timechart
```



## <a name="next-steps"></a>Дополнительная информация

- Используйте [поиск по журналам](log-analytics-log-searches.md) в Log Analytics для просмотра подробных данных SQL Azure.
- [Создавайте пользовательские панели мониторинга](log-analytics-dashboards.md), отображающие данные SQL Azure.
- [Создавайте оповещения](log-analytics-alerts.md), предупреждающие о возникновении определенных событий SQL Azure.
