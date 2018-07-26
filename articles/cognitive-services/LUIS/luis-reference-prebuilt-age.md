---
title: Справочник по предварительно созданным сущностям age в службе LUIS в Azure | Документация Microsoft
titleSuffix: Azure
description: В этой статье содержатся сведения о предварительно созданных сущностях age в Интеллектуальной службе распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: f93acb8bece8c66c3ed7197f1c4530011aec3f29
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237406"
---
# <a name="age-entity"></a>Сущность age
Предварительно созданная сущность age фиксирует значение возраста как в цифровом выражении, так и в виде количества дней, недель, месяцев или лет. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности age, не нужно. Сущность age поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-age"></a>Типы сущностей age
Управление сущностью age выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3)

## <a name="resolution-for-prebuilt-age-entity"></a>Разрешение для предварительно созданной сущности age
В следующем примере показано разрешение сущности **builtin.age**.

```JSON
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8236133
    }
  ],
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о сущностях [currency](luis-reference-prebuilt-currency.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) и [dimension](luis-reference-prebuilt-dimension.md). 