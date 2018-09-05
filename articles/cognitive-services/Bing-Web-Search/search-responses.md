---
title: Отклики API Bing для поиска в Интернете | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Сведения о типах ответов и откликах, которые предоставляет API Bing для поиска в Интернете.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: 13e9792f3d5765047dabb4cdef59e85a47a69aba
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889344"
---
# <a name="bing-web-search-responses"></a>Отклики службы "Поиск в Интернете Bing"  

При отправке поискового запроса службе "Поиск в Интернете Bing" она возвращает объект [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse) в тексте отклика. Объект содержит поле для каждого ответа, который система Bing посчитала соответствующим запросу. Этот пример иллюстрирует объект отклика, если система Bing вернула все ответы:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Как правило, служба "Поиск в Интернете Bing" возвращает только некоторые из ответов. Например, если отправить запрос с терминами *плавание на ялах*, отклик может включать ответы `webPages`, `images` и `rankingResponse`. Если вы не отфильтровали веб-страницы с помощью объекта [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#responsefilter), отклик всегда включает ответы `webpages` и `rankingResponse`.

## <a name="webpages-answer"></a>Ответ с веб-страницами

Ответ [webPages](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer) содержит список ссылок на веб-страницы, которые служба "Поиск в Интернете Bing" посчитала соответствующими запросу. Каждый объект [веб-страницы](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webpage) в списке содержит название, URL-адрес, отображаемый URL-адрес, краткое описание содержимого и дату обнаружения контента системой Bing.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Используйте свойства `name` и `url`, чтобы создать гиперссылку, направляющую пользователя на веб-страницу.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Ответ при поиске изображений

Ответ [images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) содержит список изображений, которые система Bing посчитала соответствующими запросу. Каждый объект [изображения](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) в списке содержит URL-адрес изображения, его размер, ширину и высоту, а также формат кодирования. Объект изображения также содержит URL-адрес эскиза изображения и его размеры.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

В зависимости от устройства пользователя, обычно отображаются некоторые из эскизов с возможностью просмотреть остальные изображения.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Вы также можете развернуть эскиз, когда пользователь наводит курсор на него. Обязательно описывайте изображение, если оно будет раскрываться. Например, можно извлекать узел по адресу `hostPageDisplayUrl` и показывать его под изображением. Сведения об изменении размера эскизов см. в [этой статье](./resize-and-crop-thumbnails.md).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Когда пользователь нажимает на эскиз, используйте адрес `webSearchUrl`, чтобы направить пользователя на страницу результатов поиска Bing, которая содержит коллаж изображений.

Дополнительные сведения об этом ответе и изображениях см. в статье [Image Search API](../bing-image-search/search-the-web.md) (API для поиска изображений).

## <a name="related-searches-answer"></a>Ответ со связанными поисковыми запросами

Ответ [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse-relatedsearches) содержит список наиболее популярных связанных запросов, отправленных другими пользователями. Каждый объект [запроса](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query_obj) в списке включает строку запроса (`text`), строку запроса с символами выделения совпадений (`displayText`) и URL-адрес (`webSearchUrl`) страницы результатов поиска Bing по этому запросу.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Используйте строку запроса `displayText` и URL-адрес `webSearchUrl`, чтобы создать гиперссылку, направляющую пользователя на страницу результатов поиска Bing по связанному запросу. Вы также можете использовать строку запроса `text` в собственном API для поиска в Интернете и самостоятельно отображать результаты.

Сведения о том, как обрабатывать маркеры выделения в `displayText`, см. в статье [Использование маркеров оформления для выделения текста](./hit-highlighting.md).

Ниже показан пример использования связанных запросов на сайте Bing.com.

![Пример связанных поисковых запросов в Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Ответ с видео

Ответ [videos](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos) содержит список видео, которые система Bing посчитала соответствующими запросу. Каждый объект [видео](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#video) в списке содержит URL-адрес видео, его продолжительность, размеры и формат кодировки. Объект видео также содержит URL-адрес эскиза видео и его размеры.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

В зависимости от устройства пользователя, обычно отображаются только некоторые видеоролики с возможностью просмотреть остальные. Отображается эскиз видео с его продолжительностью, описанием (названием) и принадлежностью (издателем).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

Когда пользователь наводит указатель на эскиз, вы можете использовать адрес `motionThumbnailUrl` для воспроизведения видео в режиме эскиза. Обязательно назначьте видеоролику эскиз при его отображении.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Когда пользователь выбирает эскиз, доступны следующие параметры просмотра видео:

- `hostPageUrl` для просмотра видео на исходном веб-сайте (например, YouTube);
- `webSearchUrl` для просмотра видео в браузере Видео Bing;
- `embedHtml` для внедрения видео в собственный интерфейс.

Дополнительные сведения об этом ответе и видео см. в статье [API для поиска видео](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Ответ с новостными статьями

Ответ [news](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news) содержит список новостных статей, которые система Bing посчитала соответствующими запросу. Каждый объект [новостной статьи](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#newsarticle) в списке содержит название, описание и URL-адрес статьи на исходном веб-сайте. Если статья содержит изображение, то объект включает эскиз этого изображения.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

В зависимости от устройства пользователя, обычно отображаются некоторые из новостных статей с возможностью просмотреть остальные. Используйте свойства `name` и `url`, чтобы создать гиперссылку, направляющую пользователя к новостной статье на исходном сайте. Если статья содержит изображение, сделайте его интерактивным с помощью свойства `url`. Обязательно используйте свойство `provider`, чтобы указать источник статьи.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Дополнительные сведения об этом ответе и новостных статьях см. в статье [API для поиска новостей](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Ответ с вычислениями

Если пользователь введет математическое выражение или запрос на преобразование единиц измерения, то отклик может содержать ответ [Computation](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#computation). Ответ `computation` содержит нормализованное выражение и его результат.

Запрос на преобразование единиц измерения преобразует одни единицы в другие. Примеры: *Сколько футов в 10 метрах?*, *Сколько столовых ложек в 1/4 чашки?*

Ниже показан ответ `computation` на запрос *Сколько футов в 10 метрах?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Ниже показаны примеры математических запросов и соответствующих ответов `computation`.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Математическое выражение может содержать следующие символы:

|Знак|ОПИСАНИЕ|
|------------|-----------------|
|+|Сложение|
|-|Вычитание|
|/|Деление|
|*|Умножение|
|^|Возведение в степень|
|!|Факториал|
|.|Decimal|
|()|Группирование по приоритету|
|[]|Функция|

Математическое выражение может содержать следующие константы:

|Знак|ОПИСАНИЕ|
|------------|-----------------|
|Pi|3,14159...|
|Degree|Градус|
|i|Мнимое число|
|e|e, 2,71828...|
|GoldenRatio|Золотое сечение: 1,61803...|

Математическое выражение может содержать следующие функции:

|Знак|ОПИСАНИЕ|
|------------|-----------------|
|Sqrt|Квадратный корень|
|Sin[x], Cos[x], Tan[x]<br />Csc[x], Sec[x], Cot[x]|Тригонометрические функции (с аргументами в радианах)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Обратные тригонометрические функции (возвращающие результаты в радианах)|
|Exp[x], E^x|Экспоненциальная функция|
|Log[x]|Натуральный логарифм|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Гиперболические функции|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Обратные гиперболические функции|

Математические выражения, которые содержат переменные (например, 4x+6=18, где x — переменная) не поддерживаются.

## <a name="timezone-answer"></a>Ответ TimeZone

Если пользователь вводит запрос времени или даты, отклик может содержать ответ [TimeZone](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#timezone). Этот ответ поддерживает как явные, так и неявные запросы. Неявный запрос, например *Который час?*, возвращает местное время, соответствующее местоположению пользователя. Явный запрос, например *Который час в Сиэтле?*, возвращает местное время в Сиэтле, штат Вашингтон.

В ответе `timeZone` указываются название региона, текущие дата и время в формате UTC в указанном местоположении, а также смещение от UTC. Если границы географического региона находятся в нескольких временных поясах, то ответ содержит текущие дату и время в формате UTC для всех часовых поясов в рамках этой границы. Например, так как штат Флорида находится в двух часовых поясах, ответ содержит локальные дату и время обоих часовых поясов.  

Если запрашивается время штата или страны, Bing определяет основной город в соответствующих географических границах и возвращает его в поле `primaryCityTime`. Если в границах находится несколько часовых поясов, остальные часовые пояса возвращаются в поле `otherCityTimes`.

Ниже показаны примеры запросов, возвращающих ответ `timeZone`.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>Ответ SpellSuggestion

Если система Bing устанавливает, что пользователь мог искать что-то другое, отклик включает объект [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#spellsuggestions). Например, если пользователь вводит запрос *карлос пен*, то Bing может установить, что пользователь наверняка искал имя "Карлос Пена" (на основании предыдущих поисковых запросов со словами *карлос пен*). Ниже представлен пример отклика с исправлением правописания.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

Ниже показано, как Bing использует предложения правописания.

![Пример предложения правописания Bing](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Дополнительная информация  

* Ознакомьтесь с документацией по [регулированию запросов](throttling-requests.md).  

## <a name="see-also"></a>См. также  

* [Справка по API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
