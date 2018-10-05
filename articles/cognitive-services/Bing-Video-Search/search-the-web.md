---
title: Что такое API Bing для поиска видео?
titlesuffix: Azure Cognitive Services
description: В этой статье показано, как с помощью API Bing для поиска видео искать видео в Интернете.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: overview
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: cf37db9bffa8b2a54a6327c29ec806e0eefc8c91
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225418"
---
# <a name="what-is-bing-video-search"></a>Что такое API Bing для поиска видео?

API Bing для поиска видео работает по похожему принципу (но не аналогичному), по которому работает служба [Видео Bing](https://www.bing.com/videos). API Bing для поиска видео позволяет отправлять поисковый запрос в Bing и получать список соответствующих видео.

Если вы создаете страницу результатов поиска только для видео, чтобы найти относящиеся к поисковому запросу пользователя видео, вызовите этот API вместо вызова более общего [API Bing для поиска в Интернете](../bing-web-search/search-the-web.md). Если вам нужны видеоролики и другие типы содержимого, такие как веб-страницы, новости и изображения, вызовите API Bing для поиска в Интернете.

## <a name="suggesting--using-search-terms"></a>Заполнение и использование условий поиска

Если вы предоставили окно поиска, в котором пользователь вводит свой поисковый запрос, используйте [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), чтобы оптимизировать работу. API возвращает предложенные строки запроса на основе частичного поиска, как пользовательские типы.

После ввода условия поиска URL-адрес закодирует его перед установкой параметра запроса [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query). Например, если пользователь вводит *парусные шлюпки*, установите для параметра `q` значение `sailing+dinghies` или `sailing%20dinghies`.

## <a name="getting-videos"></a>Получение видео

Чтобы получить видео, связанные с условием поиска пользователя в Интернете, отправьте следующий запрос GET.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Все запросы должны быть выполнены на сервере.

Если вы впервые вызовете любой из интерфейсов API Bing, не включайте заголовок идентификатора клиента. Включите идентификатор клиента, только если вы ранее вызывали API Bing, а Bing возвратил идентификатор клиента для комбинации пользователей и устройств.

Чтобы получить видео с определенного домена, используйте оператор запроса [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Ответ типа [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) содержит список видео, которые поиск Bing посчитал соответствующими запросу. Каждый объект [Video](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) в списке содержит URL-адрес видео, его продолжительность, размеры и формат кодировки среди других атрибутов. Объект видео также содержит URL-адрес эскиза видео и его размеры.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYW...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : 
            {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D569...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y62...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : 
            {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E113CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        ...
    ],
    "queryExpansions" : [...],
    "nextOffsetAddCount" : 0,
    "pivotSuggestions" : [...]
}
```

Можно отобразить коллаж из всех эскизов видео или подмножество эскизов. Если вы отобразите подмножество, предоставьте пользователю возможность просмотра оставшихся видеороликов. Видео должны отображаться в порядке, указанном в ответе. Сведения об изменении размера эскизов см. в [этой статье](./resize-and-crop-thumbnails.md). 

Когда пользователь наводит указатель на эскиз, можно использовать [motionThumbnailUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-motionthumbnailurl) для воспроизведения видео в режиме эскиза. Обязательно назначьте видеоролику эскиз при его отображении.

<!-- Removing until the images can be sanitized.
![Motion thumbnail of a video](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Когда пользователь выбирает эскиз, доступны следующие параметры просмотра видео:

- [hostPageUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-hostpageurl) для просмотра видео на исходном веб-сайте (например, YouTube);
- [webSearchUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-websearchurl) для просмотра видео в браузере видео Bing;
- [embdedHtml](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-embedhtml) для внедрения видео в собственный интерфейс. 

Обязательно укажите издателя и автора для атрибуции видео во время его воспроизведения.

Подробнее об использовании [videoId](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video-videoid) для получения полезных сведений о видео см. в разделе [Получение полезных сведений о видео](./video-insights.md).

## <a name="filtering-videos"></a>Фильтрация видео

По умолчанию API для поиска видео возвращает все видеоролики, относящиеся к запросу. Если вам нужны только бесплатные видеоролики или длиной менее пяти минут, используйте следующие параметры запроса фильтра:

- [pricing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#pricing)&mdash; — фильтрация видео по ценам (например, бесплатные видео или за которые нужно заплатить);
- [resolution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#resolution)&mdash; — фильтрация видео по разрешению (например, видео с разрешением 720p или выше);
- [videoLength ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videolength)&mdash; — фильтрация видео по длине (например, видео длиной менее пяти минут);
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#freshness)&mdash; — фильтрация видео по сроку существования (например, видеоролики, обнаруженные Bing на прошлой неделе).

Чтобы получить видео из определенного домена, добавьте оператор запроса [site:](http://msdn.microsoft.com/library/ff795613.aspx) в строку запроса.

> [!NOTE]
> Если вы используете оператор запроса `site:`, в зависимости от запроса есть вероятность, что ответ включает материалы для взрослых независимо от параметра [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#safesearch). Вы должны использовать `site:`, только если вам известно о содержимом на сайте, и ваш сценарий поддерживает возможность использования содержимого для взрослых.

В следующем примере показано, как получить бесплатные видео с ContosoSailing.com с разрешением 720p или выше, которые Bing обнаружил в прошлом месяце.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies+site:contososailing.com&pricing=free&freshness=month&resolution=720p&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

## <a name="expanding-the-query"></a>Расширение запроса

Если Bing может расширить запрос, чтобы сузить исходный поисковый запрос, объект [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) будет содержать поле `queryExpansions`. Например, если запрос был *очистка водостоков*, расширенные запросы могут быть такими: **средства** для очистки водостоков, очистка водостоков **с земли**, **машина** для очистки водостоков и **легкая** очистка водостока.

В следующем примере показаны расширенные запросы для *очистки водостоков*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC5...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 4,
    "queryExpansions" : [
        {
            "text" : "Gutter Cleaning Tools",
            "displayText" : "Tools",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FB....",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Gutter..."
            }
        },
        ...
    ]
    "pivotSuggestions" : [...],
}
```

Поле `queryExpansions` содержит список объектов [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj). Поле `text` содержит расширенный запрос, а `displayText` — условие расширения. Вы можете использовать поля текста и эскиза, чтобы отображать строки расширенных запросов для пользователя на случай, если он ищет именно их. Создайте эскиз и текст с помощью URL-адреса `webSearchUrl` или `searchLink`. Используйте `webSearchUrl`, чтобы отправить пользователя в результаты поиска Bing, или `searchLink`, если вы предоставите свою собственную страницу результатов.

## <a name="pivoting-the-query"></a>Сведение запроса

Если Bing может сегментировать исходный поисковый запрос, объект [Videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) содержит поле `pivotSuggestions`. Например, если исходный запрос был *очистка водостоков*, Bing может сегментировать запрос на *очистка* и *водостоки*.

В следующем примере показаны сводные предложения для *очистки водостоков*.

```json
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC...",
    "totalEstimatedMatches" : 1000,
    "value" : [...],
    "nextOffsetAddCount" : 0,
    "queryExpansions" : [...],
    "pivotSuggestions" : [
        {
            "pivot" : "cleaning",
            "suggestions" : [
                {
                    "text" : "Gutter Repair",
                    "displayText" : "Repair",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5\/videos...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=Gutter..."
                    }
                },
                ...
            ]
        },
        {
            "pivot" : "gutters",
            "suggestions" : [
                {
                    "text" : "Window Cleaning",
                    "displayText" : "Window",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=B52FBC59...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v5...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=Window..."
                    }
                },
                ...
            ]
        }
    ]
}
```

Для каждой сводки ответ содержит список объектов [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#query_obj), содержащих предлагаемые запросы. Поле `text` содержит предлагаемый запрос, а `displayText` — условие, заменяющее сводку в исходном запросе. Например, очистка окна.

Вы можете использовать поля `text` и `thumbnail`, чтобы отображать строки расширенных запросов для пользователя, если они ищут именно такие строки. Создайте эскиз и текст с помощью URL-адреса `webSearchUrl` или `searchLink`. Используйте `webSearchUrl`, чтобы отправить пользователя в результаты поиска Bing, или `searchLink`, если вы предоставите свою собственную страницу результатов.

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как быстро создать первый запрос, см. в [этой статье](./quick-start.md).

Чтобы получить ключ подписки, см. раздел [Ключи подписки](https://azure.microsoft.com/try/cognitive-services/?api=bing-video-search-api).

Ознакомьтесь со статьей [Video Search API v7 reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) (Руководство по API Bing для поиска видео версии 7). Руководство содержит список конечных точек, заголовков и параметров запроса, которые будут использоваться для запроса результатов поиска. Оно также содержит определения объектов ответа. 

Дополнительные сведения о том, как улучшить работу пользователя в окне поиска, см. в статье [What is Bing Autosuggest?](../bing-autosuggest/get-suggested-search-terms.md) (Что такое Автозаполнение Bing?). Когда пользователь вводит условие поиска, вы можете вызвать этот API для получения релевантных условий запроса, которые использовались другими.

Обязательно прочтите [эту статью](./useanddisplayrequirements.md), чтобы не нарушать какие-либо правила использования результатов поиска.

Когда вы вызываете API для поиска видео, Bing возвращает список результатов. Список — это подмножество общего количества результатов, относящихся к запросу. Поле ответа `totalEstimatedMatches` содержит оценку количества видеороликов, доступных для просмотра. Подробнее о том, как просматривать оставшиеся видео, см. в разделе [Разбиение списка видео по страницам](./paging-videos.md).

Подробнее о получение полезных сведений о видео см. в разделе [Получение полезных сведений о видео](./video-insights.md).

Дополнительные сведения о получении видео, набирающих популярность, см. в разделе [Получение видео, набирающих популярность](./trending-videos.md).