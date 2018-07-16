---
title: Быстрое начало работы с API для поиска сущностей | Документация Майкрософт
description: В этой статье показано, как начать работу с API Bing для поиска сущностей.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: B206A254-B7E9-49FF-AFD5-87B1E4D6D30B
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 07/06/2017
ms.author: scottwhi
ms.openlocfilehash: 12031d2447920c7e2d6180f35cf4fb29aa1b6150
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380108"
---
# <a name="making-your-first-entities-request"></a>Создание первого запроса на поиск сущностей

API для поиска сущностей отправляет поисковый запрос в службу Bing и получает результаты, которые включают сущности и места. Результаты поиска мест включают рестораны, отели или другие местные учреждения. При поиске мест в запросе может указываться имя местного учреждения или запрашиваться список (например, ближайших ресторанов). В результатах поиска сущностей содержатся субъекты, места или вещи. Местами в данном контексте являются достопримечательности, регионы, страны и т. д. 

## <a name="first-steps"></a>Первые шаги

Прежде чем выполнить первый вызов, необходимо получить ключ подписки Cognitive Services. Для получения ключа см. статью [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-entities-search-api). (Если API для поиска сущностей не отображается в верхней части экрана, то щелкните вкладку **Поиск** и прокрутите вниз, пока не увидите его.)

## <a name="the-endpoint"></a>Конечная точка

Чтобы получить результаты поиска сущностей и мест, отправьте запрос GET на следующую конечную точку:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

В запросе должен использоваться протокол HTTPS.

Рекомендуется, чтобы все запросы поступали с сервера. Распространение ключа в рамках клиентского приложения создает больше возможностей для доступа к нему злоумышленников. Осуществление вызовов с сервера также предоставляет единую точку обновления для будущих версий API.

## <a name="specifying-query-parameters-and-headers"></a>Указание параметров запроса и заголовков

В запросе необходимо указать параметр [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query), который содержит условие поиска пользователя. В запросе также необходимо указать параметр [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#mkt), определяющий рынок, для которого будут отображаться результаты. Список необязательных параметров запроса можно найти в разделе [Параметры запроса](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters). Все параметры запроса необходимо закодировать в URL-адресе.  
  
Запрос должен содержать заголовок [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#subscriptionkey). Приведенные ниже заголовки являются необязательными, но их также рекомендуется указать:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#location)  

Заголовки IP-адреса и расположения клиента важны для отображения содержимого с учетом расположения.  

Список всех заголовков в запросах и ответах приведен в разделе [Заголовки](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Запрос

Ниже показан запрос сущностей, который включает в себя все рекомендуемые параметры и заголовки запроса. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Если вы впервые вызовете любой из интерфейсов API Bing, не включайте заголовок идентификатора клиента. Включите идентификатор клиента, только если вы ранее вызывали API Bing, а Bing возвратил идентификатор клиента для комбинации пользователей и устройств.

## <a name="the-response"></a>Ответ

Ниже показан ответ на предыдущий запрос. В примере также показаны заголовки ответа для Bing. Сведения об объекте ответа см. в разделе [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#searchresponse).

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```



## <a name="next-steps"></a>Дополнительная информация

Проверьте, как работает API. Перейдите на страницу [консоли тестирования API для поиска сущностей](https://dev.cognitive.microsoft.com/docs/services/7a3fb374be374859a823b79fd938cc65/). 

Дополнительные сведения об использовании объектов ответа см. в статье [Что такое API Bing для поиска сущностей?](./search-the-web.md)

Обязательно прочтите [эту статью](./use-display-requirements.md), чтобы не нарушать какие-либо правила использования результатов поиска.
