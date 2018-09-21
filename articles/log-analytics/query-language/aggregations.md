---
title: Агрегирование в запросах Azure Log Analytics | Документация Майкрософт
description: Описываются функции агрегирования в запросах Log Analytics, которые предлагают эффективные способы анализа данных.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f72fb6f654b4699214a22a7f96431c605af52f2d
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603679"
---
# <a name="aggregations-in-log-analytics-queries"></a>Агрегирование в запросах Log Analytics

> [!NOTE]
> Прежде чем приступить к этому уроку, необходимо ознакомиться со статьями [Начало работы с порталом аналитики](get-started-analytics-portal.md) и [Начало работы с запросами](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

В этой статье описываются функции агрегирования в запросах Log Analytics, которые предлагают эффективные способы анализа данных. Все эти функции работают с помощью оператора `summarize`, который выдает таблицу с агрегированными результатами из входной таблицы.

## <a name="counts"></a>Подсчеты

### <a name="count"></a>count
Выполните подсчет количества строк в результирующем наборе после применения фильтров. Следующий пример возвращает общее количество строк в таблице _Perf_ за последние 30 минут. Результат возвращается в столбец под именем *count_*, если присвоено конкретное имя:


```KQL
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```KQL
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Чтобы просмотреть тенденцию по времени, можно использовать визуализацию временной диаграммы:

```KQL
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

В результате этого примера показана линия тенденции количества записей perf с интервалами в 5 минут:

![Тенденция подсчета](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Используйте `dcount` и `dcountif`, чтобы подсчитать уникальные значения в определенном столбце. Следующий запрос вычисляет, сколько отдельных компьютеров отправили пакеты пульса за последний час:

```KQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Чтобы подсчитать только компьютеры Linux, отправлявшие пакеты пульса, используйте `dcountif`:

```KQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Оценка подгрупп
Чтобы выполнить подсчет или другие агрегирования с подгруппами в данных, используйте ключевое слово `by`. Например, чтобы подсчитать количество отдельных компьютеров Linux, которые отправляли пакеты пульса в каждой стране, используйте следующее:

```KQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|США    | 19                  |
|Канада           | 3                   |
|Ирландия          | 0                   |
|Великобритания   | 0                   |
|Нидерланды      | 2                   |


Чтобы проанализировать меньшие подгруппы данных, добавьте имена дополнительных столбцов в раздел `by`. Например, может потребоваться подсчитать количество отдельных компьютеров из каждой страны на OSType:

```KQL
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Процентили и дисперсия
При вычислении числовых значений распространенной практикой является их усреднение с помощью `summarize avg(expression)`. На средние значения влияют крайние значения, характерные только в нескольких случаях. Чтобы устранить эту проблему, можно использовать менее чувствительные функции, например `median` или `variance`.

### <a name="percentile"></a>Процентиль
Чтобы найти медиану, используйте функцию `percentile` со значением, чтобы указать процентиль:

```KQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Вы также можете указать различные процентили, чтобы получить агрегированный результат для каждого из них:

```KQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Таким образом можно определить, что некоторые ЦП имеют похожие медианы, но различные условия: в некоторых показатели стабильны возле медианы, а в других определяются более низкие и высокие значения ЦП. Это означает, что ЦП испытал пиковые нагрузки.

### <a name="variance"></a>Variance
Чтобы напрямую рассчитать дисперсию значения, используйте стандартное отклонение и методы дисперсии:

```KQL
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Хороший способ проанализировать стабильность загрузки ЦП — объединить значение stdev с вычислением медианы:

```KQL
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Ознакомьтесь с другими статьями по использованию языка запросов Log Analytics:

- [Работа со строками](string-operations.md)
- [Работа со значениями даты и времени](datetime-operations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Составление расширенных запросов](advanced-query-writing.md)
- [Соединения](joins.md)
- [Создание графиков](charts.md)
