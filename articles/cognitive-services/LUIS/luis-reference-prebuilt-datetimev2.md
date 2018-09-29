---
title: Справочные материалы по предварительно созданным сущностям datetimeV2 в службе LUIS в Azure | Документация Майкрософт
titleSuffix: Azure
description: В этой статье описана готовая сущность datetimeV2 в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: b7d5b14121f533c49f29759293c0e5379ea2555e
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036700"
---
# <a name="datetimev2-entity"></a>Сущность datetimeV2

Предварительно созданная сущность **datetimeV2** извлекает значения даты и времени. Эти значения имеют стандартный формат, за счет чего их могут использовать клиентские программы. Если во фразе указана неполная дата или время, LUIS возвращает в ответе конечной точки _прошедшие и будущие значения_. Так как эта сущность уже обучена, добавлять примеры фраз, содержащие сущность datetimeV2 в приложение, не нужно. 

## <a name="types-of-datetimev2"></a>Типы сущностей datetimeV2
Управление сущностью datetimeV2 выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-DateTime.yaml).

## <a name="example-json"></a>Пример JSON 
В следующем примере ответа JSON приведена сущность `datetimeV2` с подтипом `datetime`. Подтипы сущностей datetimeV2 см. в [этом разделе](#subtypes-of-datetimev2)</a>.

```JSON
"entities": [
  {
    "entity": "8am on may 2nd 2017",
    "type": "builtin.datetimeV2.datetime",
    "startIndex": 15,
    "endIndex": 30,
    "resolution": {
      "values": [
        {
          "timex": "2017-05-02T08",
          "type": "datetime",
          "value": "2017-05-02 08:00:00"
        }
      ]
    }
  }
]
  ```

## <a name="json-property-descriptions"></a>Описания свойств JSON

|Имя свойства |Тип и описание свойства|
|---|---|
|Сущность|**Строка.** Текст, извлеченный из фразы, с типом даты, времени, диапазоном дат или времени.|
|Тип|**Строка.** Один из [подтипов сущности datetimeV2](#subtypes-of-datetimev2).
|startIndex|**Целое число.** Индекс во фразе, в которой начинается сущность.|
|endIndex|**Целое число.** Индекс во фразе, в которой заканчивается сущность.|
|resolution|Имеет массив `values` с одним, двумя или четырьмя [значениями разрешения](#values-of-resolution).|
|end|Конечное значение времени или диапазона дат в том же формате, что и значение (`value`). Используется, только если свойство `type` имеет значение `daterange`, `timerange` или `datetimerange`|

## <a name="subtypes-of-datetimev2"></a>Подтипы сущности datetimeV2

Предварительно созданная сущность **datetimeV2** имеет следующие подтипы (примеры каждого из них приведены в таблице ниже):
* `date`
* `time`
* `daterange`
* `timerange`
* `datetimerange`
* `duration`
* `set`

## <a name="values-of-resolution"></a>Значения разрешения
* Массив содержит один элемент, если дата или время во фразе полностью указаны и однозначны.
* Массив содержит два элемента, если значение datetimeV2 неоднозначное. Неоднозначность означает отсутствие года, времени и диапазона времени. Примеры см. в разделе [Неоднозначные даты](#ambiguous-dates). Если неоднозначно указано время до полудня или после полудня, включаются оба значения.
* Массив содержит четыре элемента, если фраза содержит два неоднозначных элемента. К неоднозначным относятся элементы, в которых:
  * в дате или диапазоне дат указан неоднозначно год;
  * во времени или диапазоне времени указано неоднозначно время до или или после полудня (например, 3 апреля 3:00).

Ниже приведены поля, которые может иметь каждый элемент массива `values`. 

|Имя свойства|Описание свойства|
|--|--|
|timex|Время, дата или диапазон дат, выраженные в формате TIMEX, который соответствует [стандарту ISO 8601](https://en.wikipedia.org/wiki/ISO_8601), и в атрибутах TIMEX3 заметки с помощью языка TimeML. Эта заметка описана в [рекомендациях TIMEX](http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf).|
|Тип|Один из следующих подтипов: datetime, date, time, daterange, timerange, datetimerange, duration, set.|
|value|**Необязательный параметр.** Объект datetime в формате гггг:ММ:дд (дата), ЧЧ:мм:сс (время) гггг:ММ:дд ЧЧ:мм:сс (дата и время). Если свойство `type` имеет значение `duration`, в качестве значения указывается количество секунд (длительность). <br/> Используется, только если свойство `type` имеет значение `datetime` или `date`, `time` или `duration.|

## <a name="valid-date-values"></a>Допустимые значения даты

Сущность **datetimeV2** поддерживает даты в следующих диапазонах:

| Min | Max |
|----------|-------------|
| 1 января 1900 г.   | 31 декабря 2099 г. |

## <a name="ambiguous-dates"></a>Неоднозначные даты

Если дата может быть в прошлом или будущем, LUIS предоставляет оба значения. В качестве примера можно привести фразу с датой и месяцем без года.  

Например, 2 мая.
* Если сегодня 3 мая 2017 года, LUIS возвращает два значения: 2017-05-02 и 2018-05-02. 
* Если сегодня 1 мая 2017 года, LUIS возвращает два значения: 2016-05-02 и 2017-05-02.

В следующем примере показано разрешение сущности "may 2nd". Это разрешение предполагает, что сегодняшняя дата — это дата между 2 мая 2017 г. и 1 мая 2018 г.
Поля с `X` в поле `timex` — это части даты, которые явно не указаны во фразе.

```JSON
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-numeric-date"></a>Примеры разрешения диапазона числовых дат

Сущность `datetimeV2` извлекает диапазоны дат и времени. Поля `start` и `end` определяют начало и конец диапазона. Если имеется фраза "May 2nd to May 5th", LUIS предоставляет значения **daterange** за текущий и будущий год. В поле `timex` значения `XXXX` указывают неоднозначность года. Значение `P3D` указывает на трехдневный период времени.

```JSON
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

## <a name="date-range-resolution-examples-for-day-of-week"></a>Примеры разрешения диапазона дат для дня недели

В следующем примере показано, как с помощью сущности **datetimeV2** LUIS обрабатывает фразу "Tuesday to Thursday". В этом примере ниже текущей датой является 19 июня. LUIS включает значения **daterange** обоих диапазонов дат перед и после текущей даты.

```JSON
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```
## <a name="ambiguous-time"></a>Неоднозначное время
Массив значений имеет два элемента времени, если время или диапазон времени неоднозначны. Если время указано неоднозначно, возвращается значение до и после полудня .

## <a name="time-range-resolution-example"></a>Пример разрешения диапазона времени

В следующем примере показано, как с помощью сущности **datetimeV2** LUIS обрабатывает фразу с диапазон времени.

```
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```

## <a name="deprecated-prebuilt-datetime"></a>Устаревшая предварительно созданная сущность datetime

Устаревшая предварительно созданная сущность `datetime` заменяется сущностью **datetimeV2**. 

Чтобы заменить сущность `datetime` на `datetimeV2` в приложении LUIS, сделайте следующее:

1. Откройте область **Сущности** веб-интерфейса LUIS. 
2. Удалите предварительно созданную сущность **datetime**.
3. Щелкните **Add prebuilt entity** (Добавить предварительно созданную сущность).
4. Выберите **datetimeV2** и нажмите кнопку **Сохранить**.

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о сущностях [dimension](luis-reference-prebuilt-dimension.md), [email](luis-reference-prebuilt-email.md) и [number](luis-reference-prebuilt-number.md). 

