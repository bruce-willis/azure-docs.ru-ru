---
title: Как разбить на страницы список доступных видео | Документация Майкрософт
description: Показано, как разбить на страницы список всех видео, которые может вернуть Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 910A485F-BCF3-42B9-958D-DD48BDEDA965
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 00476825eb3fc1008c3f2172b591d8b7a2f35884
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380045"
---
# <a name="paging-videos"></a>Разбиение списка видео по страницам

Когда вы вызываете API для поиска видео, Bing возвращает список результатов. Список — это подмножество общего количества результатов, относящихся к запросу. Чтобы получить предполагаемое общее количество доступных результатов, необходимо обратиться к полю [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-totalestimatedmatches) объекта ответа.  
  
В следующем примере показано поле `totalEstimatedMatches`, содержащееся в результатах поиска видео.  
  
```  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
Чтобы разбить на страницы список доступных видео, используйте параметры запроса [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#count) и [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#offset).  
  
Параметр `count` задает количество результатов, возвращаемых в ответе. Максимальное количество результатов, которые можно запросить в ответе, равно 105. Значение по умолчанию — 35. Фактическое число полученных результатов может быть меньше запрошенного.

Параметр `offset` задает количество пропускаемых результатов. Значение `offset` начинается с нуля и должно быть меньше (`totalEstimatedMatches` - `count`).  
  
Если вы хотите отображать 20 видео на странице, необходимо задать для параметра `count` значение 20, а для параметра `offset` — значение 0, чтобы получить первую страницу результатов. Для каждой последующей страницы значение параметра `offset` увеличивается на 20 (например, 20, 40).  

В следующем примере показано, как запрашиваются 20 видео с начальным смещением в 40 результатов.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Если значение по умолчанию `count` подходит для вашей реализации, необходимо указать только параметр запроса `offset`.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Как правило, для разбиения списка на страницы по 35 видео на каждой странице для параметра `offset` необходимо при первом запросе задать значение 0, а затем увеличивать `offset` на 35 при каждом последующем запросе. Но некоторые результаты из предыдущего ответа могут повторяться в последующем ответе. Например, первые два видео в ответе могут совпадать с последними двумя видео из предыдущего ответа.

Чтобы исключить повторяющиеся результаты, используйте поле [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference#videos-nextoffset) объекта `Videos`.

Например, для разбиения по 30 видео на страницу при первом запросе необходимо задать для параметра `count` значение 30, а для `offset` — 0. При следующем запросе задайте для параметра `offset` значение, равное `nextOffset`.


> [!NOTE]
> Разбиение на страницы доступно только для поиска видео (/videos/search) и не применимо к аналитическим сведениям о видео (/videos/details) или видео, набирающим популярность (/videos/trending).