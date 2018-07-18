---
title: Метод Analyzers в API лингвистического анализа | Документация Майкрософт
description: Analyzers REST API предоставляет ряд диагностических анализаторов, которые в настоящее время поддерживаются службой Microsoft Cognitive Services.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380065"
---
# <a name="analyzers-method"></a>Метод Analyzers

**Analyzers** REST API предоставляет список диагностических анализаторов, которые в настоящее время поддерживаются этой службой.
Ответ содержит их [имена](Analyzer-Names.md) и языки, поддерживаемые каждым из них (например, "en" для английского языка).

## <a name="request-parameters"></a>Параметры запроса
None

<br>

## <a name="response-parameters"></a>Параметры ответа
ИМЯ | type | ОПИСАНИЕ
-----|------|--------------
языки | Список строк | список двухбуквенных ISO-кодов языка, для которых этот диагностический анализатор можно использовать.
id   | строка | уникальный идентификатор для этого анализатора
kind | строка | здесь широкий шрифт анализатора
спецификация | строка | имя спецификации, используемой для этого анализатора
реализация | строка | описание модели и/или алгоритм этого анализатора

<br>
## <a name="example"></a>Пример
GET/Analyzers

Ответ: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3", 
        "implementation": "SplitMerge"
    }, 
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags", 
        "specification": "PennTreebank3", 
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens", 
        "specification":"PennTreebank3", 
        "implementation": "regexes"
    } 
]
```
