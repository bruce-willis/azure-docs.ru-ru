---
title: Отправка запросов к API Bing для поиска изображений | Документация Майкрософт
description: Дополнительные сведения об отправке и настройке запросов поиска в API Bing для поиска изображений.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082587"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>Отправка запросов в API Bing для поиска изображений

API Bing для поиска изображений предоставляет примерно такие же возможности, как просмотр сведений об изображении на Bing.com/images. API позволяет отправлять поисковый запрос пользователя в Bing и получать в ответ список соответствующих изображений.

## <a name="using-and-suggesting-search-terms"></a>Использование и заполнение условий поиска

После ввода поискового запроса он будет закодирован в URL-адресе перед параметром запроса [**q**](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query). Например, если пользователь вводит *sailing dinghies*, установите для параметра `q` значение `sailing+dinghies` или `sailing%20dinghies`.

Если в приложении предусмотрено поле для ввода условий поиска, то для упрощения работы можно использовать [API автозаполнения Bing](../../bing-autosuggest/get-suggested-search-terms.md), отображая предлагаемые условия поиска в режиме реального времени. API возвращает предложенные строки запроса на основе частичных условий поиска и службы Azure Сognitive Services.

## <a name="pivoting-the-query"></a>Сведение запроса

Если Bing может делить на части первоначальные условия поиска, возвращенный объект [Изображения](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) будет содержать параметр `pivotSuggestions`, который может быть отображен для пользователя как необязательный поисковый запрос. Например, если исходный запрос был *Microsoft Surface*, Bing может разделить запрос на *Microsoft* и *Surface* и предложить сведения для каждого из них. Они могут отображаться как дополнительные условия запроса пользователя.

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Дополнительная информация

Если вы еще ни разу не использовали API Bing для поиска изображений, ознакомьтесь со следующей [статьей](../quickstarts/csharp.md). Если вы ищете что-нибудь посложнее, ознакомьтесь с руководством для создания [одностраничного веб-приложения](../tutorial-bing-image-search-single-page-app.md).
