---
title: Метод evaluate в API службы поиска и распознавания данных | Документация Майкрософт
description: Узнайте, как использовать метод evaluate в API службы поиска и распознавания данных (KES) в Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: fc3d73b326b565cfe40d1b82cc419357b28a801a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380200"
---
# <a name="evaluate-method"></a>Метод evaluate
Метод *evaluate* вычисляет и возвращает результат структурированного выражения запроса на основе данных индекса.

Как правило, выражение будет получено из ответа на метод interpret.  Но можно также составить выражения запросов самостоятельно (ознакомьтесь со [структурированным выражением запроса](Expressions.md)).  

## <a name="request"></a>Запрос 
`http://<host>/evaluate?expr=<expr>&attributes=<attrs>[&<options>]`   

ИМЯ|Значение|ОПИСАНИЕ
----|----|----
expr       | Текстовая строка | Структурированное выражение запроса, которое выбирает подмножество сущностей индекса.
attributes | Текстовая строка | Разделенный запятыми список атрибутов для добавления в ответ.
count      | Число (по умолчанию — 10) | Максимальное количество отображаемых результатов.
offset     | Число (по умолчанию — 0) | Индекс первого результата для возврата.
orderby |   Текстовая строка | Имя атрибута, используемого для сортировки результатов, за которым следуют необязательный параметр порядка сортировки (по умолчанию — asc): "*attrname*[: (asc&#124;desc)]".  Если он не указан, результаты возвращаются в порядке уменьшения вероятности на основе натурального логарифма.
timeout  | Число (по умолчанию — 1000) | Время ожидания в миллисекундах. Возвращаются только результаты, вычисленные до истечения времени ожидания.

С помощью параметров *count* и *offset* можно постепенно получать все больше результатов, используя несколько запросов.
  
## <a name="response-json"></a>Ответ (JSON)
JSONPath|ОПИСАНИЕ
----|----
$.expr | Параметр *expr* из запроса.
$.entities | Массив из 0 или более сущностей, соответствующих структурированному выражению запроса. 
$.aborted | Если истекло время ожидания запроса, то имеет значение true.

Каждая сущность содержит значение *logprob* и значения запрошенных атрибутов.

## <a name="example"></a>Пример
В примере научных публикаций, приведенном ниже, запрос передает структурированное выражение запроса (потенциально из выходных данных запроса *interpret*) и получает несколько атрибутов для двух наиболее подходящих сущностей.

`http://<host>/evaluate?expr=Composite(Author.Name=='jaime teevan')&attributes=Title,Y,Author.Name,Author.Id&count=2`

Ответ содержит две ("count = 2") наиболее подходящие сущности.  Для каждой сущности возвращаются атрибуты заголовка, года, имени автора и идентификатора автора.  Обратите внимание на то, что структура значений составных атрибутов соответствует тому, как они указаны в файле данных. 

```json
{
  "expr": "Composite(Author.Name=='jaime teevan')",
  "entities": 
  [
    {
      "logprob": -6.645,
      "Ti": "personalizing search via automated analysis of interests and activities",
      "Y": 2005,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1968481722
        },
        {
          "Name": "susan t dumais",
          "Id": 676500258
        },
        {
          "Name": "eric horvitz",
          "Id": 1470530979
        }
      ]
    },
    {
      "logprob": -6.764,
      "Ti": "the perfect search engine is not enough a study of orienteering behavior in directed search",
      "Y": 2004,
      "Author": [
        {
          "Name": "jaime teevan",
          "Id": 1982462162
        },
        {
          "Name": "christine alvarado",
          "Id": 2163512453
        },
        {
          "Name": "mark s ackerman",
          "Id": 2055132526
        },
        {
          "Name": "david r karger",
          "Id": 2012534293
        }
      ]
    }
  ]
}
```
