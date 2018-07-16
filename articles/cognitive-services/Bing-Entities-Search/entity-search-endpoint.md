---
title: Конечные точки для поиска сущностей | Документация Майкрософт
description: Сводные сведения о конечной точке API для поиска сущностей.
services: cognitive-services
author: v-jaswel
manager: kaiq
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/04/2017
ms.author: v-jaswel
ms.openlocfilehash: 3d5da30102712baf399c245cc7678eeddbce796a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380037"
---
# <a name="entity-search-endpoints"></a>Конечные точки для поиска сущностей
**API для поиска сущностей** включает одну конечную точку.

## <a name="endpoint"></a>Конечная точка
Чтобы запросить результаты поиска сущностей, отправьте запрос в следующую конечную точку. Для определения дополнительных спецификаций используйте заголовки и параметры URL-адреса.

Конечная точка `GET`: 
``` 
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Требуются следующие параметры URL-адреса:
- mkt. Рынок, для которого будут отображаться результаты. 
- q. Запрос на поиск сущностей.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Краткие руководства по поиску сущностей Bing](quickstarts/csharp.md)

## <a name="see-also"></a>См. также 

[Общие сведения о поиске сущностей Bing](search-the-web.md )
[Справочник по API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
