---
title: Метод CalcHistogram в Academic Knowledge API | Документация Майкрософт
description: Используйте метод CalcHistogram для вычисления распределения значений атрибутов для набора сущностей публикаций в Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: e0b773fb9791ee638c8cfdbbc9dca40543e50ec0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380041"
---
# <a name="calchistogram-method"></a>Метод CalcHistogram

**Calchistogram** REST API используется для вычисления распределения значений атрибутов для набора сущностей публикаций.          


**Конечная точка REST:**
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?
``` 
<br>
  
## <a name="request-parameters"></a>Параметры запроса

ИМЯ  |Значение | Обязательный?  |ОПИСАНИЕ
-----------|----------|--------|----------
**expr**    |Текстовая строка | Yes  |Выражение запроса, которое указывает объекты, для которых вычисляется гистограмма.
**model** |Текстовая строка | Нет  |Имя модели, которую вы хотите запросить.  В настоящее время значение по умолчанию соответствует *latest*.
**attributes** | Текстовая строка | Нет <br>значение по умолчанию: | Список разделительных запятых, который определяет значения атрибутов, которые включены в ответ. Имена атрибутов с учетом регистра.
**count** |Number | Нет <br>Значение по умолчанию: 10. |Количество результатов для возврата.
**offset**  |Number | Нет <br>По умолчанию: 0 |Индекс первого результата для возврата.
<br>
## <a name="response-json"></a>Ответ (JSON)
ИМЯ | ОПИСАНИЕ
--------|---------
**expr**  |Параметр expr из запроса.
**num_entities** | Общее количество совпадающих сущностей.
**histograms** |  Массив гистограмм, по одной для каждого атрибута, указанного в запросе.
**histograms[x].attribute** | Имя атрибута, по которому вычислялась гистограмма.
**histograms[x].distinct_values** | Количество различных значений между соответствующими сущностями для этого атрибута.
**histograms[x].total_count** | Общее количество экземпляров значений между соответствующими сущностями для этого атрибута.
**histograms[x].histogram** | Данные гистограммы для этого атрибута.
**histograms[x].histogram[y].value** |  Значение для атрибута.
**histograms[x].histogram[y].logprob**  |Общая естественная журнальная вероятность сопоставления сущностей с этим значением атрибута.
**histograms[x].histogram[y].count**  |Количество совпадающих сущностей с этим значением атрибута.
**aborted** | Если истекло время ожидания запроса, то значение true.

 <br>
#### <a name="example"></a>Пример:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>В этом примере для создания гистограммы количества публикаций по году для определенного автора с 2010 года можно сначала сгенерировать выражение запроса с помощью **интерпретации**API со строкой запроса: *papers by jaime teevan after 2012*.

```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime teevan after 2012
```
<br>Выражение в первой интерпретации, которое возвращается из API интерпретации, это *And(Composite(AA.AuN=='jaime teevan'),Y>2012)*.
<br>Это выражение затем передается в **calchistogram** API. *Атрибуты=Y,F.FN* параметров указывают, что распределения подсчета документов должны быть по году и области изучения, например:
```
https:// westus.api.cognitive.microsoft.com/academic/v1.0/calchistogram?expr=And(Composite(AA.AuN=='jaime teevan'),Y>2012)&attributes=Y,F.FN&count=4
```
<br>Ответ на этот запрос сначала указывает, что существует 37 документов, которые соответствуют выражению запроса.  Для атрибута *Год* существует 3 различных значения, по одному на каждый год после 2012 года (т.е. 2013, 2014 и 2015), как указано в запросе.  Суммарный счетчик документов за тремя различными значениями равен 37.  Для каждого *Года* гистограмма показывает значение, общую естественную журнальную вероятность и количество совпадающих сущностей.     

Гистограмма для *Области изучения* показывает, что исследовано 34 различные области изучения. Поскольку документ может быть связан с несколькими областями изучения, общее число (53) может быть больше, чем количество согласующих объектов.  Хотя существует 34 различные значения, ответ включает только верхние 4 из-за параметра *count=4*.

```JSON
{
  "expr": "And(Composite(AA.AuN=='jaime teevan'),Y>2012)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -15.753,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -15.805,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -16.035,
          "count": 10
        }
      ]
    },
    {
      "attribute": "F.FN",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -15.258,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -16.002,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -16.226,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -17.228,
          "count": 2
        }
      ]
    }
  ]
}
```
