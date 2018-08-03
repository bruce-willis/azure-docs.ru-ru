---
title: Общие принципы извлечения данных в LUIS в Azure | Документы Майкрософт
description: Узнайте, какие виды данные можно извлекать из службы Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: f57e7cb85e6d183a59b358e347d70d4d185868a7
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225688"
---
# <a name="data-extraction"></a>Извлечение данных
Служба LUIS дает возможность получать информацию из высказываний на естественном языке пользователя. Информация извлекается таким образом, что может использоваться программой, приложением или чат-ботом для выполнения определенных действий.

В следующих разделах содержатся сведения о данных, возвращаемых из намерений и сущностей, с примерами JSON. Сложнее всего извлекаются данные, прошедшие машинное обучение, так как они не являются точным текстовым совпадением. Извлечение данных из прошедших машинное обучение [сущностей](luis-concept-entity-types.md) должно осуществляться в ходе [цикла разработки](luis-concept-app-iteration.md) до тех пор, пока вы не будете уверены, что получаете ожидаемые данные. 

## <a name="data-location-and-key-usage"></a>Расположение данных и использование ключа
LUIS предоставляет данные из опубликованной [конечной точки](luis-glossary.md#endpoint). **Запрос HTTPS** (POST или GET) содержит высказывание, а также некоторые дополнительные конфигурации, например промежуточные или рабочие среды. 

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

`appID` доступен на странице **Параметры** приложения LUIS, а также в составе URL-адреса (после `/apps/`) при редактировании этого приложения LUIS. `subscription-key` — это ключ конечной точки, используемый для запросов приложения. При использовании бесплатного ключа разработки или начального ключа во время обучения LUIS необходимо изменить ключ конечной точки на ключ, который поддерживает [ожидаемое использование LUIS](luis-boundaries.md#key-limits). Единица `timezoneOffset` в минутах.

**Ответ HTTPS** содержит все сведения о намерении и сущности, которые служба LUIS может определять на основе текущей опубликованной модели промежуточной или рабочей конечной точки. URL-адрес конечной точки находится на веб-сайте [LUIS](luis-reference-regions.md) на странице **Публикация**. 

## <a name="data-from-intents"></a>Данные из намерений
Основными данными является **имя намерения** с самой высокой оценкой. Согласно `MyStore` [краткому руководству](luis-quickstart-intents-only.md) конечная точка выдает следующий ответ:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

|Объект данных|Тип данных|Расположение данных|Значение|
|--|--|--|--|
|Намерение|Строка|topScoringIntent.intent|"GetStoreInfo"|

Если чат-бот или вызывающее приложение LUIS принимает решение на основе нескольких оценок намерения, возвращаются все оценки намерения, задав параметр строки запроса `verbose=true`. Ответ конечной точки:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

Намерения упорядочены от имеющего самую высокую оценку до имеющего самую низкую оценку.

|Объект данных|Тип данных|Расположение данных|Значение|Оценка|
|--|--|--|--|:--|
|Намерение|Строка|intents[0].intent|"GetStoreInfo"|0,984749258|
|Намерение|Строка|intents[1].intent|"None"|0,0168218873|

При добавлении готовых предметных областей имя намерения указывает предметную область, такую как `Utilties` или `Communication`, а также намерение:

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```
    
|Домен|Объект данных|Тип данных|Расположение данных|Значение|
|--|--|--|--|--|
|Техническое оборудование|Намерение|Строка|intents[0].intent|"<b>Utilities</b>.ShowNext"|
|Обмен данными|Намерение|Строка|intents[1].intent|<b>Communication</b>.StartOver"|
||Намерение|Строка|intents[2].intent|"None"|


## <a name="data-from-entities"></a>Данные из сущностей
Большинству чат-ботов и приложений требуется не только имя намерения. Эти дополнительные данные поступают из сущностей, обнаруживаемых в высказывании. Каждый тип сущности возвращает различные сведения о совпадении. 

Одно слово или фраза в высказывании может соответствовать нескольким сущностям. В этом случае каждая совпадающая сущность возвращается с оценкой. 

Все сущности возвращаются в массиве **сущностей** ответа от конечной точки:

```JSON
"entities": [
  {
    "entity": "bob jones",
    "type": "Name",
    "startIndex": 0,
    "endIndex": 8,
    "score": 0.473899543
  },
  {
    "entity": "3",
    "type": "builtin.number",
    "startIndex": 16,
    "endIndex": 16,
    "resolution": {
      "value": "3"
    }
  }
]
```

## <a name="tokenized-entity-returned"></a>Возвращенная размеченная сущность
Несколько [языков](luis-supported-languages.md#tokenization) возвращают объект сущности с [размеченным](luis-glossary.md#token) значением `entity`. startIndex и endIndex, возвращаемые службой LUIS в объекте сущности, не сопоставляются с новым размеченным значением. Они сопоставляются с исходным запросом, чтобы вы могли программно извлекать необработанные сущности. 

Например, на немецком языке слово `das Bauernbrot` размечается в `das bauern brot`. Возвращается размеченное значение `das bauern brot`, а исходное значение можно определить программно из startIndex и endIndex исходного запроса, в итоге получив `das Bauernbrot`.

## <a name="simple-entity-data"></a>Данные простой сущности

[Простая сущность](luis-concept-entity-types.md) — это значение, прошедшее машинное обучение. Это может быть слово или фраза. 

`Bob Jones wants 3 meatball pho`

В предыдущем высказывании `Bob Jones` помечена как простая сущность `Customer`.

Данные, возвращаемые из конечной точки, содержат имя сущности, обнаруженный текст из высказывания, расположение обнаруженного текста, а также оценку:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Объект данных|Имя сущности|Значение|
|--|--|--|
|Простая сущность|"Customer"|"bob jones"|

## <a name="hierarchical-entity-data"></a>Данные иерархической сущности

[Иерархические](luis-concept-entity-types.md) сущности прошли машинное обучение и могут содержать слово или фразу. Дочерние элементы определяются по контексту. Если вам требуется связь "родители-потомки" с точным текстовым совпадением, используйте сущность [списка](#list-entity-data). 

`book 2 tickets to paris`

В предыдущем высказывании сущность `paris` помечена как дочерняя сущность `Location::ToLocation` иерархической сущности `Location`. 

Данные, возвращаемые из конечной точки, содержат имя сущности и имя дочерней сущности, обнаруженный текст из высказывания, расположение обнаруженного текста, а также оценку: 

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Location::ToLocation",
    "startIndex": 18,
    "endIndex": 22,
    "score": 0.6866132
  }
]
```

|Объект данных|Родительский элемент|Дочерний|Значение|
|--|--|--|--|--|
|Иерархическая сущность|Расположение|ToLocation|"paris"|

## <a name="composite-entity-data"></a>Данные составной сущности
[Составные](luis-concept-entity-types.md) сущности прошли машинное обучение и могут содержать слово или фразу. Рассмотрим составную сущность предварительно созданных `number` и `Location::ToLocation` со следующим высказыванием:

`book 2 tickets to paris`

Обратите внимание, что между `2` (числом) и `paris` ToLocation находятся слова, которые не входят в какие-либо сущности. Зеленая линия подчеркивания, используемая в помеченном высказывании на веб-сайте [LUIS](luis-reference-regions.md), указывает на составную сущность.

![Составная сущность](./media/luis-concept-data-extraction/composite-entity.png)

Составные сущности возвращаются в массиве `compositeEntities`, а все сущности в рамках составной возвращаются в массиве `entities`:

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 18,
      "endIndex": 22,
      "score": 0.956998169
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 5,
      "endIndex": 5,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2 tickets to paris",
      "type": "Order",
      "startIndex": 5,
      "endIndex": 22,
      "score": 0.7714499
    }
  ],
  "compositeEntities": [
    {
      "parentType": "Order",
      "value": "2 tickets to paris",
      "children": [
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Location::ToLocation",
          "value": "paris"
        }
      ]
    }
  ]
```    

|Объект данных|Имя сущности|Значение|
|--|--|--|
|Предварительно созданная сущность — число|"builtin.number"|"2"|
|Иерархическая сущность — расположение|"Location::ToLocation"|"paris"|

## <a name="list-entity-data"></a>Данные сущности списка

Сущность [списка](luis-concept-entity-types.md) не прошла машинное обучение. Она является точным текстовым совпадением. Список представляет элементы в списке, а также синонимы для этих элементов. LUIS помечает все совпадения с элементом в списке как сущность в ответе. Синоним может находиться в нескольких списках. 

Предположим, что в приложении есть список `Cities`, допускающий разные варианты названия городов, включая город аэропорта (Sea-tac), код аэропорта (SEA), почтовый индекс (98101) и телефонный код города (206). 

|Элемент списка|Синонимы элемента|
|---|---|
|Сиэтл;|sea-tac, sea, 98101, 206, +1 |
|Париж|cdg, roissy, ory, 75001, 1, +33|

`book 2 tickets to paris`

В предыдущем высказывании слово `paris` сопоставляется с элементом paris как часть сущности списка `Cities`. Сущность списка совпадает с нормализованным именем элемента, а также с синонимами элемента. 

```JSON
"entities": [
  {
    "entity": "paris",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 22,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

В другом примере высказывания используется синоним для Paris:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

## <a name="prebuilt-entity-data"></a>Данные предварительно созданной сущности
Обнаружение [предварительно созданных](luis-concept-entity-types.md) сущностей выполняется на основе совпадения регулярного выражения с помощью с проекта [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) с открытым исходным кодом. Предварительно созданные сущности возвращаются в массиве сущностей и используют имя типа с префиксом `builtin::`. Ниже приводится пример высказывания с возвращаемыми предварительно созданными сущностями.

`Dec 5th send to +1 360-555-1212`

```JSON
"entities": [
    {
      "entity": "dec 5th",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2017-12-05"
          },
          {
            "timex": "XXXX-12-05",
            "type": "date",
            "value": "2018-12-05"
          }
        ]
      }
    },
    {
      "entity": "1",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 18,
      "resolution": {
        "value": "1"
      }
    },
    {
      "entity": "360",
      "type": "builtin.number",
      "startIndex": 20,
      "endIndex": 22,
      "resolution": {
        "value": "360"
      }
    },
    {
      "entity": "555",
      "type": "builtin.number",
      "startIndex": 26,
      "endIndex": 28,
      "resolution": {
        "value": "555"
      }
    },
    {
      "entity": "1212",
      "type": "builtin.number",
      "startIndex": 32,
      "endIndex": 35,
      "resolution": {
        "value": "1212"
      }
    },
    {
      "entity": "5th",
      "type": "builtin.ordinal",
      "startIndex": 4,
      "endIndex": 6,
      "resolution": {
        "value": "5"
      }
    },
    {
      "entity": "1 360 - 555 - 1212",
      "type": "builtin.phonenumber",
      "startIndex": 18,
      "endIndex": 35,
      "resolution": {
        "value": "1 360 - 555 - 1212"
      }
    }
  ]
``` 

## <a name="regular-expression-entity-data"></a>Данные сущности регулярного выражения
Обнаружение сущностей [регулярного выражения](luis-concept-entity-types.md) выполняется на основе совпадения регулярного выражения с помощью выражения, указываемого при создании сущности. При использовании `kb[0-9]{6}` в качестве определения сущности регулярного выражения следующий ответ JSON является примером высказывания с возвращаемыми сущностями регулярного выражения для запроса `When was kb123456 published?`:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="extracting-names"></a>Извлечение имен
Получение имен из высказывания является сложной задачей, имя может быть практически любым сочетанием букв и слов. В зависимости от того, какой тип имени извлекается, вы можете использовать один из подходящих вариантов. Это не правила, а, скорее, рекомендации. 

### <a name="names-of-people"></a>Имена людей
В зависимости от языка и региональных параметров формат имен людей может незначительно отличаться. Используйте иерархическую сущность с именами и фамилиями в качестве дочерних сущностей или используйте простую сущность с ролями имен и фамилий. Обязательно предоставьте примеры, использующие имя и фамилию в различных частях высказывания, и высказывания для всей намерений, включая намерение None. Регулярно [просматривайте](luis-how-to-review-endoint-utt.md) высказывания конечной точки, чтобы помечать имена, которые были неправильно спрогнозированы. 

### <a name="names-of-places"></a>Имена мест
Имена расположений имен задаются и известны как города, округа, штаты, области и страны. Если приложению известен набор расположений, рекомендуется использовать сущность списка. Если требуется найти все имена мест, создайте простую сущность и представьте различные примеры. Добавьте список фраз с именами мест, чтобы закрепить момент, определяющий отображение имен мест в приложении. Регулярно [просматривайте](luis-how-to-review-endoint-utt.md) высказывания конечной точки, чтобы помечать имена, которые были неправильно спрогнозированы. 

### <a name="new-and-emerging-names"></a>Новые имена
Некоторые приложения должны иметь возможность поиска новых имен, например имен продуктов и компаний. Это наиболее сложный тип извлечения данных. Начните с простой сущности и добавьте список фраз. Регулярно [просматривайте](luis-how-to-review-endoint-utt.md) высказывания конечной точки, чтобы помечать имена, которые были неправильно спрогнозированы. 

## <a name="pattern-roles-data"></a>Данные ролей шаблонов
Роли — это контекстно-зависимые различия сущностей. 

```JSON
{
  "query": "move bob jones from seattle to redmond",
  "topScoringIntent": {
    "intent": "MoveAssetsOrPeople",
    "score": 0.9999998
  },
  "intents": [
    {
      "intent": "MoveAssetsOrPeople",
      "score": 0.9999998
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 6.12244548E-07
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 6.12244548E-07
    },
    {
      "intent": "FindForm",
      "score": 1.1E-09
    }
  ],
  "entities": [
    {
      "entity": "bob jones",
      "type": "Employee",
      "startIndex": 5,
      "endIndex": 13,
      "score": 0.922820568,
      "role": ""
    },
    {
      "entity": "seattle",
      "type": "Location",
      "startIndex": 20,
      "endIndex": 26,
      "score": 0.948008537,
      "role": "Origin"
    },
    {
      "entity": "redmond",
      "type": "Location",
      "startIndex": 31,
      "endIndex": 37,
      "score": 0.7047979,
      "role": "Destination"
    }
  ]
}
```

## <a name="patternany-entity-data"></a>Данные сущности Pattern.any
Сущности Pattern.Any являются сущностями переменной длины, используемыми в выражениях [шаблона](luis-concept-patterns.md). 

```JSON
{
  "query": "where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.999999464
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.999999464
    },
    {
      "intent": "GetEmployeeBenefits",
      "score": 4.883697E-06
    },
    {
      "intent": "None",
      "score": 1.02040713E-06
    },
    {
      "intent": "GetEmployeeOrgChart",
      "score": 9.278342E-07
    },
    {
      "intent": "MoveAssetsOrPeople",
      "score": 9.278342E-07
    }
  ],
  "entities": [
    {
      "entity": "understand your responsibilities as a member of the community",
      "type": "FormName",
      "startIndex": 18,
      "endIndex": 78,
      "role": ""
    }
  ]
}
```


## <a name="sentiment-analysis"></a>Анализ мнений
Если настроен анализ тональности, он входит в ответ JSON LUIS. Дополнительные сведения об анализе тональности см. в документации по [анализу текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

### <a name="sentiment-data"></a>Данные тональности
Данные тональности представляют собой оценку между 1 и 0, означающую положительную (ближе к 1) или отрицательную (ближе к 0) тональность данных.

При использовании языка и региональных параметров `en-us` выводится следующий ответ:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

Для всех других языков и региональных параметров выводится ответ:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```


### <a name="key-phrase-extraction-entity-data"></a>Данные сущности извлечения ключевой фразы
Сущность извлечения ключевой фразы возвращает ключевые фразы в высказывании, предоставленном службой [анализа текста](https://docs.microsoft.com/azure/cognitive-services/text-analytics/).

<!-- TBD: verify JSON-->
```JSON
"keyPhrases": [
    "places",
    "beautiful views",
    "favorite trail"
]
```

## <a name="data-matching-multiple-entities"></a>Данные, соответствующие нескольким сущностям
LUIS возвращает все сущности, обнаруженные в высказывании. В результате чат-боту может потребоваться принять решение на основе результатов. Высказывание может содержать много сущностей:

`book me 2 adult business tickets to paris tomorrow on air france`

Конечная точка LUIS может обнаруживать те же данные в различных сущностях: 

```JSON
{
  "query": "book me 2 adult business tickets to paris tomorrow on air france",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 1.0
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 1.0
    },
    {
      "intent": "Concierge",
      "score": 0.04216196
    },
    {
      "intent": "None",
      "score": 0.03610297
    }
  ],
  "entities": [
    {
      "entity": "air france",
      "type": "Airline",
      "startIndex": 54,
      "endIndex": 63,
      "score": 0.8291798
    },
    {
      "entity": "adult",
      "type": "Category",
      "startIndex": 10,
      "endIndex": 14,
      "resolution": {
        "values": [
          "adult"
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 36,
      "endIndex": 40,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    },
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 42,
      "endIndex": 49,
      "resolution": {
        "values": [
          {
            "timex": "2018-02-21",
            "type": "date",
            "value": "2018-02-21"
          }
        ]
      }
    },
    {
      "entity": "paris",
      "type": "Location::ToLocation",
      "startIndex": 36,
      "endIndex": 40,
      "score": 0.9730773
    },
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "business",
      "type": "Seat",
      "startIndex": 16,
      "endIndex": 23,
      "resolution": {
        "values": [
          "business"
        ]
      }
    },
    {
      "entity": "2 adult business",
      "type": "TicketSeatOrder",
      "startIndex": 8,
      "endIndex": 23,
      "score": 0.8784727
    }
  ],
  "compositeEntities": [
    {
      "parentType": "TicketSeatOrder",
      "value": "2 adult business",
      "children": [
        {
          "type": "Category",
          "value": "adult"
        },
        {
          "type": "builtin.number",
          "value": "2"
        },
        {
          "type": "Seat",
          "value": "business"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о добавлении сущностей в приложение LUIS см. в статье [Добавление сущностей](luis-how-to-add-entities.md).