---
title: Быстрое начало работы с API для поиска новостей | Документация Майкрософт
description: В этой статье показано, как начать работу с API Bing для поиска новостей.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 9CF6EAF3-42D8-4321-983C-4AC3896E8E03
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2aa1b3de07bd61eebfc1d410cda76c32fc7c958e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382336"
---
# <a name="your-first-news-search-query"></a>Ваш первый запрос по поиску новостей

Прежде чем выполнить первый вызов, необходимо получить ключ подписки Cognitive Services. Для получения ключа см. статью [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api).

Для получения результатов поиска, относящихся только к новостям, необходимо отправить запрос GET к следующей конечной точке:

```http
https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

В запросе должен использоваться протокол HTTPS.

Рекомендуется, чтобы все запросы поступали с сервера. Распространение ключа в рамках клиентского приложения создает больше возможностей для доступа к нему злоумышленников. Осуществление вызовов с сервера также предоставляет единую точку обновления для будущих версий API.

В запросе необходимо указать параметр [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query), который содержит условие поиска пользователя. В запросе можно также указать необязательный параметр [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#mkt), определяющий рынок, для которого будут отображаться результаты. Список необязательных параметров запроса, таких как `freshness` и `textDecorations`, можно найти в разделе [Параметры запроса](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Все значения параметров запроса должны быть указаны в формате URL-адреса.

Запрос должен содержать заголовок [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#subscriptionkey). Приведенные ниже заголовки являются необязательными, но их также рекомендуется указать:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#location)

Заголовки IP-адреса и расположения клиента важны для отображения содержимого с учетом расположения.

Список всех заголовков в запросах и ответах приведен в разделе [Заголовки](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#headers).

## <a name="the-request"></a>Запрос

Ниже показан запрос новостей, который включает в себя все рекомендуемые параметры и заголовки запроса. Если вы впервые вызовете любой из интерфейсов API Bing, не включайте заголовок идентификатора клиента. Включите идентификатор клиента, только если вы ранее вызывали API Bing, а Bing возвратил идентификатор клиента для комбинации пользователей и устройств.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="the-response"></a>Ответ

Ниже показан ответ на предыдущий запрос. В примере также показаны заголовки ответа для Bing.

```http
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "Sailing College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },
    {
        "name" : "Reunion at Fabrikam Lakes Sailing Club, celebrates 50 years...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.38210...",
                "width" : 650,
                "height" : 366
            }
        },
        "description" : "The reunion on April 29, at Fabrikam Lakes Sailing...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T13:08:00"
    },
    {
        "name" : "Sailing Club to host Dinghy Sailing World...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.364AB41...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    },
    {
        "name" : "A 24-Carat Dinghy",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.6CC2...",
                "width" : 700,
                "height" : 466
            }
        },
        "description" : "“Hard dinghies are for purists, the kind of people who...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-03T12:14:00",
        "category" : "Politics"
    }]
}
```

## <a name="next-steps"></a>Дополнительная информация

Проверьте, как работает API. Перейдите на страницу [консоли тестирования API для поиска новостей](https://dev.cognitive.microsoft.com/docs/services/56b43f72cf5ff8098cef380a/operations/56f02400dbe2d91900c68553).

Дополнительные сведения об использовании объектов ответа см. в статье [Что такое API Bing для поиска новостей?](./search-the-web.md) Там вы также найдете дополнительные сведения о таких стандартных действиях:

- [Получение главных новостей за сегодняшний день](./search-the-web.md#getting-todays-top-news)
- [Получение новостей по категориям](./search-the-web.md#getting-news-by-category)
- [Получение популярных новостей](./search-the-web.md#getting-trending-news)
