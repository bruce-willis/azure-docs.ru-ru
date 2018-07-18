---
title: Быстрое начало работы с API для поиска в Интернете | Документация Майкрософт
description: В этой статье показано, как начать работу с API Bing для поиска в Интернете.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 27B4B51A-D017-44C8-8E4E-9684DC553886
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 0b8c4678a518985a4be3ee426a85b0a85dd2365d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380256"
---
# <a name="your-first-bing-search-query"></a>Ваш первый поисковый запрос к службе Bing

Прежде чем выполнить первый вызов, необходимо получить ключ подписки Cognitive Services. Сведения о получении ключа см. в на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

Для получения результатов поиска в Интернете необходимо отправить запрос GET к следующей конечной точке.  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/search
```  

В запросе должен использоваться протокол HTTPS.

Рекомендуется, чтобы все запросы поступали с сервера. Распространение ключа в рамках клиентского приложения создает больше возможностей для доступа к нему злоумышленников. Осуществление вызовов с сервера также предоставляет единую точку обновления для будущих версий API.  
  
В запросе необходимо указать параметр [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query), который содержит условия поиска пользователя. В запросе можно также указать необязательный параметр [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#mkt), определяющий рынок, для которого будут отображаться результаты. Список необязательных параметров запроса, таких как `responseFilter` и `textDecorations`, можно найти в разделе [Параметры запроса](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query-parameters). Все значения параметров запроса должны быть указаны в формате URL-адреса.  
  
Запрос должен содержать заголовок [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#subscriptionkey). Приведенные ниже заголовки являются необязательными, но их также рекомендуется указать:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#location)  

Заголовки IP-адреса и расположения клиента важны для отображения содержимого с учетом расположения. Например, если запрос пользователя — *кораблевождение+занятия*, вероятно, его интересуют занятия неподалеку от его расположения. Если требуется, чтобы результаты содержали занятия, проводящиеся вблизи расположения пользователя, необходимо добавить в запрос заголовок расположения. Можно также добавить в него IP-заголовок клиента. Это не так важно, если в условии запроса явно указывается расположение (например, "кораблевождение+занятия+сочи+ключи"). 

Список всех заголовков в запросах и ответах приведен в разделе [Заголовки](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers).

## <a name="the-request"></a>Запрос

Ниже показан поисковый запрос, который включает в себя все рекомендуемые параметры и заголовки запроса. Если вы впервые вызовете любой из API-интерфейсов Bing, не включайте заголовок идентификатора клиента. Включите идентификатор клиента, только если вы ранее вызывали API Bing, а Bing возвратил идентификатор клиента для комбинации пользователей и устройств. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+lessons+seattle&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Ниже показан ответ на предыдущий запрос. В примере также показаны заголовки ответа для Bing.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Seattle Sailing Club | Lessons, Membership & More",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/seattlesailing.com",
            "snippet" : "Sailing is fun! Seattle Sailing Club makes it accessible and affordable...",
            "deepLinks" : [{
                "name" : "Learn to Sail",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD..."
            },
            {
                "name" : "Fleet",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
                "snippet" : "The Seattle Sailing Club is nothing without our fleet of excellent..."
            },
            {
                "name" : "Sailing Lessons",
                "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
                "snippet" : "Sailing Lessons. Taking the first step into the sailing community..."
            },
            {
                "name" : "Membership",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD3...",
                "snippet" : "Seattle sailboat rental for less than the cost of moorage!"
            },
            {
                "name" : "Hours – Directions",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874...",
                "snippet" : "Contact the Seattle Sailing Club by phone or email..."
            },
            {
                "name" : "Overnight Cruising",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD...",
                "snippet" : "Overnight Cruising. Setting sail for a multi-day adventure..."
            }],
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1",
            "name" : "Best Sailing lessons in Seattle, WA - yelp.com",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38AA...",
            "displayUrl" : "https:\/\/www.yelp.com\/search?find_desc=sailing+lessons&find_loc...",
            "snippet" : "Reviews on Sailing lessons in Seattle, WA - Seattle Sailing...",
            "dateLastCrawled" : "2017-04-01T10:49:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2",
            "name" : "Seattle Sailing with Windworks Sailing Club - Sailing ...",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD3...",
            "displayUrl" : "https:\/\/www.windworkssailing.com",
            "snippet" : "Seattle sailing club for sailing lessons, sailboat charters...",
            "dateLastCrawled" : "2017-04-09T03:33:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3",
            "name" : "Sailing Lessons in Downtown Seattle - SheSails Seattle",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
            "displayUrl" : "shesailsseattle.com",
            "snippet" : "SheSails Seattle is sailing lessons and sailboat charters in a fun...",
            "dateLastCrawled" : "2017-04-05T07:15:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4",
            "name" : "Seattle Sailing Club - 19 Photos & 27 Reviews - Boating ...",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.yelp.com\/biz\/seattle-sailing-club-seattle",
            "snippet" : "27 reviews of Seattle Sailing Club \"Love the Seattle Sailing...",
            "dateLastCrawled" : "2017-04-08T14:40:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5",
            "name" : "Puget Sound Sailing - Seattle and Tacoma - PUGET SOUND ...",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDA...",
            "displayUrl" : "www.pugetsoundsailing.com",
            "snippet" : "Puget Sound Sailing Institute is an American Sailing Association...",
            "dateLastCrawled" : "2017-04-09T01:39:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.6",
            "name" : "Seattle Yacht Club - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "www.seattleyachtclub.org",
            "snippet" : "Seattle Yacht Club Awarded Joe Prosser Trophy for Excellence...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        }],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        },
        {
            "text" : "sailing schools seattle",
            "displayText" : "sailing schools seattle",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4..."
        },
        {
            "text" : "seattle boating lessons",
            "displayText" : "seattle boating lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4..."
        },
        {
            "text" : "adult sailing lessons seattle",
            "displayText" : "adult sailing lessons seattle",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED..."
        },
        {
            "text" : "seattle sailing club",
            "displayText" : "seattle sailing club",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED459..."
        },
        {
            "text" : "seattle sailing club lessons",
            "displayText" : "seattle sailing club lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289..."
        },
        {
            "text" : "sailing in seattle area",
            "displayText" : "sailing in seattle area",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED45948..."
        },
        {
            "text" : "seattle sailing lessons lake union",
            "displayText" : "seattle sailing lessons lake union",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED459..."
        }]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 6,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.6"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

Проверьте, как работает API. Перейдите на страницу [консоли тестирования API для поиска в Интернете](https://dev.cognitive.microsoft.com/docs/services/56b43eeccf5ff8098cef3807/operations/56b4447dcf5ff8098cef380d). 

Дополнительные сведения об использовании объектов ответа см. в разделе о [поиске в Интернете](./search-the-web.md).