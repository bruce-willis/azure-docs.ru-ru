---
title: Основные сведения об использовании ролей в сущностях на основе шаблонов — Azure | Документы Майкрософт
description: Сведения об использовании роли в сущности на основе шаблона для присвоения имени контекстно-зависимому подтипу сущности.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35383482"
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
