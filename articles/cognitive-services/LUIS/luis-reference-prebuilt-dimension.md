---
title: Справочник по предварительно созданным сущностям dimension в службе LUIS в Azure | Документация Microsoft
titleSuffix: Azure
description: В этой статье содержатся сведения о предварительно созданных сущностях dimension в Интеллектуальной службе распознавания речи (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 3b2758f1d68ae3659f2e43cad555d327b21f8732
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36321706"
---
# <a name="dimension-entity"></a>Сущность dimension
Предварительно созданная сущность dimension обнаруживает различные типы измерений, независимо от языка и региональных параметров LUIS. Так как эта сущность уже обучена, добавлять в назначения приложения примеры фраз, содержащие сущности dimension, не нужно. Сущность dimension поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-dimension"></a>Типы сущностей dimension

Управление сущностью dimension выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml)


## <a name="resolution-for-dimension-entity"></a>Разрешение для сущности dimension
В следующем примере показано разрешение сущности **builtin.dimension**.

```JSON
{
  "query": "it takes more than 10 1/2 miles of cable and wire to hook it all up , and 23 computers.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.762141049
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.762141049
    }
  ],
  "entities": [
    {
      "entity": "10 1/2 miles",
      "type": "builtin.dimension",
      "startIndex": 19,
      "endIndex": 30,
      "resolution": {
        "unit": "Mile",
        "value": "10.5"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о сущностях [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) и [ordinal](luis-reference-prebuilt-ordinal.md). 