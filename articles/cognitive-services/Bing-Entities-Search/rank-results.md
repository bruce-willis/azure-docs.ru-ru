---
title: Использование ранжирования для отображения ответов | Документация Майкрософт
description: Сведения об использовании ранжирования для отображения ответов, возвращаемых API Bing для поиска сущностей
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/12/2017
ms.author: v-jerkin
ms.openlocfilehash: 53354c0f78419a37e8896bb4d00e0d7aebf32203
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059998"
---
# <a name="using-ranking-to-display-results"></a>Использование ранжирования для отображения ответов  

Каждый ответ поиска сущностей содержит объект [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse), примерно такой же, как в ответе веб-поиска Bing, который описывает требуемый формат отображения результатов поиска. Ответ ранжирования объединяет результаты в содержимое для заголовка, основного поля и боковой панели. Результат для заголовка считается самым важным или самым значимым, и его необходимо отображать первым. Если вы не используете для отображения результатов стандартный формат основного поля и боковой панели, нужно по меньшей мере обеспечить более высокую заметность для содержимого основного поля. 
  
В каждой группе есть массив [Items](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items), который определяет порядок отображения содержимого. Каждый элемент предоставляет два метода для идентификации результатов.  
  
-   `answerType` и `resultIndex`. Поле `answerType` идентифицирует тип ответа (Entity или Place), и `resultIndex` идентифицирует результат в этом ответе (например, обнаруженную сущность). Нумерация индекса начинается с нуля.  
  
-   `value`. Поле `value` содержит идентификатор, который обозначает некоторый ответ или результат в этом ответе. Идентификатор может встречаться только в ответе или только в результате.  
  
Использование идентификатора требует согласования между идентификатором ранжирования и идентификатором определенного ответа или результата. Если объект ответа содержит поле `id`, при отображении объедините все результаты из этого ответа. Например, если объект `Entities` содержит поле `id`, отобразите вместе все содержащиеся в нем статьи. Если объект `Entities` не содержит поле `id`, тогда поле `id` должно включаться во все объекты, и ответ ранжирования будет описывать порядок их расположения по результатам Places.  
  
Процесс использования `answerType` и `resultIndex` включает два шага. Сначала нужно с помощью `answerType` найти ответ, который содержит результаты для отображения. Затем с помощью `resultIndex` вы определяете порядок результатов в этом ответе и создаете отображаемые результаты. (Значение `answerType` содержит имя поля в объекте [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse).) Если вам не нужно отображать вместе все результаты из ответа, элемент ранжирования в ответе не будет содержать поле `resultIndex`.

## <a name="ranking-response-example"></a>Пример ответа ранжирования

Ниже приведен пример элемента [RankingResponse](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Боковая панель с учетом ответа ранжирования отображает две сущности из результатов, имеющие отношение к Джимми Хендриксу.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по поиску сущностей в Bing](tutorial-bing-entities-search-single-page-app.md)
