---
title: Границы службы "Распознавание речи" (LUIS) | Документация Майкрософт
titleSuffix: Azure
description: В этой статье приводятся известные ограничения LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/31/2018
ms.author: diberry
ms.openlocfilehash: 0a75c113f619aa70ba6c504f07afc8dc8a35b694
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399837"
---
# <a name="luis-boundaries"></a>Границы LUIS
У LUIS есть несколько областей границ. Первая — [граница модели](#model-boundaries), которая управляет намерениями, сущностями и возможностями в LUIS. Вторая область — [пределы квот](#key-limits) на основе типа ключа. Третья область границ — [сочетания клавиш](#keyboard-controls) для управления веб-сайтом LUIS. Четвертая область — [сопоставление региона мира](luis-reference-regions.md) между веб-сайтом разработки LUIS и API-интерфейсами [конечной точки](luis-glossary.md#endpoint) LUIS. 


## <a name="model-boundaries"></a>Границы модели

|Область|Ограничение|
|--|:--|--|
| [Имя приложения][luis-get-started-create-app] | * Макс. кол-во символов по умолчанию |
| [Пакетное тестирование][batch-testing]| 10 наборов данных, 1000 высказываний для каждого набора данных|
| **[Составная сущность](./luis-concept-entity-types.md)|100 с максимум десятью дочерними сущностями |
| Явный список | 50 для каждого приложения|
| **[Иерархическая сущность](./luis-concept-entity-types.md) |100 с максимум десятью дочерними сущностями |
| [Намерения][intents]|500 для каждого приложения<br>Приложение [на основе диспетчеризации](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) имеет 500 соответствующих источников диспетчеризации|
| [Сущности списка](./luis-concept-entity-types.md) | Родитель: 50, потомок: 20 000 элементов. Каноническое имя — *макс. кол-во символов по умолчанию. Значения синонимов не имеют ограничений по длине. |
| [Шаблоны](luis-concept-patterns.md)|500 шаблонов для каждого приложения.<br>Максимальная длина шаблона — 400 символов.<br>3 сущности Pattern.any для каждого шаблона<br>Максимум 2 вложенных необязательных текста в шаблоне|
| [Pattern.Any](./luis-concept-entity-types.md)|100 для каждого приложения, 3 сущности pattern.any для каждого шаблона |
| [Список фраз][phrase-list]|10 списков фраз, 5000 элементов для каждого списка|
| [Предварительно созданные сущности](./luis-prebuilt-entities.md) | без ограничений|
| [Сущности регулярного выражения](./luis-concept-entity-types.md)|20 сущностей<br>макс. 500 символов для каждого шаблона сущности регулярного выражения|
| [Роли](luis-concept-roles.md)|300 ролей для каждого приложения, 10 ролей для каждой сущности|
| **[Простая сущность](./luis-concept-entity-types.md)| 100 сущностей|
| [Высказывание][utterances] | 500 символов|
| [Высказывания][utterances] | 15 000 для каждого приложения|
| [Версии](luis-concept-version.md)| без ограничений |
| [Имя версии][luis-how-to-manage-versions] | 10 символов — только буквенно-цифровые и точка (.) |

* Макс. кол-во символов по умолчанию — 50. 

** Общее количество простых, иерархических и составных сущностей не может превышать 100. Общее количество иерархических сущностей, составных сущностей, простых сущностей и дочерних элементов иерархических сущностей не может превышать 330. 

## <a name="intent-and-entity-naming"></a>Именование намерений и сущностей
В именах сущностей и намерений нельзя использовать следующие символы:

|Character|ИМЯ|
|--|--|
|`{`|Открывающая фигурная скобка|
|`}`|Закрывающая фигурная скобка|
|`[`|Открывающая квадратная скобка|
|`]`|Закрывающая квадратная скобка|
|`\`|Обратная косая черта|

## <a name="key-limits"></a>Ограничения ключа
Ключ разработки имеет разные ограничения для разработки и конечной точки. Ключ конечной точки службы LUIS является допустимым только для запросов конечной точки.

|Ключ|Разработка|Конечная точка|Назначение|
|--|--|--|--|
|Разработка/начальный|1 млн/месяц, 5/с|1 тыс./месяц, 5/с|Разработка приложения LUIS|
|[Подписка] [ pricing] — F0 — уровень "Бесплатный" |недопустимо|10 тыс./месяц, 5/с|Запрос конечной точки LUIS|
|[Subscription][pricing] — S0 — цен. категория "Базовый"|недопустимо|50/с|Запрос конечной точки LUIS|
|[Интеграция анализа тональности](luis-how-to-publish-app.md#enable-sentiment-analysis)|недопустимо|бесплатно|Добавление сведений о тональности, включая извлечение данных ключевой фразы |
|Интеграция речи|недопустимо|5,5 долл. США/1 тыс. запросов конечной точки|Преобразование голосового высказывания в текстовое и возвращение результатов LUIS|

## <a name="keyboard-controls"></a>Элементы управления клавиатуры

|Ввод с клавиатуры | ОПИСАНИЕ | 
|--|--|
|CTRL+E|Переключение между токенами и сущностями в списке высказываний|

## <a name="website-sign-in-time-period"></a>Период времени для входа на веб-сайт

Выполнять вход можно в течение **60 минут**. По истечении этого времени возникнет ошибка. Необходимо снова выполнить вход.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
