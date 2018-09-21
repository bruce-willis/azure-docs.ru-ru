---
title: Что такое служба "Распознавание речи" (LUIS) — Azure Cognitive Services | Документация Майкрософт
description: Распознавание речи (LUIS) — это облачная служба API, которая применяет пользовательскую аналитику машинного обучения к тексту пользователя в разговорном стиле и на естественном языке, чтобы предсказать общий смысл и извлечь соответствующую подробную информацию. Клиентское приложение для LUIS — это любое диалоговое приложение, которое взаимодействует с пользователем на естественном языке для выполнения задачи. Примеры клиентских приложений включают в себя приложения для социальных сетей, чат-боты и классические приложения с поддержкой речи.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: overview
ms.date: 08/15/2018
ms.author: diberry
ms.openlocfilehash: aadf80df388a25e07051f6dd0a83cd4da7c2ef83
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629803"
---
# <a name="what-is-language-understanding-luis"></a>Что такое служба "Распознавание речи" (LUIS)?

Распознавание речи (LUIS) — это облачная служба API, которая применяет пользовательскую аналитику машинного обучения к тексту пользователя в разговорном стиле и на естественном языке, чтобы предсказать общий смысл и извлечь соответствующую подробную информацию. 

Клиентское приложение для LUIS — это любое диалоговое приложение, которое взаимодействует с пользователем на естественном языке для выполнения задачи. Примеры клиентских приложений включают в себя приложения для социальных сетей, чат-боты и классические приложения с поддержкой речи.  

![Схематическое изображение 3-х клиентских приложений, работающих со службой "Распознавание речи" Cognitive Services](./media/luis-overview/luis-entry-point.png "Conceptual image of 3 client applications working with Cognitive Services Language Understanding (LUIS)")

## <a name="use-luis-in-a-chat-bot"></a>Использование LUIS в чат-боте

<a name="Accessing-LUIS"></a>

После публикации приложения LUIS клиентское приложение отправляет фразы (текст) в [API][endpoint-apis] конечной точки обработки естественного языка LUIS и получает результаты в виде ответов JSON. Типичное применение клиента для LUIS — чат-бот.


![Схематическое изображение LUIS, работающего с чат-ботом для прогнозирования пользовательского текста с помощью распознавания естественного языка](./media/luis-overview/luis-overview-process-2.png "Conceptual imagery of LUIS working with Chat bot to predict user text with natural language understanding (NLP")

|Шаг|Действие|
|:--|:--|
|1|Клиентское приложение отправляет пользовательскую _фразу_ (другими словами — текст) "Я хочу вызвать представителя отдела кадров" в конечную точку LUIS в качестве HTTP-запроса.|
|2|LUIS применяет изученную модель к тексту на естественном языке, чтобы обеспечить интеллектуальное распознавание текста, вводимого пользователем. LUIS возвращает ответ в формате JSON с главным намерением "HRContact". Ответ конечной точки JSON содержит по крайней мере фразу в запросе и намерение с высшим показателем. Он также позволяет извлекать данные, такие как сущность типа контакта.|
|3|Клиентское приложение использует ответ в формате JSON для принятия решений о том, как выполнять запросы пользователя. Эти решения могут включать определенное дерево принятия решений в коде Bot Framework и вызовы к другим службам. |

Приложение LUIS предоставляет аналитику, чтобы клиентское приложение могло сделать разумный выбор. LUIS не предоставляет такие варианты выбора. 

<!--

### Example of JSON endpoint response

The minimum JSON endpoint response contains the query utterance, and the top scoring intent. It can also extract data such as the following **Contact Type** entity. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```
-->

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>Обработка естественного языка

Приложение LUIS содержит разработанную для домена модель естественного языка. Вы можете запустить приложение LUIS с моделью предварительно созданного домена, создать собственную модель или объединить части готового домена с собственными пользовательскими данными.

* **Предварительно созданная модель**. В LUIS имеется множество предварительно созданных моделей предметной области, включая намерения, фразы и предварительно созданные сущности. Вы можете использовать эти сущности без намерений и фраз предварительно созданной модели. [Предварительно созданные модели предметной области](luis-how-to-use-prebuilt-domains.md). Включают всю структуру и являются отличным способом быстро начать использовать LUIS.

* **Пользовательские сущности**. LUIS предлагает несколько способов идентифицировать собственные намерения и сущности, включая сущности с машинным обучением, конкретные или литеральные сущности и комбинацию обоих типов.

## <a name="build-the-luis-model"></a>Создание модели LUIS
Создайте модель с помощью API [разработки](https://aka.ms/luis-authoring-apis) или портала LUIS.

Модель LUIS начинается с категорий намерений пользователя, называемых **[намерениями](luis-concept-intent.md)**. Каждое намерение должно иметь примеры пользовательских **[фраз](luis-concept-utterance.md)**. Каждая фраза может предоставить широкий набор данных, которые требуется извлечь с помощью **[сущностей](luis-concept-entity-types.md)**. 

|Пример фразы пользователя|Намерение|Сущности|
|-----------|-----------|-----------|
|"Забронировать рейс до __Сиэтла__?"|BookFlight|Сиэтл;|
|"Когда __открыт__ ваш магазин?"|StoreHoursAndLocation|открыт|
|"Запланировать встречу в __13:00__ с __Бобом__ в Distribution"|ScheduleMeeting|13:00, Боб|

<!--
## What is a natural language model?

A model begins with a list of general user intentions, called _intents_, such as "Book Flight" or "Contact Help Desk." You provide user's example text, called _example utterances_ for the intents. Then mark significant words or phrases in the utterance, called _entities_.


A model includes:

* **[intents](#intents)**: categories of user intentions (overall intended action or result)
* **[entities](#entities)**: specific types of data in utterances such as number, email, or name contained in text
* **[example utterances](#example-utterances)**: example text a user might enter in the client application

### Intents 

An [intent](luis-how-to-add-intents.md), short for _intention_, is a purpose or goal expressed in a user's utterance, such as booking a flight, paying a bill, or finding a news article. You create an intent for each action. A LUIS travel app may define an intent named "BookFlight." Each prediction query includes the top scored intent. 

The client application can use the top scoring intent to trigger an action. For example, when "BookFlight" intent is returned from LUIS, a client application could trigger an API call to an external service for booking a plane ticket.

### Entities

An [entity](luis-how-to-add-entities.md) represents detailed information found within the utterance that is relevant to the user's request. For example, in the utterance "Book a ticket to Paris",  a single ticket is requested, and "Paris" is a location. Two entities are found "a ticket" indicating a single ticket and "Paris" indicating the destination. 

After LUIS returns the entities found in the user’s utterance, the client application can use the list of entities as parameters to trigger an action. For example, booking a flight requires entities like the travel destination, date, and airline.
-->
<!--
### Example utterances

An example [utterance](luis-how-to-add-example-utterances.md) is text input from the user that the client application needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. Add 10 to 20 example utterances to each intent and mark entities in every utterance.

|Example user utterance|Intent|Entities|
|-----------|-----------|-----------|
|"Book a flight to __Seattle__?"|BookFlight|Seattle|
|"When does your store __open__?"|StoreHoursAndLocation|open|
|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|ScheduleMeeting|1pm, Bob|
-->
## <a name="query-prediction-endpoint"></a>Запрос конечной точки прогнозирования

После создания модели и ее публикации в конечной точке клиентское приложение отправляет фразы в API опубликованной [конечной точки](https://aka.ms/luis-endpoint-apis) прогнозирования. API применяет модель к тексту для анализа. API возвращает результаты прогнозирования в формате JSON.  

Ответ конечной точки JSON содержит по крайней мере фразу в запросе и намерение с высшим показателем. Он также позволяет извлекать данные, такие как сущность **Contact Type**. 

```JSON
{
  "query": "I want to call my HR rep.",
  "topScoringIntent": {
    "intent": "HRContact",
    "score": 0.921233
  },
  "entities": [
    {
      "entity": "call",
      "type": "Contact Type",
      "startIndex": 10,
      "endIndex": 13,
      "score": 0.7615982
    }
  ]
}
```

## <a name="improve-model-prediction"></a>Повышение точности прогнозирования модели

Когда модель LUIS будет опубликована и получит реальные пользовательские фразы, в LUIS можно применить несколько методов для повышения точности прогнозирования: [активное обучение](#active-learning) фраз конечных точек, [списки фраз](#phrase-lists) для включения слов в домен и [шаблоны](#patterns), чтобы уменьшить количество требуемых фраз.
<!--
### Active learning

In the [active learning](luis-how-to-review-endoint-utt.md) process, LUIS allows you to adapt the LUIS app to real-world utterances by selecting utterances it received at the endpoint for your review. You can accept or correct the endpoint prediction, retrain, and republish. LUIS learns quickly with this iterative process, taking the minimum amount of your time and effort. 

### Phrase lists 

LUIS provides [phrases lists](luis-concept-feature.md) so you can indicate important words or phrases of the model. LUIS uses these lists to add additional significance to those words and phrases that would otherwise not be found in the model.

### Patterns 

Patterns allow you to simplify an intent's utterance collection into common [templates](luis-concept-patterns.md) of word choice and word order. This allows LUIS to learn quicker by needing fewer example utterances for the intents. Patterns are a hybrid system of regular expressions and machine-learned expressions. 
-->
<a name="using-luis"></a>
<!--
## Authoring and accessing models
Author LUIS from the [authoring](https://aka.ms/luis-authoring-apis) APIs or from the LUIS portal. Query the published prediction endpoint of the model from the [endpoint](https://aka.ms/luis-endpoint-apis) APIs.
-->

## <a name="integrating-with-luis"></a>Интеграция с LUIS
Так как LUIS является REST API, это решение можно использовать с любым продуктом, службой или платформой, которая отправляет HTTP-запрос. Следующий список содержит основные продукты и службы корпорации Майкрософт, используемые с LUIS.

Клиентские приложения Майкрософт для LUIS:
* [Бот веб-приложения](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-3.0). Быстро создает чат-бот с поддержкой LUIS для общения с пользователем с помощью ввода текста. Для эффективной работы с ботом используется [Bot Framework][bot-framework] версии [3.x](https://github.com/Microsoft/BotBuilder) или [4.x](https://github.com/Microsoft/botbuilder-dotnet).
* [Windows Mixed Reality](https://docs.microsoft.com/windows/mixed-reality/). Узнайте больше с помощью этого курса о [смешанной реальности](https://docs.microsoft.com/windows/mixed-reality/mr-azure-303) с LUIS. 

Средства корпорации Майкрософт для использования LUIS с чат-ботом:
* [Диспетчеризация](https://aka.ms/dispatch-tool). Позволяет использовать несколько приложений LUIS и QnA Maker из родительского приложения с помощью модели диспетчера.
* [Средство для обучения ведению диалога](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview). Позволяет создавать диалоги с чат-ботом быстрее с помощью LUIS.
* [Индивидуальный чат в проекте](https://docs.microsoft.com/azure/cognitive-services/project-personality-chat/overview), позволяющий чат-боту обрабатывать непринужденные разговоры на общие темы.

Другие службы Cognitive Services, используемые в LUIS:
* [QnA Maker][qnamaker] позволяет объединять несколько типов текста в базу знаний вопросов и ответов.
* [API Bing для проверки орфографии](../bing-spell-check/proof-text.md) обеспечивает коррекцию текста перед предсказанием. 
* [Служба "Речь"](../Speech-Service/overview.md) преобразует запросы на разговорном языке в текст. 



## <a name="next-steps"></a>Дополнительная информация

Создайте приложение LUIS с [предварительно созданным](luis-get-started-create-app.md) или [пользовательским](luis-quickstart-intents-only.md) доменом. [Отправьте запрос к конечной точке прогнозирования](luis-get-started-cs-get-intent.md) общедоступного приложения Интернета вещей.

<!-- Reference-style links -->
[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://aka.ms/luis-authoring-api
[endpoint-apis]: https://aka.ms/luis-endpoint-apis
[qnamaker]: https://qnamaker.ai/