---
title: Общие сведения о службе поиска ответов в проекте в Microsoft Cognitive Services | Документация Майкрософт
description: Общие сведения о службе поиска ответов в проекте.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: d87cf1390970d2c815b94bcaee7e07c19bc03cce
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381836"
---
# <a name="what-is-project-answer-search"></a>Общие сведения о службе поиска ответов в проекте
API службы поиска ответов в проекте возвращает ответы на вопросительные запросы с помощью конечной точки Bing версии 7. Вопросительный запрос, например "Какова окружность Земли?", возвращает ответ с фактическими данными.  Запрос сведений о человеке, месте или предмете возвращает информацию об определенной сущности. Этот сценарий можно использовать в приложениях, например в чат-ботах, приложениях обмена сообщениями, устройствах чтения и т. д.  

Запросы возвращают ответы в зависимости от сценария: веб-страницы возвращаются всегда, а [факты](fact-queries.md) и (или) [сущности](entity-queries.md) — только при необходимости.

## <a name="endpoint"></a>Конечная точка
Чтобы получить ответы на вопросы или сведения о человеке, месте или предмете, запрос следует отправить на конечную точку API службы поиска ответов. Используйте заголовки и параметры URL-адреса для разных спецификаций.  Добавьте заголовок *Ocp-Apim-Subscription-Key* с допустимым токеном.  Обязательно укажите параметр рынка. Сейчас поддерживается только рынок `en-us`.

Приведенный ниже запрос возвращает ответ на вопрос "Какова окружность Земли?"

GET:
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

````

Параметр `q=` URL-адреса позволяет указать объект поиска.

## <a name="response-object"></a>Объект ответа

Ответ содержит заголовки HTTP, веб-страницы, факты и (или) сущности.

````
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

````

## <a name="terms-of-use"></a>Условия использования
Использование и отображение служб поиска ответов в проекте и определения трендов видео в проекте регулируются условиями [API-интерфейсов поиска Bing](use-display-requirements.md).

Вы или третья сторона, действующая от вашего имени, можете не использовать, хранить, кэшировать, распределять данные API службы предварительного просмотра URL-адресов или предоставлять доступ к ним с целью тестирования, разработки, обучения, распределения или предоставления доступа любой сторонней службе или функции (не от корпорации Майкрософт). 

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>Определение принадлежности данных  

Результаты службы поиска ответов в проекте содержат сведения, принадлежащие третьим лицам. Вы несете ответственность за их должное использование, например за соблюдение условий лицензирования Creative Commons в пользовательском интерфейсе.  
  
Если ответ или результат содержит поля `contractualRules`, `attributions` или `provider`, вы должны определить принадлежность данных. Если эти поля отсутствуют, это делать не нужно. Когда ответ содержит поле `contractualRules` и поле `attributions`, и (или) `provider`, принадлежность данных следует определить с помощью договорных правил.  
  
В приведенном ниже примере показана сущность, содержащая договорное правило MediaAttribution и раздел image с полем `provider`. Правило MediaAttribution определяет раздел image как целевой объект, поэтому вы должны игнорировать поле `provider` этого раздела и вместо этого определить принадлежность данных с помощью правила MediaAttribution.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
Если договорное правило содержит поле `targetPropertyName`, это правило применяется только к целевому полю. В противном случае правило применяется к родительскому объекту, который содержит поле `contractualRules`.  
  
  
В приведенном ниже примере правило `LinkAttribution` содержит поле `targetPropertyName`, поэтому это правило применяется к полю `description`. Для правил, применяемых к определенным полям, сразу после целевых данных необходимо включить строку, содержащую гиперссылку на веб-сайт поставщика. Например, чтоб определить принадлежность текста описания, сразу после него включите строку с гиперссылкой на веб-сайт поставщика. В этом случае создайте ссылку на сайт en.wikipedia.org.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>Определение принадлежности лицензии  

Если список договорных правил содержит правило [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution), сразу после содержимого, к которому применяется лицензия, необходимо добавить строку уведомления. Правило `LicenseAttribution` определяет свойство, к которому применяется лицензия, с помощью поля `targetPropertyName`.  
  
Ниже приведен пример с использованием правила `LicenseAttribution`.  
  
![Определение принадлежности лицензии](./media/licenseattribution.png)  
  
Уведомление о лицензии должно содержать гиперссылку на сайт со сведениями о лицензии. Как правило, имя лицензии делают гиперссылкой. Например, если уведомление содержит описание **Текст лицензии CC-BY-SA**, а CC-BY-SA — это имя лицензии, то CC-BY-SA нужно сделать гиперссылкой.  
  
### <a name="link-and-text-attribution"></a>Определение принадлежности текста или ссылки  

Как правило, правила [LinkAttribution](reference.md#linkattribution) и [TextAttribution](reference.md#textattribution) определяют поставщика данных. Поле `targetPropertyName` определяет поле, к которому применяется правило.  
  
Чтобы определить принадлежность поставщиков, сразу после содержимого, к которому применяется определение принадлежности (например, целевое поле), добавьте строку. Строка должна четко указывать, что данные принадлежат этим поставщикам. Например, данные из сайта en.wikipedia.org. Если используются правила `LinkAttribution`, необходимо создать гиперссылку на веб-сайт поставщика.  
  
Ниже приведен пример с использованием правил `LinkAttribution` и `TextAttribution`.  
  
![Определение принадлежности ссылки](./media/linktextattribution.png)  

### <a name="media-attribution"></a>Определение принадлежности медиаданных  

Если сущность содержит изображение, необходимо указать ссылку для перехода на веб-сайт поставщика. Если сущность включает правило [MediaAttribution](reference.md#mediaattribution), создайте такую ссылку, указав URL-адрес правила. В противном случае используйте URL-адрес в поле `provider` изображения.  
  
Ниже приведен пример, в котором используется поле `provider` изображения и договорные правила. Так как в этом примере содержится договорное правило, вы должны игнорировать поле `provider` изображения и применить правило `MediaAttribution`.  
  
![Определение принадлежности медиаданных](./media/mediaattribution.png)  

## <a name="next-steps"></a>Дополнительная информация
- [Project Answer Search query in C#](c-sharp-quickstart.md) (Запрос службы поиска ответов в проекте на языке C#)
- [Project Answer Search query in Java](java-quickstart.md) (Запрос службы поиска ответов в проекте на языке Java)
- [Project Answer Search Node quickstart](node-quickstart.md) (Краткое руководство по запросам Node в службе поиска ответов в проекте)
- [Project Answer Search Python quickstart](python-quickstart.md) (Краткое руководство по запросам Python в службе поиска ответов в проекте)
