---
title: Как разбить на страницы список доступных новостных статей | Документация Майкрософт
description: Показано, как разбить на страницы список всех новостных статей, которые может вернуть Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: EA388F72-FA43-493B-967C-9560B3243C62
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 2c90d468536f0864d7deac073667e29e9a54692f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380068"
---
# <a name="paging-news"></a>Разбиение новостей по страницам

Когда вы вызываете API Bing для поиска новостей, Bing возвращает список результатов. Список — это подмножество общего количества результатов, относящихся к запросу. Чтобы получить предполагаемое общее количество доступных результатов, необходимо обратиться к полю [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#news-totalmatches) объекта ответа.  
  
В следующем примере показано поле `totalEstimatedMatches`, содержащееся в результатах поиска новостей.  
  
```  
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Чтобы разбить на страницы список доступных статей, используйте параметры запроса [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#count) и [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#offset).  
  
Параметр `count` задает количество результатов, возвращаемых в ответе. Максимальное количество результатов, которые можно запросить в ответе, равно 100. По умолчанию задано значение 10. Фактическое число полученных результатов может быть меньше запрошенного.

Параметр `offset` задает количество пропускаемых результатов. Значение `offset` начинается с нуля и должно быть меньше (`totalEstimatedMatches` - `count`).  

Если вы хотите отображать 20 статей на странице, необходимо задать для параметра `count` значение 20, а для параметра `offset` — значение 0, чтобы получить первую страницу результатов. Для каждой последующей страницы значение параметра `offset` увеличивается на 20 (например, 20, 40).  
  
В следующем примере показано, как запрашиваются 20 новостных статей с начальным смещением в 40 результатов.  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Если значение по умолчанию `count` подходит для вашей реализации, необходимо указать только параметр запроса `offset`, как показано в следующем примере:  
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
> [!NOTE]
> Разбиение на страницы доступно только для поиска новостей (/news/search) и не применимо к темам, набирающим популярность (/news/trendingtopics), или категориям новостей (/news).