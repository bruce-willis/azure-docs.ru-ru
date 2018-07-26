---
title: Справочник по предварительно созданным сущностям phonenumber в LUIS Azure | Документация Майкрософт
titleSuffix: Azure
description: В этой статье содержатся сведения о предварительно созданных сущностях phonenumber в Интеллектуальной службе распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 1ae14f72f0dc610b9399e675f49ef5fff51a6965
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238997"
---
# <a name="phonenumber-entity"></a>Сущность phonenumber
Сущность `phonenumber` извлекает различные номера телефонов, в том числе код страны. Так как эта сущность уже обучена, добавлять в приложение примеры фраз не нужно. Сущность `phonenumber` поддерживается только в языке `en-us`. 

## <a name="types-of-phonenumber"></a>Типы сущности phonenumber
Управление сущностью phonenumber выполняется из репозитория GitHub [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml).

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Разрешение для предварительно созданной сущности phonenumber
В следующем примере показано разрешение сущности **builtin.phonenumber**.

```JSON
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [temperature](luis-reference-prebuilt-temperature.md). 