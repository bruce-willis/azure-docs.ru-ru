---
title: 'API пользовательского поиска Bing: получение пользовательских предложений автозаполнения | Документация Майкрософт'
description: Инструкции для получения пользовательских предложений автозаполнения
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.technology: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 73059038018602f7aa76377bf7c98d4658576576
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380997"
---
# <a name="get-custom-suggestions"></a>Получение пользовательских предложений
Перед отправкой запросов в службу пользовательского поиска Bing следует вызвать API пользовательского автозаполнения для создания предложений условий поиска и улучшения процесса поиска. API пользовательского автозаполнения возвращает список предлагаемых запросов на основе частично введенной пользователем строки запроса. Все релевантные условия из пользовательского запроса, указанные вами, будут предшествовать предложениям, созданным функцией автозаполнения. Дополнительные сведения см. в статье об [определении пользовательских предложений поиска](define-custom-suggestions.md).

## <a name="endpoint"></a>Конечная точка
Чтобы получить предлагаемые запросы с помощью API пользовательского поиска Bing, отправьте запрос `GET` на следующую конечную точку.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

## <a name="response-json"></a>Ответ JSON
Ответ содержит список объектов SearchAction с предлагаемыми условиями поиска.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Каждое из предложений содержит поля `displayText` и `query`. Поле `displayText` содержит предлагаемые запросы, которые вы можете поместить в раскрывающийся список для поля поиска.

Если пользователь выберет предложенный запрос из раскрывающегося списка, следует использовать условие запроса из поля `query` при вызове [API пользовательского поиска Bing](overview.md).

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Дополнительная информация

- [Вызов API пользовательского поиска](./search-your-custom-view.md)
- [Настройка размещенного пользовательского интерфейса](./hosted-ui.md)