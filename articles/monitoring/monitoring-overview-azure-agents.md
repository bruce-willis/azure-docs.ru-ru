---
title: Обзор агентов мониторинга Azure | Документация Майкрософт
description: В этой статье представлен подробный обзор доступных агентов Azure, которые поддерживают мониторинг виртуальных машин Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: a399c3968e5ee1e2d1f6d623a68dbb1e15cef212
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088452"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Обзор агентов Azure для мониторинга виртуальных машин Azure
Microsoft Azure предоставляет несколько способов сбора различных типов данных с виртуальных машин, размещенных в Azure или в других облачных провайдерах, которые работают с программным обеспечением Microsoft в Windows и Linux.  Эта статья описывает различия и возможности каждого агента, чтобы иметь возможность определить, какой из них будет поддерживать ваши требования к мониторингу или управлению услугами.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Сравнение системы диагностики Azure и агента Log Analytics
Сегодня в Azure существуют два типа агентов для мониторинга виртуальной машины Azure: расширение системы диагностики Azure и агент Log Analytics для Linux и Windows.  Эти агенты по сути предназначены для сбора метрик и журналов и для их переадресации в репозиторий. Тем не менее они больше ничем не похожи.  

[Расширение системы диагностики Azure](../monitoring-and-diagnostics/azure-diagnostics.md) стало общедоступным для облачных служб Azure в 2010 году. Это агент, который обеспечивает простой сбор данных диагностики ресурса IaaS Azure, например виртуальной машины, и сохраняет его в хранилище Azure.  После сохранения можно просмотреть данные с помощью одного из инструментов, например [Server Explorer в Visual Studio](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md) или [Обозреватель службы хранилища Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Теперь можно выбрать следующие параметры.

* Предопределенный набор счетчиков производительности и журналов событий операционной системы, или можно самому указать, что собирать. 
* Все запросы или неудачные запросы к веб-серверу IIS.
* Приложение .NET для трассировки журналов вывода.
* Трассировку событий для Windows (ETW). 
* Сбор регистрации событий из системного журнала.  
* Аварийные дампы 

Данные можно альтернативно переадресовать к [Application Insights](../application-insights/app-insights-cloudservices.md), [Log Analytics](../log-analytics/log-analytics-overview.md) или, используя [концентратор событий](../event-hubs/event-hubs-what-is-event-hubs.md), к службам, не относящимся к Azure. 

Для расширенного мониторинга, когда необходимо не только собирать метрики и подмножество журналов, нужен агент Log Analytics для Windows и Linux.  С помощью этого агента можно использовать службы Azure, например автоматизацию и Log Analytics, включая их полный набор функций для комплексного управления виртуальными машинами Azure на протяжении их жизненного цикла. А именно:

* [Управление обновлениями службы автоматизации Azure](../automation/automation-update-management.md) для обновлений операционной системы
* [Настройка требуемого состояния службы автоматизации Azure](../automation/automation-dsc-overview.md) для поддержки согласованного состояния конфигурации
* Отслеживание изменений в конфигурации с помощью [Отслеживания изменений в службе автоматизации и инвентаризации Azure](../automation/automation-change-tracking.md)
* Сбор с помощью Log Analytics пользовательских журналов из ОС и размещенных приложений, таких как [FluentD](../log-analytics/log-analytics-data-sources-json.md), [пользовательские журналы](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL и Apache](../log-analytics/log-analytics-data-sources-linux-applications.md)
* Службы Azure, такие как [Application Insights](https://docs.microsoft.com/azure/application-insights/) и [центр безопасности Azure](https://docs.microsoft.com/azure/security-center/), изначально хранят свои данные непосредственно в Log Analytics.  

## <a name="next-steps"></a>Дополнительная информация

- Сведения для просмотра требований и доступных методов развертывания агента на компьютеры в центре обработки данных или в другой облачной среде см. в разделе [Сбор данных с компьютеров в среде с помощью Log Analytics](../log-analytics/log-analytics-concept-hybrid.md).
- Сведения о настройке сбора данных на виртуальных машинах Azure см. в [этой статье](../log-analytics/log-analytics-quick-collect-azurevm.md). 