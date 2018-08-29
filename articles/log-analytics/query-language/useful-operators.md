---
title: Полезные операторы в запросах Azure Log Analytics | Документация Майкрософт
description: Общие функции для различных сценариев в запросах Log Analytics.
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
ms.date: 08/21/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: ce397b1ba8d77d2916caa2798c0161ba55f51dbb
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42447304"
---
# <a name="useful-operators-in-log-analytics-queries"></a>Полезные операторы в запросах Log Analytics

В приведенной ниже таблице приведены некоторые общие функции, которые можно использовать для разных сценариев в запросах Log Analytics.

## <a name="useful-operators"></a>Полезные операторы

Категория                                |Соответствующая аналитическая функция
----------------------------------------|----------------------------------------
Псевдонимы выделений и столбцов            |`project`, `project-away`, `extend`
Временные таблицы и константы          |`let scalar_alias_name = …;` <br> `let table_alias_name = (){ … &#124; … &#124; … };`
Операторы сравнения и строк         |`startswith`, `!startswith`, `has`, `!has` <br> `contains`, `!contains`, `containscs` <br> `hasprefix`, `!hasprefix`, `hassuffix`, `!hassuffix`, `in`, `!in` <br> `matches regex` <br> `==`, `=~`, `!=`, `!~`
Общие строковые функции                 |`strcat()`, `replace()`, `tolower()`, `toupper()`, `substring()`, `strlen()`
Общие математические функции                   |`sqrt()`, `abs()` <br> `exp()`, `exp2()`, `exp10()`, `log()`, `log2()`, `log10()`, `pow()` <br> `gamma()`, `gammaln()`
Анализ текста                            |`extract()`, `extractjson()`, `parse`, `split()`
Ограничение выходных данных                         |`take`, `limit`, `top`, `sample`
Функции данных                          |`now()`, `ago()` <br> `datetime()`, `datepart()`, `timespan` <br> `startofday()`, `startofweek()`, `startofmonth()`, `startofyear()` <br> `endofday()`, `endofweek()`, `endofmonth()`, `endofyear()` <br> `dayofweek()`, `dayofmonth()`, `dayofyear()` <br> `getmonth()`, `getyear()`, `weekofyear()`, `monthofyear()`
Группирование и статистический анализ                |`summarize by` <br> `max()`, `min()`, `count()`, `dcount()`, `avg()`, `sum()` <br> `stddev()`, `countif()`, `dcountif()`, `argmax()`, `argmin()` <br> `percentiles()`, `percentile_array()`
Соединения и объединения                        |`join kind=leftouter`, `inner`, `rightouter`, `fullouter`, `leftanti` <br> `union`
Порядок, сортировка                             |`sort`, `order` 
Динамический объект (JSON и массив)         |`parsejson()` <br> `makeset()`, `makelist()` <br> `split()`, `arraylength()` <br> `zip()`, `pack()`
Логические операторы                       |`and`, `or`, `iff(condition, value_t, value_f)` <br> `binary_and()`, `binary_or()`, `binary_not()`, `binary_xor()`
Машинное обучение                        |`evaluate autocluster`, `basket`, `diffpatterns`, `extractcolumns`


## <a name="next-steps"></a>Дополнительная информация

- Пройдите урок по [написанию запросов в Log Analytics](get-started-queries.md).