---
title: Атрибуты авторских сущностей в Academic Knowledge API | Документация Майкрософт
description: Изучите атрибуты, которые вы можете использовать с авторской сущностью в Academic Knowledge API в Cognitive Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35379993"
---
# <a name="author-entity"></a>Авторские сущности
<sub> *Следующие атрибуты относятся к авторским сущностям. (Ty = '1') </sub>

ИМЯ    |ОПИСАНИЕ                            |type       | Операции
------- | ------------------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                              |Int64      |Равно
AuN     |Нормализованное имя автора                 |Строка     |Равно
DAuN    |Отображаемое имя автора                    |Строка     |Нет
CC      |Общий показатель цитируемости автора            |Int32      |Нет  
ECC     |Общий расчетный показатель цитируемости автора  |Int32      |Нет
E       |Расширенные метаданные (см. табл. "Атрибуты расширенных метаданных")  |Строка     |Нет  


## <a name="extended-metadata-attributes"></a>Расширенные атрибуты метаданных ##

ИМЯ    | ОПИСАНИЕ               
--------|---------------------------    
LKA.Afn     | отображаемое имя принадлежности, связанной с автором  
LKA.AfId        | идентификатор сущности принадлежности, связанной с автором