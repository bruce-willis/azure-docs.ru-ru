---
title: Справочник по предварительно созданным сущностям keyPhrase службы LUIS в Azure | Документация Майкрософт
titleSuffix: Azure
description: В этой статье содержатся сведения о предварительно созданных сущностях keyPhrase в Интеллектуальной службе распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/09/2018
ms.author: diberry
ms.openlocfilehash: 4133b7c7c3fabbe92a3208c567d7b4c6c2c27283
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434404"
---
# <a name="keyphrase-entity"></a>Сущность keyPhrase
keyPhrase извлекает различные ключевые фразы из высказывания. Добавление в приложение примеров высказываний, содержащих keyPhrase, не требуется. Сущность keyPhrase поддерживается во [многих языках и региональных параметрах](luis-language-support.md#languages-supported) как часть функции [текстовой аналитики](../text-analytics/overview.md). 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Разрешение для предварительно созданной сущности keyPhrase
В следующем примере показано разрешение для сущности **builtin.keyPhrase**.

```JSON
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [percentage](luis-reference-prebuilt-percentage.md), [number](luis-reference-prebuilt-number.md) и [age](luis-reference-prebuilt-age.md).
