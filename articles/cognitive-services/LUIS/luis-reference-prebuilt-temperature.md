---
title: Справочник по предварительно созданным сущностям temperature в службе LUIS в Azure | Документация Microsoft
titleSuffix: Azure
description: В этой статье описана готовая сущность temperature в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 39d33a3132a400ce0f1853062968cfdaf81c693f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041478"
---
# <a name="temperature-entity"></a>Сущность temperature
Сущность temperature извлекает различные типы температурных данных. Так как эта сущность уже обучена, добавлять в приложение примеры фраз, содержащие сущности temperature, не нужно. Сущность temperature поддерживается во [многих языках и региональных параметрах](luis-reference-prebuilt-entities.md). 

## <a name="types-of-temperature"></a>Типы сущностей temperature
Управление сущностью temperature выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L819)

## <a name="resolution-for-prebuilt-temperature-entity"></a>Разрешение для предварительно созданной сущности temperature
В следующем примере показано разрешение сущности **builtin.temperature**.

```JSON
{
  "query": "set the temperature to 30 degrees",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.85310787
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.85310787
    }
  ],
  "entities": [
    {
      "entity": "30 degrees",
      "type": "builtin.temperature",
      "startIndex": 23,
      "endIndex": 32,
      "resolution": {
        "unit": "Degree",
        "value": "30"
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [age](luis-reference-prebuilt-age.md). 