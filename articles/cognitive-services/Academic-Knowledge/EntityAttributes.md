---
title: Атрибуты сущностей Academic Graph для Academic Knowledge API | Документация Майкрософт
description: Дополнительные сведения об атрибутах сущностей, которые можно использовать совместно с Academic Graph в Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35379989"
---
# <a name="entity-attributes"></a>Атрибуты сущностей

Academic Graph содержит 7 типов сущностей. Все сущности будут иметь идентификатор сущности и тип сущности.

## <a name="common-entity-attributes"></a>Общие атрибуты сущностей
ИМЯ    |ОПИСАНИЕ                |type       | Операции
------- | ------------------------- | --------- | ----------------------------
Идентификатор      |Идентификатор сущности                  |Int64      |Равно
Ty      |Тип сущности                |enum   |Равно

## <a name="entity-type-enum"></a>Тип сущности enum
ИМЯ                                                            |value
----------------------------------------------------------------|-----
[Документ](PaperEntityAttributes.md)                               |0
[Автор](AuthorEntityAttributes.md)                             |1
[Журнал](JournalEntityAttributes.md)                           |2
[Серия конференций](JournalEntityAttributes.md)                 |3
[Экземпляр конференции](ConferenceInstanceEntityAttributes.md)    |4.
[Принадлежность](AffiliationEntityAttributes.md)                   |5
[Область исследований](FieldsOfStudyEntityAttributes.md)                      |6

