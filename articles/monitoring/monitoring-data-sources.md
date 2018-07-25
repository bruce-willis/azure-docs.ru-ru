---
title: Источники данных мониторинга в Azure | Документы Майкрософт
description: Описывает данные, доступные для мониторинга работоспособности и производительности ресурсов Azure, а также выполняющихся на их базе приложений.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: bwren
ms.openlocfilehash: 262099bbe45e483efd269445aa8042b30668ebe3
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036529"
---
# <a name="sources-of-monitoring-data-in-azure"></a>Источники данных мониторинга в Azure
Эта статья описывает данные, доступные для мониторинга работоспособности и производительности ресурсов Azure, а также выполняющихся на их базе приложений.  Эти данные можно собирать и анализировать с помощью средств, описанных в разделе [Сбор данных мониторинга в Azure](monitoring-data-collection.md).

Данные мониторинга в Azure поступают из различных источников, которые можно упорядочить по уровням, где верхним является уровень приложений, а нижним — уровень платформы Azure. Эта концепция проиллюстрирована на следующей схеме, а каждый из уровней подробно описан в разделах ниже.

![Уровни данных мониторинга](media/monitoring-data-sources/monitoring-tiers.png)


## <a name="azure-platform"></a>Платформа Azure
Телеметрия, связанная с работоспособностью и работой самой платформы Azure, включает в себя данные об операциях и управлении клиентом или подпиской Azure. Она включает в себя данные о работоспособности служб, хранящиеся в журнале действий Azure и журналах аудита из Azure Active Directory.

![Коллекция Azure](media/monitoring-data-sources/azure-collection.png)

### <a name="azure-service-health"></a>Работоспособность служб Azure
[Работоспособность служб Azure](../monitoring-and-diagnostics/monitoring-service-notifications.md) предоставляет сведения о работоспособности служб Azure в вашей подписке, используемых приложением и ресурсами. Вы можете создать оповещения, чтобы получать уведомления о текущих и ожидаемых критических проблемах, которые могут затронуть ваше приложение. Записи работоспособности служб сохраняются в [журнале действий Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), чтобы вы могли просматривать их в обозревателе журналов действий и копировать в Log Analytics.

### <a name="azure-activity-log"></a>Журнал действий Azure
[Журнал действий Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) включает в себя записи работоспособности служб, а также записи о любых изменениях конфигурации, внесенных для ресурсов Azure. Журнал действий доступен всем ресурсам Azure и является их _внешним_ представлением. Конкретные типы записей в журнале действий описаны в [схеме событий журнала действий Azure](../monitoring-and-diagnostics/monitoring-activity-log-schema.md).

Вы можете просматривать журнал действий для определенного ресурса на его странице на портале Azure или журналы из нескольких ресурсов в [обозревателе журналов действий](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Это особенно удобно для копирования записей журналов в Log Analytics для объединения с другими данными мониторинга. Вы также можете отправлять их в другие расположения с помощью [концентраторов событий](../monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md).


### <a name="azure-active-directory-audit-logs"></a>Журналы аудита Azure Active Directory
[Отчеты Azure Active Directory](../active-directory/active-directory-reporting-azure-portal.md) включают в себя журнал действий входа и журнал аудита изменений, внесенных в пределах конкретного клиента. Сейчас невозможно объединить данные аудита Azure Active Directory с другими данными мониторинга, так как они доступны только через Azure Active Directory и [API отчетов Azure Active Directory](../active-directory/active-directory-reporting-api-getting-started-azure-portal.md).


## <a name="azure-services"></a>Службы Azure
Метрики и журналы диагностики по уровням ресурсов содержат сведения о _внутренних_ операциях с ресурсами Azure. Они доступны для большинства служб Azure, а решения по управлению предоставляют дополнительные сведения о конкретных службах.

![Коллекция ресурсов Azure](media/monitoring-data-sources/azure-resource-collection.png)


### <a name="metrics"></a>Метрики
Большинство служб Azure создают метрики, отражающие их производительность и операции. Конкретные [метрики зависят от типа ресурса](../monitoring-and-diagnostics/monitoring-supported-metrics.md).  Они доступны в обозревателе метрик и могут быть скопированы в Log Analytics для выявления тенденций и другого анализа.


### <a name="resource-diagnostic-logs"></a>Журналы диагностики ресурсов
Хотя журнал действий содержит сведения об операциях с ресурсами Azure, [журналы диагностики](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) по уровням ресурсов предоставляют сведения о работе самого ресурса.   Требования к конфигурации и содержимое этих журналов [зависит от типа ресурса](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

Вы не можете просматривать журналы диагностики непосредственно на портале Azure, но можете [отправлять их в службу хранилища Azure для архивации](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) и экспортировать их в [концентратор событий](../event-hubs/event-hubs-what-is-event-hubs.md) для перенаправления в другие службы либо в [Log Analytics](../monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics.md) для анализа. Некоторые ресурсы могут выполнять запись непосредственно в Log Analytics, а другие записывают данные в учетную запись хранения перед их [импортом в Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).

### <a name="management-solutions"></a>Решения для управления
 [Решения по управлению](../monitoring/monitoring-solutions.md) собирают данные, чтобы получить дополнительные сведения о работе конкретной службы. Они собирают данные в Log Analytics, где их можно проанализировать с помощью [языка запросов](../log-analytics/log-analytics-log-search.md) или представлений, которые обычно включаются в решение.

## <a name="guest-operating-system"></a>Гостевая операционная система
В дополнение к телеметрии, создаваемой всеми службами Azure, вычислительные ресурсы также имеют гостевую операционную систему, которую нужно отслеживать. Установив один или несколько агентов, вы можете собирать данные телеметрии из гостевой ОС и передавать их в те же средства мониторинга, что и сами службы Azure.

![Коллекция вычислительных ресурсов Azure](media/monitoring-data-sources/compute-resource-collection.png)

### <a name="diagnostic-extension"></a>Расширение системы диагностики
С помощью [расширения системы диагностики Azure](../monitoring-and-diagnostics/azure-diagnostics.md) вы можете собирать журналы и данные о производительности из клиентской операционной системы вычислительных ресурсов Azure. Метрики и журналы, собранные из клиентов, хранятся в учетной записи хранения Azure, [из которой можно настроить импорт в Log Analytics](../log-analytics/log-analytics-azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage).  Обозреватель метрик поддерживает чтение из учетной записи хранения и включает клиентские метрики в другие собранные метрики.


### <a name="log-analytics-agent"></a>Агент Log Analytics
Агент Log Analytics можно установить на любом физическом компьютере или любой виртуальной машине под управлением Windows или Linux. Виртуальная машина может работать в Azure, другом облаке или в локальной среде.  Агент подключается к Log Analytics либо напрямую, либо через [подключенную группу управления System Center Operations Manager](../log-analytics/log-analytics-om-agents.md) и позволяет собирать данные из настроенных [источников данных](../log-analytics/log-analytics-data-sources.md) или из [решений по управлению](../monitoring/monitoring-solutions.md), которые предоставляют дополнительные сведения о приложениях, выполняемых на виртуальной машине.

### <a name="service-map"></a>Схема услуги
[Сопоставление служб](../operations-management-suite/operations-management-suite-service-map.md) требует наличия Dependency Agent на виртуальных машинах Windows и Linux. Это решение работает с агентом Log Analytics для сбора данных о процессах, запущенных на виртуальной машине, и зависимостях от внешних процессов. Оно сохраняет эти данные в Log Analytics и имеет консоль, которая визуально отображает данные, собираемые в дополнение к другим данным в Log Analytics.

## <a name="applications"></a>ПРИЛОЖЕНИЯ
Кроме данных телеметрии, которые приложение может записывать в гостевую операционную систему, подробный мониторинг приложений осуществляется с помощью [Application Insights](https://docs.microsoft.com/azure/application-insights/). Application Insights может собирать данные из приложений, работающих на различных платформах. Приложение может выполняться в Azure, другом облаке или локальной среде.

![Сбор данных приложения](media/monitoring-data-sources/application-collection.png)


#### <a name="application-data"></a>Данные приложения
Когда вы включаете службу Application Insights для приложения, установив пакет инструментирования, она собирает метрики и журналы, относящиеся к производительности и операциям приложения. Сюда входят подробные сведения о просмотрах страниц, запросах приложений и исключениях. Служба Application Insights сохраняет собранные данные в метриках Azure и Log Analytics. Она обладает эффективными средствами для анализа этих данных, но вы можете анализировать их вместе с данными из других источников, используя такие средства, как обозреватель метрик и поиск по журналам.

Вы также можете использовать Application Insights для [создания пользовательских метрик](../application-insights/app-insights-api-custom-events-metrics.md).  Это позволяет определить собственную логику для вычисления числового значения и последующего сохранения этого значения с другими метриками, которые можно просмотреть из обозревателя метрик и использовать для [автомасштабирования](../monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md) и оповещений метрик.

#### <a name="dependencies"></a>Зависимости
Для наблюдения за различными логическими операциями приложения нужно [собирать данные телеметрии по нескольким компонентам](../application-insights/app-insights-transaction-diagnostics.md). Application Insights поддерживает [распределенную корреляцию телеметрии](../application-insights/application-insights-correlation.md), определяющую зависимости между компонентами, что позволяет анализировать их совместно.

#### <a name="availability-tests"></a>Тесты доступности
[Тесты доступности](../application-insights/app-insights-monitor-web-app-availability.md) в Application Insights позволяют протестировать доступность и скорость реагирования приложения из разных расположений в общедоступном Интернете. Вы можете выполнить простую проверку связи, чтобы убедиться, что приложение активно, или воспользоваться Visual Studio для создания веб-теста, имитирующего пользовательский сценарий.  Тесты доступности не требуют никакого инструментирования в приложении.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о [типах данных мониторинга и средствах Azure](monitoring-data-collection.md), используемых для их сбора и анализа.
