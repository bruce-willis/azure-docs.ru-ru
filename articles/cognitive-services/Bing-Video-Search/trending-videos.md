---
title: Поиск набирающих популярность видео в Интернете — Поиск видео Bing
titlesuffix: Azure Cognitive Services
description: В этой статье показано, как с помощью API Bing для поиска видео искать в Интернете видео, набирающие популярность.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: conceptual
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: 8a6ccc9ea8cf9468d7638360c9db8131bc6dc5be
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222052"
---
# <a name="get-trending-videos"></a>Получение видео, набирающих популярность  

Чтобы получить видео, набирающие сегодня популярность, отправьте следующий запрос GET:  
  
```
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/trending?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

Поиск видео, набирающих популярность, поддерживается для следующих рынков:  
 
-   en-AU (английский, Австралия)  
-   en-CA (английский, Канада)  
-   en-GB (английский, Великобритания)  
-   en-ID (английский, Индонезия)  
-   en-IE (английский, Ирландия)  
-   en-IN (английский, Индия)  
-   en-NZ (английский, Новая Зеландия)  
-   en-PH (английский, Филиппины)  
-   en-SG (английский, Сингапур)  
-   en-US (английский, США)  
-   en-WW (английский, объединенный глобальный код)  
-   en-ZA (английский, Южная Африка)  
-   zh-CN (китайский, Китай)

  
В следующем примере показан ответ, который содержит видео, набирающие популярность.  

```  
{  
    "_type" : "TrendingVideos",  
    "bannerTiles" : [
        {  
            "query" : {  
                "text" : "Hello - Smith",  
                "displayText" : "Hello - Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
            },  
            "image" : {  
                "description" : "Image from: contosowallpapers.com",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=RsA%2fdPlTmx4zS...",  
                "headLine" : "\"Hello\" is a song by...",  
                "contentUrl" : "http:\/\/www.contosowallpapers.com\/wp-content..."  
            }  
        },  
        . . .  
    ],  
    "categories" : [
        {  
            "title" : "Music videos",  
            "subcategories" : [
                {  
                    "tiles" : [
                        {  
                            "query" : {  
                                "text" : "Song Title - Artist Name",  
                                "displayText" : "Song Title - Artist Name",  
                                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3E8F5..."
                            },  
                            "image" : {  
                                "description" : "Image from: contoso.com",  
                                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=...",  
                                "contentUrl" : "http:\/\/images6.contoso.com\/image..."  
                            }  
                        },  
                        . . .  
                    ],
                    "title" : "Top "  
                },
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        {
            "title" : "Viral videos",
            "subcategories" : [
                {
                    "tiles" : [...],
                    "title" : "Trending"
                },
                . . .
            ],  
        },
        . . .  
    ]  
}  
  
```  
Этот ответ содержит список видео, разделенный на категории и подкатегории. Например, если список категорий содержал категорию "Music Videos" (Музыкальные видео) и одна из ее подкатегорий называлась "Top" (Лучшие), то можно создать категорию "Top Music Videos" (Лучшие музыкальные видео) и сделать ее доступной для пользователей. Затем можно использовать поля `thumbnailUrl`, `displayText` и `webSearchUrl`, чтобы создать гиперактивный элемент под каждой категорией (например, "Top Music Videos"). Когда пользователь щелкнет такой гиперактивный элемент, откроется браузер Bing, в котором и будет воспроизведено видео.

Ответ также содержит баннеры с видео, которые являются самыми популярными видео. Баннеры с видео могут поступать из одной или нескольких категорий.  
  
