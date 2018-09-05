---
title: Автозаполнение поисковых запросов Bing
titleSuffix: Microsoft Cognitive Services
description: Создайте пару между API Bing для поиска в Интернете и API автозаполнения Bing для предоставления пользователям улучшенного интерфейса поиска.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: df8a57b3136abfacce971f4d01ccb2296dfa784c
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889337"
---
# <a name="autosuggest-bing-search-terms"></a>Автозаполнение поисковых запросов Bing

Если вы предоставили окно поиска, в котором пользователь вводит свой поисковый запрос, используйте [API автозаполнения Bing](../bing-autosuggest/get-suggested-search-terms.md), чтобы оптимизировать работу. API возвращает предложенные строки запроса на основе частичного поиска, как пользовательские типы.

После того, как пользователь введет условие поиска, оно должно закодироваться с использованием URL-адреса перед установкой параметра запроса [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query). Например, если пользователь вводит *парусные шлюпки*, установите для параметра `q` значение `sailing+dinghies` или `sailing%20dinghies`.

Если в условии поиска есть орфографические ошибки, ответ поиска включает объект [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext). Объект показывает исходное написание и исправленное написание, которое Bing использует для поиска.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

При отображении результатов поиска можно использовать эти сведения, чтобы пользователь понял, что вы изменили строку запроса.

![Пример UX контекста запроса](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Дополнительная информация  

* Просмотрите пример [ответов API Bing для поиска в Интернете](search-responses.md).  

## <a name="see-also"></a>См. также  

* [Справка по API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
