---
title: Справочник по предварительно созданным сущностям ordinal в службе LUIS в Azure | Документация Microsoft
titleSuffix: Azure
description: В этой статье содержатся сведения о предварительно созданных сущностях ordinal в Интеллектуальной службе распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 216114ec521e2065cb13cd39b4086f50ec81ba56
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236760"
---
# <a name="ordinal-entity"></a>Сущность ordinal
Порядковый номер — это числовое представление объекта внутри набора: `first`, `second`, `third`. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности ordinal, не нужно. Сущность ordinal поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Типы сущностей ordinal
Управление сущностью ordinal выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45)

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Разрешение для предварительно созданной сущности ordinal
В следующем примере показано разрешение сущности **builtin.ordinal**.

```JSON
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-phonenumber.md) и [temperature](luis-reference-prebuilt-temperature.md). 