---
title: Атрибуты сущности принадлежности в Academic Knowledge API | Документация Майкрософт
description: Изучите атрибуты, которые можно использовать совместно с сущностью принадлежности в Academic Knowledge API в Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a0ec67cb811ca207b3d038028491da2516028f0b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35379992"
---
# <a name="affiliation-entity"></a>Сущность принадлежности

<sub> *Следующие атрибуты относятся к сущностям принадлежности. (Ty = '5') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
AfN     |Нормализованное имя принадлежности        |Строка     |Равно
DAfN    |Отображаемое имя принадлежности       |Строка     |Нет
CC      |Общий показатель цитируемости принадлежности           |Int32      |Нет  
ECC     |Общий расчетный показатель цитируемости принадлежности |Int32      |Нет

## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

ИМЯ    | ОПИСАНИЕ               
--------|---------------------------    
PC      |Количество публикаций по принадлежности