---
title: Запрос сущностей из службы поиска ответов в проектах в Microsoft Cognitive Services | Документация Майкрософт
description: Запросы сущностей с использованием службы поиска ответов в проектах.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/16/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 2b8382b791c02514e5110097700e223d98fafd6a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381744"
---
# <a name="query-for-entities"></a>Запрос для сущностей

Если запрос извлекает сведения о человеке, месте или предмете, результат может содержать ответ `entities`.  Запросы всегда возвращают веб-страницы, [факты](fact-queries.md) и (или) [сущности](entity-queries.md), которые зависят от запроса.

Сущности поддерживают три сценария выполнения запроса. 
-   Основная сущность: — имеется только одна сущность, которая соответствует запросу и намерению пользователя. Например, запрос "Space Needle", относится к сценарию основной сущности. 
-   Неоднозначность: запросу и намерению пользователя соответствует несколько сущностей, и именно пользователь должен выбрать правильную сущность. Например, запрос "Game of Thrones"относится к сценарию неоднозначности, так как возвращает результаты для телесериала и цикла книг. 
-   Список: запросу и намерению пользователя соответствует несколько сущностей. Например, запрос "List of endangered species"относится к этому сценарию, так как он возвращает табличные значения, отформатированные для отображения в виде строк и ячеек. 
 
Чтобы определить сценарий запроса, используйте поле `queryScenario` объекта `entities`. Данные, которые включает в себя сущность, зависят от ее типа. Хотя сущности содержат общие основные сведения, некоторые из них, например достопримечательности или книги, включают в себя дополнительные свойства. Сущности, которые включают в себя дополнительные свойства, содержат поле `_type` с указанием, используемым сериализатором. Следующие сущности в себя включают дополнительные свойства: 
-   Book 
-   MusicRecording 
-   Person 
-   Attraction 
 
Чтобы определить тип сущности, содержащейся в ответе, используйте поле `entityTypeHints`, как показано в запросе "Bill Gates".
````
        },
        "description": "Bill Gates is an American business man and philanthropist, co-founder of Microsoft",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Person"
          ]
        },
        "bingId": "6d7d66a7-2cb8-0ae9-637c-f81fd749dc9a"
      }
````
Ниже приведен запрос "Space Needle".
````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=space+needle&mkt=en-us
````
Результат содержит ответ `entities`. Обратите внимание на поля `entityScenario` и `entityTypeHints`. 
````
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
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dSpace+Needle\u0026filters\u003dsid:%22f8dd5b08-206d-2554-6e4a-893f51f4de7e%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Space Needle",
        "image": {
          "name": "Space Needle",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA15d336cf119b9b5c7e0ab37e271421d3\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Space_Needle"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 152,
          "sourceHeight": 300
        },
        "description": "The Space Needle is an observation tower in Seattle, Washington, a landmark of the Pacific Northwest, and an icon of Seattle. It was built in the Seattle Center for the 1962 World\u0027s Fair, which drew over 2.3 million visitors, when nearly 20,000 people a day used its elevators.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Attraction"
          ]
        },
        "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e"
      }
    ]
  },
````

Запрос может вернуть список, если он соответствует запросу.

**Запрос:** следующий запрос находит список вымирающих видов.

````
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=list+of+endangered+species

````

**Ответ:** ответ включает в себя список, отформатированный для отображения в виде табличных значений.
````
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "seeMoreUrl": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "value": [
      {
        "subjectName": "Species Directory",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "Common name",
            "Scientific name",
            "Conservation status ↓"
          ],
          "rows": [
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Amur Leopard",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/amur-leopard"
                },
                {
                  "text": "Panthera pardus orientalis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Black Rhino",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/black-rhino"
                },
                {
                  "text": "Diceros bicornis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Bornean Orangutan",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/bornean-orangutan"
                },
                {
                  "text": "Pongo pygmaeus"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Cross River Gorilla",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/cross-river-gorilla"
                },
                {
                  "text": "Gorilla gorilla diehli"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "46 more rows",
            "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
          }
        }
      }
    ]
  },

````


## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство для C#](c-sharp-quickstart.md)
- [Краткое руководство для Java](java-quickstart.md)
- [Краткое руководство для Node](node-quickstart.md)
- [Краткое руководство для Python](python-quickstart.md)