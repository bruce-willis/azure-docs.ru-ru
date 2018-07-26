---
title: Справочник по предварительно созданным сущностям валюты в LUIS в Azure | Документация Майкрософт
titleSuffix: Azure
description: В этой статье содержатся сведения о предварительно созданных сущностях валюты в службе Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: b383e21f870d15818c540b79a9a56c1dd65fa342
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39236828"
---
# <a name="currency-entity"></a>Сущность валюты
Предварительно созданная сущность валюты обнаруживает валюту во многих деноминациях и странах, независимо от языка и региональных параметров LUIS. Так как эта сущность уже обучена, добавлять в назначение приложения примеры фраз, содержащие валюты, не нужно. Сущность валюты поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-currency"></a>Типы валюты
Управление валютой выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26)

## <a name="resolution-for-currency-entity"></a>Разрешение для сущности валюты
В следующем примере показано разрешение сущности **builtin.currency**.

```JSON
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [datetimeV2](luis-reference-prebuilt-datetimev2.md), [измерение](luis-reference-prebuilt-dimension.md) и [электронная почта](luis-reference-prebuilt-email.md). 