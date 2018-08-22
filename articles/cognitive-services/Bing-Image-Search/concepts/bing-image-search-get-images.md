---
title: Получение изображений из Интернета с помощью API Bing для поиска изображений | Документация Майкрософт
description: Используйте API Bing для поиска изображений и получайте соответствующие изображения из Интернета.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: c379cc2dfbe53f83e4d79500cd947879407c8d55
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082584"
---
# <a name="getting-images-from-the-web-with-the-bing-image-search-api"></a>Получение изображений из Интернета с помощью API Bing для поиска изображений

С помощью REST API Bing для поиска изображений можно получать изображения из Интернета, которые связаны с условием пользовательского поиска путем отправки следующего GET-запроса.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * Все запросы нужно выполнить на сервере, а не в клиенте.
> * Если вы впервые вызываете любой из поисковых API-интерфейсов Bing, не включайте заголовок идентификатора клиента. Включите идентификатор клиента только в том случае, если вы ранее вызывали API Bing, который возвратил идентификатор клиента для определенного сочетания пользователя и устройства.
> * Изображения должны отображаться в порядке, указанном в ответе.

## <a name="getting-images-from-a-specific-web-domain"></a>Получение изображений из определенного веб-домена

Чтобы получить изображения с определенного домена, используйте оператор запроса [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Ответы на запросы с помощью оператора `site:` могут относиться к содержимому для взрослых вне зависимости от параметров [SafeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch). Используйте `site:` только тогда, когда вам известно содержимое домена.

В следующем примере показано, как получить небольшие изображения с сайта ContosoSailing.com, обнаруженные Bing на прошлой неделе.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filtering-images"></a>Фильтрация изображений

 API для поиска изображений по умолчанию возвратит все изображения, относящиеся к запросу. Если необходимо использовать фильтры для изображений, которые возвращает Bing (например, только изображения с прозрачным фоном или определенного размера), можно использовать следующие параметры запроса:

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) — фильтрация изображений по формату (например, стандартные или широкоэкранные изображения).
* [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) — фильтрация изображений по доминирующему цвету или черно-белой палитре.
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) — фильтрация изображений по сроку существования (например, изображения, обнаруженные Bing на прошлой неделе).
* [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) — фильтрация изображений по ширине и высоте.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) — фильтровать изображения по содержанию (например, изображения, на которых только лицо человека).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) — фильтрация изображений по типу (например, клип, GIF с анимацией или с прозрачным фоном).
* [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) — фильтрация изображений по типу лицензии, связанной с сайтом.
* [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) — фильтрация изображений по размеру, например маленькие изображения размером до 200x200 пикселей.

Чтобы получить изображения с определенного домена, используйте оператор запроса [site:](http://msdn.microsoft.com/library/ff795613.aspx).

    > [!NOTE]
    > Responses to queries using the `site:` operator may include adult content regardless of the [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) setting. Only use `site:` if you are aware of the content on the domain.

В следующем примере показано, как получить небольшие изображения с сайта ContosoSailing.com, обнаруженные Bing на прошлой неделе.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Формат ответа для API Bing для поиска изображений

Ответное сообщение от Bing содержит [Изображения](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images), список которых определен в Azure Cognitive Services как соответствующий запросу. Каждый объект [Изображения](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) в списке содержит следующие сведения об изображении: URL-адрес, размер, измерения, формат кодирования, URL-адрес эскиза изображения, а также размеры эскизов.

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

Когда вы вызываете API Bing для поиска изображений, Bing возвращает список результатов. Список — это подмножество общего количества результатов, относящихся к запросу. Поле ответа `totalEstimatedMatches` содержит оценку количества изображений, доступных для просмотра. Подробнее о том, как просматривать оставшиеся изображения, см. в статье [Paging results](../paging-images.md) (Разбиение результатов).

## <a name="next-steps"></a>Дополнительная информация

Если вы еще ни разу не использовали API Bing для поиска изображений, ознакомьтесь со следующей [статьей](../quickstarts/csharp.md). Если вы ищете что-нибудь посложнее, ознакомьтесь с руководством для создания [одностраничного веб-приложения](../tutorial-bing-image-search-single-page-app.md).
