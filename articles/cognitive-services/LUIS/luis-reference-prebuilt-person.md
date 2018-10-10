---
title: Справочник по LUIS. Предварительно созданная сущность PersonName
titleSuffix: Azure Cognitive Services
description: В этой статье описана предварительно созданная сущность PersonName в службе "Распознавание речи" (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0bc9d59fa44125429bfcb4e6ca9e68d93b81c04d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950579"
---
# <a name="personname-entity"></a>Сущность PersonName
Предварительно созданная сущность PersonName определяет имена людей. Так как эта сущность уже обучена, добавлять в намерения приложения примеры высказываний, содержащие сущности PersonName, не нужно. Сущность personName поддерживается для английского и китайского [языков и региональных параметров](luis-reference-prebuilt-entities.md).

## <a name="resolution-for-personname-entity"></a>Разрешение для сущности personName
В следующем примере показано разрешение сущности **builtin.PersonName**.

```JSON
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  }
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о сущностях [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) и [ordinal](luis-reference-prebuilt-ordinal.md). 