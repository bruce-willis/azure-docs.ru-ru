---
title: Расширенные статистические функции в запросах Azure Log Analytics | Документация Майкрософт
description: В этой статье описываются некоторые более сложные параметры статистической обработки, доступные для запросов Log Analytics.
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
ms.openlocfilehash: 4f2d49233a6eb92f567d4265210fcab394aa6461
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190975"
---
# <a name="advanced-aggregations-in-log-analytics-queries"></a>Расширенные статистические функции в запросах Log Analytics

> [!NOTE]
> Прежде чем приступать к работе с этой статьей, ознакомьтесь со статьей, посвященной [статистическим функциям в запросах Log Analytics](./aggregations.md).

В этой статье описываются некоторые более сложные параметры статистической обработки, доступные для запросов Log Analytics.

## <a name="generating-lists-and-sets"></a>Создание списков и наборов
Вы можете использовать `makelist`, чтобы свести данные по порядку значений в отдельном столбце. Например, вы можете изучить наиболее распространенные события на компьютерах по порядку. Вы можете сводить данные по порядку идентификаторов событий на каждом компьютере. 

```OQL
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```
|Компьютер|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` формирует список в порядке, в котором были переданы данные. Чтобы отсортировать события от старых к новым, используйте `asc` в операторе порядка, а не `desc`. 

Также эффективно создавать список только уникальных значений. Такой список называется _набором_, и его можно создать с помощью `makeset`:

```OQL
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```
|Компьютер|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Как и `makelist`, `makeset` также работает с упорядоченными данными и генерирует массивы на основе порядка переданных строк.

## <a name="expanding-lists"></a>Развертывание списков
Обратной операцией `makelist` или `makeset` является инструкция `mvexpand`, которая расширяет список значений для разделения строк. Ее можно развернуть в любом количестве динамических столбцов — в формате JSON и массива. Например, вы можете просканировать таблицу *пульса* для определения решений, отправляющих данные с компьютеров, которые отправили пакет пульса за последний час:

```OQL
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```
| Компьютер | Решения | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... | ... |

Используйте `mvexpand`, чтобы показать каждое значение в отдельной строке вместо списка, разделенного запятыми:

Heartbeat | where TimeGenerated > ago(1h) | project Computer, split(Solutions, ",") | mvexpand Solutions
```
| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... | ... |
```

Затем вы можете использовать `makelist` снова, чтобы сгруппировать элементы. Теперь вы можете просмотреть список компьютеров для каждого решения:

```OQL
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```
|Решения | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Обработка отсутствующих ячеек
`mvexpand` полезно применять, если требуется заполнить значения по умолчанию для отсутствующих ячеек. Предположим, что вам нужно определить время доступности определенного компьютера с помощью анализа пульса. Также вы хотите просмотреть источник пульса, который находится в столбце _категории_. Как правило, мы используем простой оператор суммирования следующим образом:

```OQL
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```
| Категория | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direct Agent | 2017-06-06T17:00:00Z | 15 |
| Direct Agent | 2017-06-06T18:00:00Z | 60 |
| Direct Agent | 2017-06-06T20:00:00Z | 55 |
| Direct Agent | 2017-06-06T21:00:00Z | 57 |
| Direct Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Однако, в этих результатах контейнер, связанный с "2017-06-06T19:00:00Z", отсутствует, так как отсутствуют данные пульса для этого времени. Используйте функцию `make-series`, чтобы присвоить значение по умолчанию пустым контейнерам. Это приведет к созданию строки для каждой категории с двумя дополнительными столбцами массива, одним для значений и одним для сопоставления контейнеров по времени:

```OQL
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Категория | count_ | TimeGenerated |
|---|---|---|
| Direct Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Третий элемент массива *count_*, как и ожидалось, равен 0, и есть соответствующая метка времени "2017-06-06T19:00:00.0000000Z" в массиве _TimeGenerated_. Однако этот формат массива трудно прочитать. Используйте `mvexpand`, чтобы развернуть массивы и создать выходные данные того же формата, что и сгенерированные `summarize`:

```OQL
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```
| Категория | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direct Agent | 2017-06-06T17:00:00Z | 15 |
| Direct Agent | 2017-06-06T18:00:00Z | 60 |
| Direct Agent | 2017-06-06T19:00:00Z | 0 |
| Direct Agent | 2017-06-06T20:00:00Z | 55 |
| Direct Agent | 2017-06-06T21:00:00Z | 57 |
| Direct Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Сужение результатов до набора элементов: `let`, `makeset`, `toscalar`, `in`
Распространенным сценарием является выбор имен некоторых конкретных сущностей на основе набора критериев, а затем фильтрация другого набора данных до этого набора сущностей. Например, вы можете найти компьютеры, у которых отсутствуют обновления, и определить IP-адреса, для которых вызываются эти компьютеры:


```OQL
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
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