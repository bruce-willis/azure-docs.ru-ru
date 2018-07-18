---
title: Краткое руководство по API автозаполнения | Документация Майкрософт
description: В этой статье показано, как начать работу с API автозаполнения Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382144"
---
# <a name="making-your-first-autosuggest-query"></a>Создание первого запроса для Автозаполнения

Прежде чем выполнить первый вызов, необходимо получить ключ подписки Cognitive Services. Сведения о получении ключа см. на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

Чтобы получить результаты поиска в Интернете, необходимо отправить запрос GET к следующей конечной точке:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> Конечная точка для версии 7 (предварительная версия).
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

В запросе должен использоваться протокол HTTPS.

Рекомендуется, чтобы все запросы поступали с сервера. Распространение ключа в рамках клиентского приложения создает больше возможностей для доступа злоумышленников к нему. Осуществление вызовов с сервера также предоставляет единую точку обновления для будущих версий API.

В запросе необходимо указать параметр [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), который содержит частичный поисковый запрос пользователя. В запросе также можно указать необязательный параметр [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), который определяет рынок, для которого будут отображаться результаты. Список необязательных параметров запроса см. в [этом разделе](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Все значения параметров запроса должны быть указаны в формате URL-адреса.

Запрос должен содержать заголовок [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Приведенные ниже заголовки являются необязательными, но их также рекомендуется указать:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent);
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid);
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip);
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location).

Заголовки IP-адреса и расположения клиента требуются для отображения содержимого с учетом расположения.

Список всех заголовков в запросах и ответах см. в разделе [Заголовки](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

## <a name="the-request"></a>Запрос

Запрос должен содержать все параметры и заголовки для предложений по строкам запроса. Обращение к этому API выполняется каждый раз, когда пользователь вводит новый символ в поле поиска. Полнота строки запроса влияет на релевантность предложений, возвращаемых API-интерфейсом. Чем ближе строка запроса к завершению, тем точнее будет список предлагаемых условий для этого запроса. Например, возвращаемые API-интерфейсом предложения по строке *s* будут, скорее всего, менее значимыми, чем результаты для строки *sailing dinghies*. 

В следующем примере показано, как отправить запрос, который возвращает предлагаемые строки запроса для слова *sail*.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> Запрос в версии 7 (предварительная версия):

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Если вы впервые вызовете любой из API-интерфейсов Bing, не включайте заголовок идентификатора клиента. Передавайте заголовок с идентификатором клиента только в том случае, если вы уже вызывали API Bing и получили от него идентификатор клиента для конкретного сочетания пользователя и устройства.

Ниже показан пример ответ на предыдущий запрос. Ответ содержит группу предложений Web со списком предложений по поисковому запросу. Каждое из предложений содержит поля `displayText`, `query` и `url`.

Поле `displayText` содержит предлагаемые запросы, которые вы можете поместить в раскрывающийся список для поля поиска. Должны отображаться все предоставленные предложения и строго в указанном порядке.  

Если пользователь выбирает запрос из раскрывающегося списка, используйте строку запроса из поля `query`, чтобы создать новый вызов к [API поиска Bing](../bing-web-search/search-the-web.md) и самостоятельно отобразить полученные результаты. Также вы можете использовать URL-адрес из поля `url`, чтобы переадресовать пользователя на страницу результатов поиска Bing.

```  
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Дополнительная информация

Проверьте, как работает API. Откройте [консоль тестирования API автозаполнения](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

Дополнительные сведения об использовании объектов ответа см. в разделе [Getting suggested search terms](./get-suggested-search-terms.md) (Получение рекомендаций по условиям поиска).
