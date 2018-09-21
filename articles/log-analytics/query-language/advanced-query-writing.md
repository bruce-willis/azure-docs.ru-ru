---
title: Расширенные запросы в Azure Log Analytics | Документация Майкрософт
description: В этой статье содержатся сведения об использовании портала аналитики для записи запросов в Log Analytics.
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
ms.openlocfilehash: f7594b7d1eb7d41508be435cdd0a6203433727c1
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603062"
---
# <a name="writing-advanced-queries-in-log-analytics"></a>Составление расширенных запросов в Log Analytics

> [!NOTE]
> Прежде чем приступить к этому уроку, необходимо ознакомиться со статьями [Начало работы с порталом аналитики](get-started-analytics-portal.md) и [Начало работы с запросами](get-started-queries.md).

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>Повторное использование кода с помощью оператора let
С помощью оператора `let` можно присвоить результаты переменной и ссылаться на нее позже в запросе:

```KQL
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

Переменным также можно назначить постоянные значения. При этом можно настроить параметры для полей, которые необходимо менять каждый раз, когда вы выполняете запрос. Измените параметры нужным образом. Например, чтобы вычислить свободное дисковое пространство и объем доступной памяти (в процентилях) в заданный период времени:

```KQL
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

Таким образом можно легко изменить время начала и окончания во время очередного выполнения запроса.

### <a name="local-functions-and-parameters"></a>Локальные функции и параметры
С помощью оператора `let` можно создать функции, которые могут использоваться в одном запросе. Например, определите функцию, которая принимает поле datetime (в формате UTC) и преобразует его в стандартный формат США. 

```KQL
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="functions"></a>Функции Azure
Вы можете сохранить запрос с псевдонимом функции, чтобы на него могли ссылаться другие запросы. Например, следующий стандартный запрос возвращает все отсутствующие обновления безопасности, о которых сообщалось в последний день:

```KQL
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Вы можете сохранить этот запрос как функцию и присвоить ему псевдоним, например _security_updates_last_day_. Затем вы можете использовать его в другом запросе для поиска необходимых обновлений безопасности SQL:

```KQL
security_updates_last_day | where Title contains "SQL"
```

Чтобы сохранить запрос как функцию, нажмите кнопку **Сохранить** на портале и измените **Сохранить как** на _Функция_. Псевдоним функции может содержать буквы, цифры или символы подчеркивания, но должен начинаться с буквы или символа подчеркивания.

> [!NOTE]
> Функции можно сохранять в запросах Log Analytics, но это пока недоступно для запросов Application Insights.


## <a name="print"></a>Оператор print
`print` возвращает таблицу с одним столбцом и одной строкой, содержащей результаты вычисления. Это часто используется в тех случаях, когда нужно простое вычисление. Например, чтобы найти текущее время в формате PST и добавить столбец с временем в формате EST.

```KQL
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Оператор datatable
`datatable` позволяет определить набор данных. Укажите схему и набор значений, а затем передайте таблицу в любые другие элементы запроса. Например, чтобы создать таблицу использования ОЗУ и вычислить среднее значение в час:

```KQL
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Конструкции datatable также очень полезны при создании таблицы подстановки. Например, чтобы сопоставить данные таблицы, такие как идентификаторы событий из таблицы _SecurityEvent_, с типами событий, перечисленными в другом месте, создайте таблицу подстановки с типами событий, используя оператор `datatable`, и соедините эту таблицу с данными _SecurityEvent_:

```KQL
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
```

## <a name="next-steps"></a>Дополнительная информация
Ознакомьтесь с другими статьями по использованию языка запросов Log Analytics:

- [Работа со строками](string-operations.md)
- [Работа со значениями даты и времени](datetime-operations.md)
- [Статистические функции в запросах Log Analytics](aggregations.md)
- [Расширенные статистические функции в запросах Azure Log Analytics](advanced-aggregations.md)
- [Работа с JSON и структурами данных в запросах Log Analytics](json-data-structures.md)
- [Соединения](joins.md)
- [Создание графиков](charts.md)