---
title: Анализ данных Log Analytics в Azure Monitor | Документация Майкрософт
description: Чтобы получить данные из Log Analytics, требуется поиск по журналам.  В этой статье описывается использование новых поисков по журналам в Log Analytics и приведены основные сведения, которые необходимо знать перед их созданием.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: d7c006ca0be5e8db4b7ab02974ff029d3fe738e3
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48042348"
---
# <a name="analyze-log-analytics-data-in-azure-monitor"></a>Анализ данных Log Analytics в Azure Monitor

Данные журнала, собранные Azure Monitor, хранятся в службе Log Analytics, которая собирает данные телеметрии и другие данные из разных источников и предоставляет язык запросов для расширенной аналитики.

> [!NOTE]
> Log Analytics ранее рассматривалась как собственная служба в Azure. Она считается частью Azure Monitor и сфокусирована на хранении и анализе данных журнала с помощью языка запросов. Компоненты, которые были частью Log Analytics, такие как агенты Windows и Linux для сбора данных, просмотры для визуализации существующих данных и оповещения для предварительного уведомления о проблемах, не изменились, но теперь они считаются частью Azure Monitor.



## <a name="log-queries"></a>Запросы журнала

Чтобы получить данные из Log Analytics, требуется запрос к журналам.  Чтобы указать нужные данные, вы будете использовать запрос независимо от того, что вы делаете: [анализируете данные на портале](log-analytics-log-search-portals.md), [настраиваете правило генерации оповещений](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md) для уведомления о конкретном состоянии или получаете данные с помощью [API Log Analytics](https://dev.loganalytics.io/).  В этой статье описываются запросы к журналам в Log Analytics и предоставляются основные сведения, которые необходимо знать перед их созданием.



## <a name="where-log-queries-are-used"></a>Использование запросов к журналу

Ниже приведены различные способы использования запросов к журналам в Log Analytics.

- **Порталы.** На [портале Azure](log-analytics-log-search-portals.md) можно выполнять интерактивный анализ данных журнала.  Так вы можете изменить запрос и анализировать результаты в различных форматах и визуализациях.  
- **Правила генерации оповещений.** [Правила генерации оповещений](log-analytics-alerts.md) заранее выявляют проблемы с данными в рабочей области.  Каждое правило генерации оповещений основано на поиске по журналам, который автоматически выполняется через определенные интервалы.  Результаты проверяются, чтобы определить, следует ли создавать оповещение.
- **Панели мониторинга.** Результаты любого запроса можно закрепить на [панели мониторинга Azure](), что даст вам возможность визуализировать данные журналов и метрик вместе и при необходимости использовать совместно с другими пользователями Azure. 
- **Представления.**  Вы можете создавать визуализации данных, которые добавлены на панели мониторинга пользователя, с помощью [конструктора представлений](log-analytics-view-designer.md).  Запросы к журналам предоставляют данные, используемые [плитками](log-analytics-view-designer-tiles.md) и [элементами визуализации](log-analytics-view-designer-parts.md) в каждом просмотре.  
- **Экспорт.**  Когда вы экспортируете данные из рабочей области Log Analytics в Excel или [Power BI](log-analytics-powerbi.md), вы создаете запрос к журналам для определения экспортируемых данных.
- **PowerShell.** Вы можете запустить скрипт PowerShell из командной строки или модуль Runbook службы автоматизации Azure, использующий командлет [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightssearchresults?view=azurermps-4.0.0), для получения данных из Log Analytics.  Для этого командлета требуется запрос, чтобы определить извлекаемые данные.
- **API Log Analytics.**  [API поиска по журналам службы Log Analytics](log-analytics-log-search-api.md) позволяет любому клиенту REST API извлекать данные из рабочей области.  Запрос API включает запрос, который выполняется в Log Analytics, чтобы определить извлекаемые данные.

![Поиск по журналам](media/log-analytics-queries/queries-overview.png)

## <a name="write-a-query"></a>Напишите запрос
Служба Log Analytics поддерживает [обширный язык запросов](query-language/get-started-queries.md), который позволяет получать и анализировать данные журналов различными способами.  Обычно начинают с основных запросов, а затем переходят к использованию более усовершенствованных функций, так как требования становятся более сложными.

Базовая структура запроса — это исходная таблица и ряд операторов, разделенных символом вертикальной черты `|`.  Вы можете объединить в цепочку несколько операторов для уточнения данных и выполнения расширенных функций.

Например, предположим, необходимо найти первые десять компьютеров с наибольшим количеством событий ошибок за последний день.

    Event
    | where (EventLevelName == "Error")
    | where (TimeGenerated > ago(1days))
    | summarize ErrorCount = count() by Computer
    | top 10 by ErrorCount desc

Или нужно найти компьютеры, которые не были активными в течение последнего дня.

    Heartbeat
    | where TimeGenerated > ago(7d)
    | summarize max(TimeGenerated) by Computer
    | where max_TimeGenerated < ago(1d)  

Как насчет графика, отражающего использование процессора на каждом компьютере за прошлую неделю?

    Perf
    | where ObjectName == "Processor" and CounterName == "% Processor Time"
    | where TimeGenerated  between (startofweek(ago(7d)) .. endofweek(ago(7d)) )
    | summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min)
    | render timechart    

Из этих примеров можно увидеть, что независимо от типа данных, с которыми вы работаете, структура запроса является аналогичной.  Ее можно разбить на отдельные действия, где полученные данные из одной команды отправляются через конвейер в следующую команду.

Кроме того, вы можете запрашивать данные из рабочих областей Log Analytics в рамках своей подписки.

    union Update, workspace("contoso-workspace").Update
    | where TimeGenerated >= ago(1h)
    | summarize dcount(Computer) by Classification 

## <a name="how-log-analytics-data-is-organized"></a>Упорядочивание данных в Log Analytics
При создании запроса сначала нужно определить, какие таблицы содержат нужные данные. Различные типы данных разделяются на выделенные таблицы в каждой [рабочей области Log Analytics](log-analytics-quick-create-workspace.md).  Документация для каждого источника данных включает имя создаваемого типа данных и описание каждого из его свойств.  Многие запросы потребуют только данные из одной таблицы, но другие могут использовать множество параметров для включения данных из нескольких таблиц.

Хотя служба [Application Insights](../application-insights/app-insights-overview.md) хранит данные приложения, например запросы, исключения, трассировки и данные использования в Log Analytics, эти данные хранятся в отдельном разделе, в отличие от других данных журнала. Используется тот же язык запросов для доступа к этим данным, но для этого должны быть [консоль Application Insights](../application-insights/app-insights-analytics.md) или [REST API Application Insights](https://dev.applicationinsights.io/). Вы можете использовать [кросс-ресурсные запросы](log-analytics-cross-workspace-search.md), чтобы объединить данные Application Insights с другими данными в Log Analytics.


![Таблицы](media/log-analytics-queries/queries-tables.png)







## <a name="next-steps"></a>Дополнительная информация

- Узнайте о [порталах, которые можно использовать для создания и изменения поисков по журналам](log-analytics-log-search-portals.md).
- Изучите [руководство по написанию запросов](log-analytics-tutorial-viewdata.md) на новом языке.
