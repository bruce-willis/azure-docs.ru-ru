---
title: Метод calchistogram в API службы поиска и распознавания данных | Документация Майкрософт
description: Узнайте, как использовать метод calchistogram в API службы поиска и распознавания данных (KES) в Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 6ed694b0cc9cf41b815cc54b9f6d12adb2b7cd64
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380173"
---
# <a name="calchistogram-method"></a>Метод calchistogram
Метод *calchistogram* вычисляет объекты, соответствующие структурированному выражению запроса, и вычисляет распределение значений их атрибутов.

## <a name="request"></a>Запрос
`http://<host>/calchistogram?expr=<expr>[&options]` 

ИМЯ|Значение|ОПИСАНИЕ
----|-----|-----------
expr | Текстовая строка | Структурированное выражение запроса, которое указывает сущности индекса, для которых вычисляются гистограммы.
attributes | Текстовая строка (по умолчанию — "") | Разделенный запятыми список атрибутов для добавления в ответ.
count   | Число (по умолчанию — 10) | Количество результатов для возврата.
offset  | Число (по умолчанию — 0) | Индекс первого результата для возврата.

## <a name="response-json"></a>Ответ (JSON)
JSONPath | ОПИСАНИЕ
----|----
$.expr | Параметр *expr* из запроса.
$.num_entities | Общее количество соответствующих сущностей.
$.histograms |  Массив гистограмм, по одной для каждого запрашиваемого атрибута.
$.histograms[\*].attribute | Имя атрибута, по которому вычислялась гистограмма.
$.histograms[\*].distinct_values | Количество различных значений между соответствующими сущностями для этого атрибута.
$.histograms[\*].total_count | Общее количество экземпляров значений между соответствующими сущностями для этого атрибута.
$.histograms[\*].histogram | Данные гистограммы для этого атрибута.
$.histograms[\*].histogram[\*].value | Значение атрибута.
$.histograms[\*].histogram[\*].logprob  | Общая вероятность на основе натурального логарифма для соответствующих сущностей с этим значением атрибута.
$.histograms[\*].histogram[\*].count    | Количество соответствующих сущностей с этим значением атрибута.
$.aborted | Если истекло время ожидания запроса, то имеет значение true.

### <a name="example"></a>Пример
В приведенном ниже примере для области научных публикаций вычисляется гистограмма числа публикаций по году и ключевому слову для конкретного автора после 2013 года.

`http://<host>/calchistogram?expr=And(Composite(Author.Name=='jaime teevan'),Year>=2013)&attributes=Year,Keyword&count=4`

Ответ указывает, что существует 37 документов, соответствующих выражению запроса.  Для атрибута *Year* существуют 3 различные значения, по одному на каждый год после 2013 года.  Суммарное количество документов по трем отличным значениям равно 37.  Для каждого значения *Year* гистограмма показывает значение, общую вероятность на основе натурального логарифма и количество соответствующих сущностей.     

Гистограмма для *Keyword* показывает наличие 34 различающихся ключевых слов. Поскольку документ может быть связан с несколькими ключевыми словами, общее число (53) может быть больше, чем количество соответствующих сущностей.  Хотя существуют 34 различные значения, ответ включает только четыре наиболее подходящих из них из-за параметра "count=4".

```json
{
  "expr": "And(Composite(Author.Name=='jaime teevan'),Y>=2013)",
  "num_entities": 37,
  "histograms": [
    {
      "attribute": "Y",
      "distinct_values": 3,
      "total_count": 37,
      "histogram": [
        {
          "value": 2014,
          "logprob": -6.894,
          "count": 15
        },
        {
          "value": 2013,
          "logprob": -6.927,
          "count": 12
        },
        {
          "value": 2015,
          "logprob": -7.082,
          "count": 10
        }
      ]
    },
    {
      "attribute": "Keyword",
      "distinct_values": 34,
      "total_count": 53,
      "histogram": [
        {
          "value": "crowdsourcing",
          "logprob": -7.142,
          "count": 9
        },
        {
          "value": "information retrieval",
          "logprob": -7.389,
          "count": 4
        },
        {
          "value": "personalization",
          "logprob": -7.623,
          "count": 3
        },
        {
          "value": "mobile search",
          "logprob": -7.674,
          "count": 2
        }
      ]
    }
  ]
}
``` 
