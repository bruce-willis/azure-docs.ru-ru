---
title: Общие принципы изменения данных в LUIS в Azure | Документы Майкрософт
description: Узнайте, как изменить данные перед прогнозированием в Language Understanding (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/26/2018
ms.author: v-geberr
ms.openlocfilehash: 4fb1a5542bb56bd853984e66198ebfbd189451f8
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266871"
---
# <a name="data-alterations"></a>Изменения данных
LUIS предоставляет способы управления высказыванием до или во время прогнозирования. 

## <a name="correct-spelling-errors-in-utterance"></a>Исправление орфографических ошибок в высказывании
Для исправления орфографических ошибок в высказывании LUIS использует [API проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/). LUIS требуется ключ, связанный с этой службой. Создайте ключ, а затем добавьте его в качестве параметра QueryString в [конечную точку](https://aka.ms/luis-endpoint-apis). 

Орфографические ошибки можно также исправить на панели **Test** (Тестирование) путем [ввода ключа](interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Ключ хранится в виде переменной сеанса в браузере для панели тестирования. Добавляйте ключ на панель тестирования в каждом сеансе браузера, где необходимо исправить орфографические ошибки. 

При применении ключа на панели тестирования и в конечной точке учитывается квота на [использование ключа](https://azure.microsoft.com/pricing/details/cognitive-services/spellcheck-api/). LUIS реализует ограничения проверки орфографии Bing на длину текста. 

Чтобы исправления орфографических ошибок вступили в силу, конечной точке требуется два параметра.

|Параметр|Значение|
|--|--|
|`spellCheck`|Логическое|
|`bing-spell-check-subscription-key`|Ключ подписки [API проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/)|

Когда [API проверки орфографии Bing версии 7](https://azure.microsoft.com/services/cognitive-services/spell-check/) обнаруживает ошибку, из конечной точки возвращаются исходное выражение, исправленное выражение и прогнозирование.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```
 
## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Изменение часового пояса предварительно созданной сущности datetimeV2
Когда приложение LUIS использует предварительно созданную сущность datetimeV2, в ответе прогнозирования может возвращаться значение даты и времени. Часовой пояс запроса используется для определения правильного возвращаемого значения даты и времени. Если запрос поступает от бота или другого централизованного приложения до передачи в LUIS, исправьте часовой пояс, используемый LUIS. 

### <a name="endpoint-querystring-parameter"></a>Параметр QueryString конечной точки
Часовой пояс исправляется путем добавления часового пояса пользователя к [конечной точке](https://aka.ms/luis-endpoint-apis) с помощью параметра `timezoneOffset`. Чтобы изменить время, значение `timezoneOffset` должно быть положительным или отрицательным числом в минутах.  

|Параметр|Значение|
|--|--|
|`timezoneOffset`|положительное или отрицательное число в минутах|

### <a name="daylight-savings-example"></a>Пример перехода на летнее время
Чтобы скорректировать возвращенную предварительно созданную сущность datetimeV2 для перехода на летнее время, следует использовать параметр QueryString `timezoneOffset` со значением +/– в минутах для запроса [конечной точки](https://aka.ms/luis-endpoint-apis).

Добавьте 60 минут: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

Удалите 60 минут: 

https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?**timezoneOffset=-60**&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}

## <a name="c-code-determines-correct-value-of-timezoneoffset"></a>Код на C# определяет правильное значение timezoneOffset
В следующем коде на C# для определения правильного значения `timezoneOffset` на основе системного времени используется метод [FindSystemTimeZoneById](https://docs.microsoft.com/dotnet/api/system.timezoneinfo.findsystemtimezonebyid?view=netframework-4.7.1#examples) класса [TimeZoneInfo](https://docs.microsoft.com/dotnet/api/system.timezoneinfo?view=netframework-4.7.1):

```CSharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset
int timezoneOffset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Исправление орфографических ошибок с помощью этого руководства](luis-tutorial-bing-spellcheck.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions