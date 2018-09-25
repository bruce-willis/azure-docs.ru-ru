---
title: Работа со значениями даты и времени в запросах Azure Log Analytics | Документация Майкрософт
description: В этой статье описывается работа с данными даты и времени в запросах Log Analytics.
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
ms.openlocfilehash: 9b0c58fdbfb0d55b3b8998f4edfc1222b9a3d4aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988604"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Работа со значениями даты и времени в запросах Log Analytics

> [!NOTE]
> Прежде чем приступить к этому уроку, необходимо ознакомиться со статьями [Начало работы с порталом аналитики](get-started-analytics-portal.md) и [Начало работы с запросами](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

В этой статье описывается работа с данными даты и времени в запросах Log Analytics.


## <a name="date-time-basics"></a>Основные сведения о дате и времени
Язык запросов Log Analytics содержит два основных типа данных, связанных с датами и временем: дата и время (datetime) и интервал времени (timespan). Все даты задаются в формате UTC. Хотя поддерживаются несколько форматов даты и времени, рекомендуется использовать формат ISO8601. 

Интервалы времени выражаются как десятичное число, за которым следует единица времени:

|сокращение   | единица времени    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | минута       |
|s           | секунда       |
|ms          | миллисекунда  |
|микросекунда | микросекунда  |
|галочка        | наносекунда   |

Значения даты и времени можно создать путем преобразования строки с помощью оператора `todatetime`. Например, чтобы просмотреть пульс виртуальной машины, отправленный в определенный период времени, можно использовать [оператор between](https://docs.loganalytics.io/docs/Language-Reference/Scalar-operators/between-operator), с помощью которого удобнее указывать диапазон времени...

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Другим распространенным сценарием является сравнение даты и времени с настоящим временем. Например, чтобы просмотреть все пакеты пульса за последние две минуты, можно использовать оператор `now` вместе с промежутком времени в две минуты:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Для этой функции также доступно сокращение:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

Кратчайший и самый удобочитаемый метод — использовать оператор `ago`:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Предположим, что вместо времени начала и окончания вы знаете время начала и длительность. Запрос можно переписать следующим образом:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Преобразование единиц измерения времени
Может быть удобно выразить дату и время или интервал времени в единицах времени, отличных от значения по умолчанию. Предположим, вы просматриваете события ошибок за последние 30 минут и нуждаетесь в вычисляемом столбце, который показывает, как давно произошло событие:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Вы увидите столбец _timeAgo_, который содержит значения, такие как 00:09:31.5118992. То есть значения имеют формат "hh:mm:ss.fffffff". Если вы хотите форматировать эти значения в _количество_ минут с времени начала, просто укажите timeAgo/1m:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Агрегирование и группирование по интервалам времени
Другим очень распространенным сценарием является необходимость получения статистики за определенный период времени и для определенного интервала. Для этого можно использовать оператор `bin` как часть предложения итоговых значений.

Чтобы получить количество событий, которые происходили каждые 5 минут в течение последнего получаса, используйте следующий запрос:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

В результате вы получите следующую таблицу:  
|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Другой способ создания контейнеров результатов — использовать функции, такие как `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Мы получим следующие результаты:

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416  |


## <a name="time-zones"></a>Часовые пояса
Так как все значения даты и времени выражены в формате UTC, зачастую полезно преобразовывать их в формат местного часового пояса. Например, используйте это вычисление для преобразования времени UTC в формат PST (тихоокеанское время):

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Связанные функции

| Категория | Функция |
|:---|:---|
| Преобразование типов данных | [todatetime](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/todatetime())  [totimespan](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/totimespan())  |
| Округление значения до размера ячейки | [bin](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/bin()) |
| Получение конкретной даты или времени | [ago](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago()) [now](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/now())   |
| Получение части значения | [datetime_part](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/datetime_part()) [getmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getmonth()) [monthofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/monthofyear()) [getyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getyear()) [dayofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofmonth()) [dayofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofweek()) [dayofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofyear()) [weekofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/weekofyear()) |
| Получение даты относительно к значению  | [endofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofday()) [endofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofweek()) [endofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofmonth()) [endofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofyear()) [startofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofday()) [startofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofweek()) [startofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofmonth()) [startofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofyear()) |

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с другими статьями по использованию языка запросов Log Analytics:

- [Работа со строками](string-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Составление расширенных запросов](advanced-query-writing.md)
- [Соединения](joins.md)
- [Создание графиков](charts.md)