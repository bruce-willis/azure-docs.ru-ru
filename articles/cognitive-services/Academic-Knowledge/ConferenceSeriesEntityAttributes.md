---
title: Атрибуты сущности серии конференций в Academic Knowledge API | Документация Майкрософт
description: Дополнительные сведения об атрибутах, которые можно использовать с сущностью серии конференций в Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 332736c927bdaa00334546f626a6eabb8e11d3b5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380000"
---
# <a name="conference-series-entity"></a>Сущность серии конференций

<sub> *Следующие атрибуты относятся к сущности серии конференций. (Ty = '3') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
CN      |Нормализованное имя серии конференций      |Строка     |Равно
DCN     |Отображаемое имя серии конференций         |Строка     |Нет
CC      |Общий показатель цитируемости серии конференций         |Int32      |Нет  
ECC     |Общий расчетный показатель цитируемости серии конференций   |Int32      |Нет
F.FId   |Идентификатор сущности области исследований, связанный с серией конференций |Int64  | Равно
F.FN    |Имя сущности области исследований, связанное с серией конференций  | Равно,<br/>StartsWith;