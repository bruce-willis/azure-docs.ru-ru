---
title: Что такое API Bing для поиска изображений? | Документация Майкрософт
description: Узнайте, как с помощью API Bing искать изображения в Интернете.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336525"
---
# <a name="what-is-bing-image-search"></a>Что такое API Bing для поиска изображений?

API Bing для поиска изображений предоставляет процедуру, аналогичную [Изображениям Bing](https://www.bing.com/images), позволяя вам отправлять поисковый запрос пользователя в Bing и возвращать список соответствующих изображений.

Если вы создаете страницу результатов поиска только для изображений, чтобы найти изображения, относящиеся к поисковому запросу пользователя, вызовите этот API вместо вызова более общего [API для поиска в Интернете](../bing-web-search/search-the-web.md). Если вам нужны изображения и другие типы содержимого, такие как веб-страницы, новости и видеоролики, вызовите API для поиска в Интернете.

## <a name="suggesting--using-search-terms"></a>Заполнение и использование условий поиска

Если вы предоставили окно поиска, в котором пользователь вводит свой поисковый запрос, используйте [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), чтобы оптимизировать работу. API возвращает предложенные строки запроса на основе частичного поиска, как пользовательские типы.

После ввода условия поиска URL-адрес закодирует его перед установкой параметра запроса [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Например, если пользователь вводит *парусные шлюпки*, установите для параметра `q` значение `sailing+dinghies` или `sailing%20dinghies`.

## <a name="getting-images"></a>Получение изображений

Чтобы получить изображения, связанные с условием поиска пользователя в Интернете, отправьте следующий запрос GET:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Все запросы должны быть выполнены на сервере. Вы не можете делать вызовы из клиента.

Если вы впервые вызовете любой из API-интерфейсов Bing, не включайте заголовок идентификатора клиента. Включите идентификатор клиента, только если вы ранее вызывали API Bing, а Bing возвратил идентификатор клиента для комбинации пользователей и устройств.

Чтобы получить изображения с определенного домена, используйте оператор запроса [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Ответ содержит ответ [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) со списком изображений, которые, по мнению Bing, были релевантны запросу. Каждый объект [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) в списке содержит URL-адрес изображения, его объем, размеры и формат кодировки. Объект изображения также содержит URL-адрес эскиза изображения и его размеры.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Вы можете отобразить коллаж из всех эскизов изображений или подмножество эскизов. Если вы отобразите подмножество, предоставьте пользователю возможность просмотра оставшихся изображений. Вы должны отображать изображения в порядке, указанном в ответе.

Вы также можете развернуть эскиз, когда пользователь наводит курсор на него. Обязательно описывайте изображение, если оно будет раскрываться. Например, извлекая узел из [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) и отображая его под изображением. Сведения об изменении размера эскизов см. в [этой статье](./resize-and-crop-thumbnails.md).

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Если пользователь щелкает эскиз, вы можете использовать [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) для отображения полноразмерного изображения пользователю. Обязательно описывайте изображение.

Если `shoppingSourcesCount` или `recipeSourcesCount` больше нуля, добавьте значки, например тележку для покупок, на эскиз, чтобы указать, что для объекта на изображении существуют покупки или квитанции.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

Чтобы получить представление об изображении, например о веб-страницах, содержащих изображение, или людях, которые были распознаны на изображении, используйте [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Дополнительные сведения см. в статье [Get insights about an image](./image-insights.md) (Получение аналитических сведений об изображении).

## <a name="filtering-images"></a>Фильтрация изображений

 По умолчанию API для поиска изображений возвращает все изображения, относящиеся к запросу. Но если вы ищете только изображения с прозрачным фоном или изображениями определенного размера, вы должны использовать следующие параметры запроса для фильтрации изображений, возвращаемых Bing.  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) — фильтрация изображений по формату (например, стандартные или широкоэкранные изображения).
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) — фильтрация изображений по доминирующему цвету или черно-белой палитре.
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) — фильтрация изображений по сроку существования (например, изображения, обнаруженные Bing на прошлой неделе).
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) — фильтрация изображений по ширине и высоте.
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) — фильтровать изображения по содержанию (например, изображения, на которых только лицо человека).
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) — фильтрация изображений по типу (например, клип, GIF с анимацией или с прозрачным фоном).
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) — фильтрация изображений по типу лицензии, связанной с сайтом.
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) — фильтрация изображений по размеру, например маленькие изображения размером до 200x200 пикселей.

Чтобы получить изображения с определенного домена, используйте оператор запроса [site:](http://msdn.microsoft.com/library/ff795613.aspx).

> [!NOTE]
> Если вы используете оператор `site:`, в зависимости от запроса есть вероятность, что ответ включает содержимое для взрослых независимо от параметра [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch). Вы должны использовать `site:`, только если вам известно о содержимом на сайте, и ваш сценарий поддерживает возможность использования содержимого для взрослых.

В следующем примере показано, как получить небольшие изображения с сайта ContosoSailing.com, обнаруженные Bing на прошлой неделе.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>Сведение запроса

Если Bing может сегментировать исходный поисковый запрос, объект [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) содержит поле `pivotSuggestions`. Например, если исходный запрос был *Microsoft Surface*, Bing может сегментировать запрос на *Microsoft* и *Surface*.  

В следующем примере показаны сводные предложения для *Microsoft Surface*.  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Затем вы можете отобразить пользователю дополнительные условия запроса, если они представляют интерес для него.

Поле `pivotSuggestions` содержит список сегментов (сводок), на которые был разбит исходный запрос. Для каждой сводки ответ содержит список объектов [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj), содержащих предлагаемые запросы. Поле `text` содержит предлагаемый запрос, а `displayText` — условие, заменяющее сводку в исходном запросе. Например, дата выпуска Surface.

Вы можете использовать поля `text` и `thumbnail`, чтобы отображать строки сводных запросов для пользователя, если они ищут именно такие строки. Создайте эскиз и текст с помощью URL-адреса `webSearchUrl` или `searchLink`. Используйте `webSearchUrl`, чтобы отправить пользователя в результаты поиска Bing, или `searchLink`, если вы предоставите свою собственную страницу результатов.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>Расширение запроса

Если Bing может расширить запрос, чтобы сузить исходный поисковый запрос, объект [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) будет содержать поле `queryExpansions`. Например, в случае запроса *Microsoft Surface* могут использоваться такие расширенные запросы: Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** и Microsoft Surface **Hub**.

В следующем примере показаны расширенные запросы для *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Поле `queryExpansions` содержит список объектов [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj). Поле `text` содержит расширенный запрос, а `displayText` — условие расширения. Вы можете использовать поля `text` и `thumbnail`, чтобы отображать строки расширенных запросов для пользователя, если они ищут именно такие строки. Создайте эскиз и текст с помощью URL-адреса `webSearchUrl` или `searchLink`. Используйте `webSearchUrl`, чтобы отправить пользователя в результаты поиска Bing, или `searchLink`, если вы предоставите свою собственную страницу результатов.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как быстро создать первый запрос, см. в статье [Call and response: your first Bing Image Search query in C#](quickstarts/csharp.md) (Вызов и ответ: ваш первый поисковой запрос в службе изображений Bing на C#).

Ознакомьтесь со статьей [Image Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference) (Руководство по API Bing для поиска изображений версии 7). Руководство содержит список конечных точек, заголовков и параметров запроса, которые будут использоваться для запроса результатов поиска. Оно также содержит определения объектов ответа.

Дополнительные сведения о том, как улучшить работу пользователя в окне поиска, см. в статье [What is Bing Autosuggest?](../bing-autosuggest/get-suggested-search-terms.md) (Что такое Автозаполнение Bing?). Когда пользователь вводит условие поиска, вы можете вызвать этот API для получения релевантных условий запроса, которые использовались другими.

Обязательно прочтите [эту статью](./useanddisplayrequirements.md), чтобы не нарушать какие-либо правила использования результатов поиска.

Когда вы вызываете API Bing для поиска изображений, Bing возвращает список результатов. Список — это подмножество общего количества результатов, относящихся к запросу. Поле ответа `totalEstimatedMatches` содержит оценку количества изображений, доступных для просмотра. Подробнее о том, как просматривать оставшиеся изображения, см. в статье [Paging results](./paging-images.md) (Разбиение результатов).