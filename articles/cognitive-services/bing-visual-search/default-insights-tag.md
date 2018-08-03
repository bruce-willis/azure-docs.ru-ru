---
title: Тег аналитических сведений по умолчанию | Документация Майкрософт
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Сведения о стандартном теге аналитических сведений, который возвращается для изображения при визуальном поиске.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/17/2018
ms.author: scottwhi
ms.openlocfilehash: a0ced7076c566c819f9e3f7abc5e2fa3930fa0b3
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004307"
---
# <a name="default-insights-tag"></a>Тег аналитических сведений по умолчанию

В качестве тега аналитических сведений по умолчанию используется тот, для которого поле `displayName` содержит пустую строку. В следующем примере показан список допустимых действий (аналитических сведений) по умолчанию. Список действий в ответе от изображения. И список свойств для каждого действия может различаться в зависимости от изображения, поэтому проверьте, существует ли свойство, прежде чем пытаться его использовать.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {
          "_type" : "ImageModuleAction",
          "actionType" : "PagesIncluding",
          "data" : {
            "value" : [...]
          }
        },
        {
          "_type" : "ImageShoppingSourcesAction",
          "actionType" : "ShoppingSources",
          "data" : {
            "offers" : [...]
          }
        },
        {
          "image" : {...},
          "actionType" : "MoreSizes"
        },
        {
          "_type" : "ImageModuleAction",
          "actionType" : "VisualSearch",
          "data" : {
            "value" : [...]
          }
        },
        {
          "_type" : "ImageRecipesAction",
          "actionType" : "Recipes",
          "data" : {
            "value" : [...]
          }
        },
        {
          "image" : {...},
          "actionType" : "ImageById"
        },
        {
          "_type" : "ImageModuleAction",
          "actionType" : "ProductVisualSearch",
          "data" : {
            "value" : [...]
          }
        },
        {
          "_type" : "ImageRelatedSearchesAction",
          "actionType" : "RelatedSearches",
          "data" : {
            "value" : [...]
          }
        },
        {
          "_type" : "ImageRelatedSearchesAction",
          "actionType" : "DocumentLevelSuggestions",
          "data" : {
            "value" : [...]
          }
        }
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

## <a name="pagesincluding-insight"></a>Аналитические сведения PagesIncluding

Аналитические сведения PagesIncluding возвращают список веб-страниц с указанным изображением. Это фактически список объектов Image, для которых поле `hostPageUrl` содержит URL-адрес веб-страницы с этим изображением. Пример использования PagesIncluding см. [здесь](./bing-insights-usage.md#pagesincluding-insight-example). 

```json
      {
        "_type" : "ImageModuleAction",
        "actionType" : "PagesIncluding",
        "data" : {
          "value" : [
            {
              "webSearchUrl" : "https://www.bing.com\/images\/search?",
              "name" : "Today's smoking hot country",
              "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
              "datePublished" : "2017-09-20T12:00:00.0000000Z",
              "contentUrl" : "http:\/\/contoso.com\/wordstuff",
              "hostPageUrl" : "http:\/\/contoso.com\/2017\/09\/20\/car",
              "contentSize" : "122540 B",
              "encodingFormat" : "jpeg",
              "hostPageDisplayUrl" : "contoso.com\/2017\/09\/20\/car",
              "width" : 894,
              "height" : 1200,
              "thumbnail" : {
                "width" : 474,
                "height" : 636
              },
              "imageInsightsToken" : "ccid_CO5GEthj*mid_5323B1",
              "insightsMetadata" : {
                "pagesIncludingCount" : 12,
                "availableSizesCount" : 7
              },
              "imageId" : "5323B1900FB9087B6B45D176D234E1F2F23CD3A5",
              "accentColor" : "55585B"
            }
          ]
        }
      }
```

## <a name="shoppingsources-insight"></a>Аналитические сведения ShoppingSources

Аналитические сведения ShoppingSources содержат список веб-сайтов, на которых пользователь может купить товар, изображенный на рисунке. Список предложений содержит URL-адрес веб-страницы, где пользователь может приобрести этот товар, цену товара и оценку, сведения о рейтинге или отзывах. Пример использования ShoppingSources см. [здесь](./bing-insights-usage.md#shoppingsources-insight-example).

```json
      {
        "_type" : "ImageShoppingSourcesAction",
        "actionType" : "ShoppingSources",
        "data" : {
          "offers" : [
            {
              "name" : "Apple Pie",
              "url" : "https:\/\/contoso.com\/product_p\/l10.htm",
              "description" : "A taste of the crust, apple, and pie filling...",
              "seller" : {
                "name" : "Contoso"
              },
              "price" : 3.99,
              "priceCurrency" : "USD",
              "aggregateRating" : {
                "ratingValue" : 5
              },
              "lastUpdated" : "2018-04-16T00:00:00.0000000"
            }
          ]
        }
      }
```


## <a name="moresizes-insight"></a>Аналитические сведения MoreSizes

Аналитические сведения MoreSizes предоставляют число размеров (больших и маленьких) изображения, которые система Bing смогла найти в Интернете (см. поле `availableSizesCount`).

```json
      {
        "image" : {
          "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detai...",
          "name" : "Making Apple Pie",
          "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP....",
          "datePublished" : "2013-06-21T12:00:00.0000000Z",
          "contentUrl" : "http:\/\/contoso.com\/content\/uploads\/2013\/06\/apple-pie.jpg",
          "hostPageUrl" : "http:\/\/contoso.com\/2013\/06\/21\/making-apple-pie\/",
          "contentSize" : "134847 B",
          "encodingFormat" : "jpeg",
          "hostPageDisplayUrl" : "contoso.com\/2013\/06\/21\/making-apple-pie",
          "width" : 1050,
          "height" : 765,
          "thumbnail" : {
            "width" : 474,
            "height" : 345
          },
          "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CF...",
          "insightsMetadata" : {
            "recipeSourcesCount" : 6,
            "pagesIncludingCount" : 103,
            "availableSizesCount" : 28
          },
          "imageId" : "D12339146CFEDF3D409CC7A66D2C98D0D71904D4",
          "accentColor" : "3A0B01"
        },
        "actionType" : "MoreSizes"
      },
```

## <a name="visualsearch-insight"></a>Аналитические сведения VisualSearch

Аналитические сведения VisualSearch предоставляют список изображений, визуально похожих на исходное изображение (с содержимым похожим на содержимое исходного изображения). Пример использования VisualSearch см. [здесь](./bing-insights-usage.md#visualsearch-insight-example).

```json
      {
        "_type" : "ImageModuleAction",
        "actionType" : "VisualSearch",
        "data" : {
          "value" : [
            {
              "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
              "name" : "An apple pie...",
              "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.z...",
              "datePublished" : "2017-03-18T00:17:00.0000000Z",
              "contentUrl" : "http:\/\/contoso.net\/images\/8\/8a\/an_apple_pie.png",
              "hostPageUrl" : "http:\/\/contoso.com\/wiki\/an_apple_pie.png",
              "contentSize" : "87930 B",
              "encodingFormat" : "png",
              "hostPageDisplayUrl" : "contoso.com\/wiki\/an_apple_pie.png",
              "width" : 263,
              "height" : 192,
              "thumbnail" : {
                "width" : 474,
                "height" : 346
              },
              "imageInsightsToken" : "ccid_zhRxfGkI*mid_1DCBA7AA6D231...",
              "insightsMetadata" : {
                "recipeSourcesCount" : 6,
                "pagesIncludingCount" : 103,
                "availableSizesCount" : 28
              },
              "imageId" : "1DCBA7AA6D23147F9DD06D47DB3A38EB25389",
              "accentColor" : "3E0D01"
            }
          ]
        }
      }
```

## <a name="recipes-insight"></a>Аналитические сведения Recipes

Аналитические сведения Recipes содержат список веб-страниц, на которых есть инструкции по приготовлению блюда, изображенного на рисунке. Пример использования Recipes см. [здесь](./bing-insights-usage.md#recipes-insight-example).

```json
      {
        "_type" : "ImageRecipesAction",
        "actionType" : "Recipes",
        "data" : {
          "value" : [
            {
              "name" : "Granny's Apple Pie",
              "url" : "http:\/\/contoso.com\/recipes\/appetizer\/apple-pie.html",
              "description" : "I love Granny's apple pie. Sooooo delicious...",
              "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=A63002cd9",
              "creator" : {
                "_type" : "Person",
                "name" : "Charlene Whitney"
              },
              "aggregateRating" : {
                "text" : "5",
                "ratingValue" : 5,
                "bestRating" : 5,
                "reviewCount" : 1
              },
              "cookTime" : "PT45M",
              "prepTime" : "PT1H",
              "totalTime" : "PT1H45M"
            }
          ]
        }
      }
```


## <a name="imagebyid-insight"></a>Аналитические сведения ImageById

Аналитические сведения ImageById предоставляют объект `Image` с изображением, для которого вы запросили эти сведения.

```json
      {
        "image" : {
          "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=deta...",
          "name" : "Making Apple Pie",
          "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP...",
          "datePublished" : "2013-06-21T12:00:00.0000000Z",
          "contentUrl" : "http:\/\/contoso.com\/content\/uploads\/2013\/06\/apple-pie.jpg",
          "hostPageUrl" : "http:\/\/contoso.com\/2013\/06\/21\/making-apple-pie\/",
          "contentSize" : "134847 B",
          "encodingFormat" : "jpeg",
          "hostPageDisplayUrl" : "contoso.com\/2013\/06\/21\/making-apple-pie",
          "width" : 1050,
          "height" : 765,
          "thumbnail" : {
            "width" : 474,
            "height" : 345
          },
          "imageInsightsToken" : "ccid_tmaGQ2eU*mid_D12339146CFE...",
          "insightsMetadata" : {
            "recipeSourcesCount" : 6,
            "pagesIncludingCount" : 103,
            "availableSizesCount" : 28
          },
          "imageId" : "D12339146CFEDF3D409A66D2C98D0D71904D4",
          "accentColor" : "3A0B01"
        },
        "actionType" : "ImageById"
      },
```


## <a name="productvisualsearch-insight"></a>Аналитические сведения ProductVisualSearch

Аналитические сведения ProductVisualSearch предоставляют список изображений с товарами, которые визуально похожи на товары на исходном изображении. Поле `insightsMetadata` может содержать сведения о предложениях по этим товарам, в том числе их цену. 

```json
      {
        "_type" : "ImageModuleAction",
        "actionType" : "ProductVisualSearch",
        "data" : {
          "value" : [
            {
              "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detail...",
              "name" : "Contoso 4-Piece Kitchen Package...",
              "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.l9hzaabu-RJd...",
              "datePublished" : "2017-07-16T04:28:00.0000000Z",
              "contentUrl" : "https:\/\/www.contoso.com\/assets\/media\/images\/prod...",
              "hostPageUrl" : "https:\/\/www.contoso.com\/4-piece-kitchen-package...",
              "contentSize" : "13594 B",
              "encodingFormat" : "jpeg",
              "hostPageDisplayUrl" : "https:\/\/www.contoso.com\/4-piece-kitchen-package...",
              "width" : 450,
              "height" : 332,
              "thumbnail" : {
                "width" : 474,
                "height" : 349
              },
              "imageInsightsToken" : "ccid_l9hzaabu*mid_70A8B616355D681DB9A5A...",
              "insightsMetadata" : {
                "shoppingSourcesCount" : 1,
                "recipeSourcesCount" : 0,
                "aggregateOffer" : {
                  "name":"4-Piece Kitchen Package with...",
                  "priceCurrency":"USD",
                  "lowPrice":2756,
                  "offers" : [
                    {
                      "name" : "4-Piece Kitchen Package with...",
                      "url" : "https:\/\/www.fabrikam.com\/1234.html?ref=bing",
                      "description" : "This 36 Frenchdoor refrigerator by...",
                      "seller" : {
                        "name" : "Fabrikam",
                        "image" : {
                          "url" : "https:\/\/tse1.mm.bing.net\/th?id=A818f811..."
                        }
                      },
                      "price" : 2756,
                      "priceCurrency" : "USD",
                      "availability" : "InStock",
                      "lastUpdated" : "2018-02-20T00:00:00.0000000"
                    }
                  ],
                  "offerCount":1
                },
                "pagesIncludingCount" : 4,
                "availableSizesCount" : 2
              },
              "imageId" : "70A8B616355D681DA5980A8D0514BCC995A3",
              "accentColor" : "60646B"
            }
          ]
        }
      }
```


## <a name="relatedsearches-insight"></a>Аналитические сведения RelatedSearches

Аналитические сведения RelatedSearches возвращают список связанных поисковых запросов, выполненных другими пользователями (с учетом условий поиска, заданных другими пользователями). Пример использования RelatedSearches см. [здесь](./bing-insights-usage.md#relatedsearches-insight-example).


```json
      {
        "_type" : "ImageRelatedSearchesAction",
        "actionType" : "RelatedSearches",
        "data" : {
          "value" : [
            {
              "text" : "Homemade Apple Pies Recipes",
              "displayText" : "Homemade Apple Pies Recipes",
              "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Homemade...",
              "thumbnail" : {
                "url" : "https:\/\/tse1.mm.bing.net\/th?q=Homemade+Apple+Pies"
              }
            }
          ]
        }
      }
```


## <a name="documentlevelsuggestions-insight"></a>Аналитические сведения DocumentLevelSuggestions

Аналитические сведения DocumentLevelSuggestions предоставляют список предлагаемых поисковых терминов на основе содержимого изображения. 

```json
      {
        "_type" : "ImageRelatedSearchesAction",
        "actionType" : "DocumentLevelSuggestions",
        "data" : {
          "value" : [
            {
              "text" : "American Apple Pie",
              "displayText" : "American Apple Pie",
              "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=American",
              "thumbnail" : {
                "url" : "https:\/\/tse3.mm.bing.net\/th?q=American+Apple+Pie."
              }
            }
          ]
        }
      }
```



## <a name="next-steps"></a>Дополнительная информация

Изучите примеры отображения визуальных аналитических сведений в Bing (см. [Примеры использования аналитических сведений Bing](bing-insights-usage.md)).

Сведения о том, как быстро создать первый запрос, см. в следующих кратких руководствах: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [Node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md).
