---
title: Фильтрация ненормативной лексики с помощью API для работы с текстами Microsoft Translator | Документация Майкрософт
description: Используйте фильтрацию ненормативной лексики в API для работы с текстами Microsoft Translator.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a7172e1e8aa336c011fb06e93fc5c4b54d26a3cd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380817"
---
# <a name="how-to-add-profanity-filtering-with-the-microsoft-translator-text-api"></a>Добавление фильтра ненормативной лексики с помощью API для работы с текстами Microsoft Translator

Обычно служба переводов сохраняет в переводе ненормативную лексику, которая присутствует в источнике. Степень ненормативной лексики и контекст, который делает слова оскорбительными, отличаются между культурами. В результате степень оскорбительности лексики на целевом языке может усиливаться или уменьшаться.

Если необходимо избежать появления ненормативной лексики при переводе (независимо от наличия ненормативной лексики в источнике), можно использовать параметр фильтрации ненормативной лексики в методе Translate(). Этот параметр позволяет выбрать, хотите ли вы удалить ненормативную лексику, пометить ее соответствующими тегами или не предпринимать никаких действий.

Метод Translate() принимает параметр "options", который содержит новый элемент ProfanityAction. Принятыми значениями для элемента ProfanityAction являются "NoAction" (пропущено), "Marked" (помечено) и "Deleted" (удалено).

## <a name="accepted-values-of-profanityaction-and-examples"></a>Принятые значения ProfanityAction и примеры
|Значение ProfanityAction | Действие | Пример: Источник — японский | Пример: Целевой объект — английский|
| :---|:---|:---|:---|
| NoAction | По умолчанию. Аналогично отсутствию параметра. Ненормативная лексика переходит из источника в целевой объект. | 彼は変態です。 | Он подонок. |
| Marked | Оскорбительные слова выделены XML тегами \<profanity> … \</profanity>. | 彼は変態です。 | Он \<нецензурная лексика>jerk\</profanity >. |
| Deleted | Оскорбительные слова удаляются из выходных данных без замены. | 彼は。 | Он — . |

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Применение фильтра ненормативной лексики с помощью вызова API переводчика](reference/v3-0-translate.md)

