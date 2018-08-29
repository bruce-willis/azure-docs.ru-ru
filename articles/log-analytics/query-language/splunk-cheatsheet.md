---
title: Сравнение Splunk с Azure Log Analytics | Документация Майкрософт
description: Помощь пользователям, знакомым с Splunk, в изучении языка запросов Log Analytics.
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
ms.openlocfilehash: 5ea9790695b8afe7bd42b98b071869756b301350
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42447291"
---
# <a name="splunk-to-log-analytics"></a>Сравнение Splunk с Log Analytics

Эта статья поможет пользователям, знакомым с Splunk, в изучении языка запросов Log Analytics. Прямые сравнения между этими языками выполнены для понимания двух ключевых различий, а также сходств, где можно использовать имеющиеся знания.

## <a name="structure-and-concepts"></a>Структура и концепции

В следующей таблице сравниваются концепции и структуры данных Splunk и Log Analytics.

 | Концепция  | Splunk | Log Analytics |  Комментарий
 | --- | --- | --- | ---
 | Единица развертывания  | cluster |  cluster |  В Log Analytics можно выполнять произвольные межкластерные запросы. В Splunk — нет. |
 | Кэши данных |  контейнеры  |  Политики кэширования и хранения |  Определяет период и уровень кэширования данных. Этот параметр напрямую влияет на производительность запросов и затраты на развертывание. |
 | Логическое разделение данных  |  index  |  database  |  Обеспечивает логическое разделение данных. В обеих реализациях можно объединять и соединять разделы. |
 | Структурированные метаданные событий | Недоступно | таблица |  Splunk не поддерживает концепцию языка поиска в метаданных событий. Log Analytics поддерживает концепцию таблицы со столбцами. Каждый экземпляр события сопоставляется со строкой. |
 | Запись данных | event | строка |  Отличается только терминология. |
 | Атрибут записи данных | поле |  столбец |  В Log Analytics это часть структуры таблицы. В Splunk каждое событие имеет собственный набор полей. |
 | Типы | тип данных |  тип данных |  Типы данных Log Analytics более явные, так как они задаются в столбцах. Как в Splunk, так и в Log Analytics можно динамически работать с типами данных и находятся примерно одинаковые наборы типов данных, включая поддержку JSON. |
 | Запрос и поиск  | поиск | query |  Концепции в Log Analytics и Splunk, по сути, являются идентичными. |
 | Время приема событий | Системное время | ingestion_time() |  В Splunk каждое событие получает метку времени индексирования события. В Log Analytics можно определить политику под названием ingestion_time, предоставляющую системный столбец, на который можно ссылаться с помощью функции ingestion_time(). |

## <a name="functions"></a>Функции Azure

В следующей таблице перечислены функции в Log Analytics, эквивалентные функциям Splunk.

|Splunk | Log Analytics |Комментарий
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| replace | replace() | (1)<br> Кроме того, обратите внимание, что хоть `replace()` и принимает три параметра в обоих продуктах, параметры отличаются. |
| substr | substring() | (1)<br>Обратите внимание, что Splunk использует индексы, отсчитываемые с единицы, а Log Analytics — индексы, отсчитываемые с нуля. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | соответствует regex |  (2)  |
| regex | соответствует regex | В Splunk `regex` является оператором. В Log Analytics это оператор отношения. |
| searchmatch | == | В Splunk `searchmatch` позволяет выполнять поиск конкретных строк.
| random | rand()<br>rand(n) | Функция Splunk возвращает число от 0 до 2<sup>31</sup>-1. Функция Log Analytics возвращает число от 0,0 до 1,0 или от 0 до n-1 (если указан параметр).
| now | now() | (1)
| relative_time | totimespan() | (1)<br>В Log Analytics эквивалент relative_time(datetimeVal, offsetVal) в Splunk – это datetimeVal + totimespan(offsetVal).<br>Например, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> становится <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) В Splunk функция вызывается с помощью оператора `eval`. В Log Analytics она используется как часть `extend` или `project`.<br>(2) В Splunk функция вызывается с помощью оператора `eval`. В Log Analytics она может использоваться с оператором `where`.


## <a name="operators"></a>Операторы

В следующих разделах предоставлены примеры использования различных операторов в Splunk и Log Analytics.

> [!NOTE]
> В следующем примере поле Splunk _rule_ сопоставляется с таблицей в Azure Log Analytics, а метка времени по умолчанию в Splunk сопоставляется со столбцом Log Analytics _ingestion_time()_.

### <a name="search"></a>поиска
В Splunk можно опустить ключевое слово `search` и указать строку без кавычек. В Azure Log Analytics каждый поисковой запрос должен начинаться с `find`, имя столбца должно быть строкой без кавычек, а значение подстановки — строкой в кавычках. 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Log Analytics | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Фильтр
Запросы Azure Log Analytics начинаются с табличного результирующего набора, где находится фильтр. В Splunk фильтрация является операцией по умолчанию в текущем индексе. В Splunk можно также использовать оператор `where`, но не рекомендуется.

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Log Analytics | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>Получение n событий или строк для проверки 
Azure Log Analytics также поддерживает `take` в качестве псевдонима для `limit`. В Splunk, если результаты упорядочены, `head` возвращает первые n результатов. В Azure Log Analytics для limit не применяется упорядочивание, но возвращаются первые n найденных строк.

| |  | |
|:---|:---|:---|
| Splunk | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Log Analytics | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Получение первых n событий или строк, упорядоченных по полю или столбцу
Для нижних результатов в Splunk используется `tail`. В Azure Log Analytics можно указать направление упорядочения с помощью `asc`.

| |  | |
|:---|:---|:---|
| Splunk | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Log Analytics | **В начало** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Расширение результирующего набора с помощью новых полей или столбцов
В Splunk также есть функция `eval`, которую не следует сопоставлять с оператором `eval`. Оператор `eval` в Splunk и оператор `extend` в Azure Log Analytics поддерживают только скалярные функции и арифметические операторы.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Log Analytics | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>Переименование 
Для переименования и создания поля в Azure Log Analytics используется один и тот же оператор. В Splunk есть два отдельных оператора: `eval` и `rename`.

| |  | |
|:---|:---|:---|
| Splunk | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Log Analytics | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>Форматирование результатов и проекция
В Splunk нет оператора, аналогичного `project-away`. Для фильтрации полей можно использовать пользовательский интерфейс.

| |  | |
|:---|:---|:---|
| Splunk | **таблица** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Log Analytics | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>Агрегирование
Сведения о различных статистических функциях в Log Analytics см. в [этой статье](aggregations.md).

| |  | |
|:---|:---|:---|
| Splunk | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Log Analytics | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Объединение
Объединение в Splunk имеет значительные ограничения. Вложенный запрос имеет ограничение в 10 000 результатов (задается в файле конфигурации развертывания), а число разновидностей соединения также ограничено.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Log Analytics | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>Сортировать
В Splunk для сортировки в возрастающем порядке необходимо использовать оператор `reverse`. Azure Log Analytics также поддерживает определение места размещения значений NULL: в начале или в конце.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Log Analytics | **order by** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>Развертывание нескольких значений
Это аналогичный оператор и в Splunk, и в Log Analytics.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Log Analytics | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>Аспекты результатов, интересующие поля
На портале Log Analytics предоставляется только первый столбец. Все столбцы доступны через API.

| |  | |
|:---|:---|:---|
| Splunk | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Log Analytics | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>Дедупликация
Вместо этого можно использовать `summarize arg_min()`, чтобы изменить порядок, в котором выбираются записи.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Log Analytics | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>Дополнительная информация

- Пройдите урок по [написанию запросов в Log Analytics](get-started-queries.md).