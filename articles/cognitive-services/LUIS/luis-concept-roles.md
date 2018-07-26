---
title: Основные сведения об использовании ролей в сущностях на основе шаблонов — Azure | Документы Майкрософт
description: Сведения об использовании роли в сущности на основе шаблона для присвоения имени контекстно-зависимому подтипу сущности.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry
ms.openlocfilehash: d2692cdce9da7428bd7b30c4feaf7347792618f5
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222709"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Роли сущностей в шаблонах — это контекстно-зависимые подтипы
Роли — это именованные, контекстно-зависимые подтипы сущности, которые используется только в [шаблонах](luis-concept-patterns.md).

Например, во фрагменте речи `buy a ticket from New York to London` New York и London являются городами, однако имеют разное значение в предложении. New York — это город отправления, а London — город назначения. 

Роли присваивают имена этим различиям:

|Сущность|Роль|Назначение|
|--|--|--|
|Расположение|origin|откуда вылетает самолет|
|Расположение|ресурс destination|куда прилетает самолет|

## <a name="how-are-roles-used-in-patterns"></a>Использование ролей в шаблонах
Во фрагменте речи шаблона роли используются внутри фрагмента речи: 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>Синтаксис роли в шаблонах
Сущность и роль заключаются в круглые скобки, `{}`. Сущность и роль разделяются точкой с запятой. 

## <a name="roles-versus-hierarchical-entities"></a>Сравнение ролей и иерархических сущностей
Иерархические сущности предоставляют те же контекстно-зависимые сведения, что и роли, но для фрагментов речи в **намерениях**. Аналогичным образом, роли предоставляют те же контекстно-зависимые сведения, что и иерархические сущности, но только в **шаблонах**.

|Контекстно-зависимое обучение|Используется в|
|--|--|
|иерархических сущностях,|намерениях,|
|roles|шаблонах.|

## <a name="next-steps"></a>Дополнительная информация

* Сведения о добавлении [ролей](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
