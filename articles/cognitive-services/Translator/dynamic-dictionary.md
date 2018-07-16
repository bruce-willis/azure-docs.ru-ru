---
title: Динамический словарь API перевода текстов Microsoft | Документация Майкрософт
description: Использование функции динамического словаря API перевода текстов Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382873"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Использование функции динамического словаря API перевода текстов Microsoft

Если вы уже знаете перевод, который хотите применить к слову или фразе, вы можете указать его в запросе как исправление. Динамический словарь безопасен только для составных существительных, таких как имена собственные и названия продуктов. 

**Синтаксис** 

<mstrans:dictionary translation="перевод фразы">фраза</mstrans:dictionary>

**Пример: en-de:**

Источник входных данных: Слово <mstrans: dictionary translation = \"wordomatic\"> слово или фраза </mstrans: dictionary> — это словарная запись.

Целевые выходные данные: ein ist Das Wort "wordomatic" Wörterbucheintrag.

Эта возможность работает одинаково как в режиме HTML, так и без него. 

Компонент должен использоваться только в случае необходимости. Соответствующий и гораздо лучший способ настройки перевода — это использование концентратора Microsoft Translator. Концентратор обеспечивает полное использование контекста и статистические значения вероятности. Если вы имеете или можете позволить себе создавать учебные данные, которые показывают вашу работу или фразу в контексте, вы получите гораздо лучшие результаты. Дополнительную информацию о концентраторе можно найти на [http://hub.microsofttranslator.com](http://hub.microsofttranslator.com).

