---
title: 'Пользовательский поиск Bing: разбиение на страницы списка доступных веб-страниц | Документация Майкрософт'
description: Показано, как разбить на страницы список всех веб-страниц, которые может вернуть Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: f2f545a5a9195fc65515ea716f277723600cbb78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380032"
---
# <a name="paging-webpages"></a>Разбиение на страницы списка веб-страниц 

Когда вы вызываете API Bing для пользовательского поиска, Bing возвращает список результатов. Список — это подмножество общего количества результатов, относящихся к запросу. Чтобы получить предполагаемое общее количество доступных результатов, необходимо обратиться к полю [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#totalestimatedmatches) объекта ответа.  
  
В следующем примере показано поле `totalEstimatedMatches`, содержащееся в результатах поиска в Интернете.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Чтобы разбить на страницы список доступных веб-страниц, используйте параметры запроса [count](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#count) и [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#offset).  
  
Параметр `count` задает количество результатов, возвращаемых в ответе. Максимальное количество результатов, которые можно запросить в ответе, равно 50. По умолчанию задано значение 10. Фактическое число полученных результатов может быть меньше запрошенного.

Параметр `offset` задает количество пропускаемых результатов. Значение `offset` начинается с нуля и должно быть меньше (`totalEstimatedMatches` - `count`).  
  
Если вы хотите отображать 15 веб-страниц на странице, необходимо задать для параметра `count` значение 15, а для параметра `offset` — значение 0, чтобы получить первую страницу результатов. Для каждой последующей страницы значение параметра `offset` увеличивается на 15 (например, 15, 30).  
  
В следующем примере показано, как запрашиваются 15 веб-страниц с начальным смещением в 45 результатов.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Если значение по умолчанию `count` подходит для вашей реализации, необходимо указать только параметр запроса `offset`.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

