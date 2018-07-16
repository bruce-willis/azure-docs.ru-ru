---
title: Обновление API Bing для поиска в Интернете версии 5 до версии 7 | Документация Майкрософт
description: Определение частей приложения, которые необходимо обновить для использования версии 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/15/2017
ms.author: scottwhi
ms.openlocfilehash: 155297f230c0ee02d6fa49d6d35eb24d9941f29b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382112"
---
# <a name="web-search-api-upgrade-guide"></a>Руководство по обновлению API для поиска в Интернете

В этом руководстве по обновлению определены изменения между версиями 5 и 7 API Bing для поиска в Интернете. Руководство поможет вам определить части приложения, которые необходимо обновить для использования версии 7.

## <a name="breaking-changes"></a>Критические изменения

### <a name="endpoints"></a>Конечные точки

- Номер версии конечной точки изменен с 5 на 7. Например, https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Объекты ответов на ошибки и коды ошибок

- Все невыполненные запросы теперь должны включать объект `ErrorResponse` в текст ответа.

- В объект `Error` добавлены следующие поля:  
  - `subCode`&mdash; разделяет код ошибки на дискретные группы, если это возможно.
  - `moreDetails`&mdash; дополнительные сведения об ошибке, описанной в поле `message`.
   

- Коды ошибок версии 5 заменены следующими возможными значениями `code` и `subCode`.

|Код|SubCode|ОПИСАНИЕ
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing возвращает ошибку ServerError при соблюдении любого из условий вложенного кода. Ответ будет включать в себя ошибки, если код состояния HTTP — 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Заблокировано|Bing возвращает ошибку InvalidRequest, когда любая часть запроса является недопустимой. Например, когда отсутствует обязательный параметр или значение параметра является недопустимым.<br/><br/>В случае возникновения ошибки ParameterMissing или ParameterInvalidValue кодом состояния HTTP будет 400.<br/><br/>При ошибке HttpNotAllowed код состояния HTTP будет 410.
|RateLimitExceeded||Bing возвращает ошибку RateLimitExceeded при превышении квоты запросов в секунду (QPS) или запросов в месяц (QPM).<br/><br/>Bing возвращает код состояния HTTP 429 при превышении квоты QPS и 403 при превышении QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing возвращает ошибку InvalidAuthorization при невозможности выполнить аутентификацию вызывающей стороны. Например, когда заголовок `Ocp-Apim-Subscription-Key` отсутствует или ключ подписки является недопустимым.<br/><br/>Избыточность возникает, если указать более одного способа аутентификации.<br/><br/>При ошибке InvalidAuthorization кодом состояния HTTP будет 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing возвращает ошибку InsufficientAuthorization, когда вызывающая сторона не имеет разрешений на доступ к ресурсу. Эта ошибка может произойти, если ключ подписки отключен или срок его действия истек. <br/><br/>При ошибке InsufficientAuthorization кодом состояния HTTP будет 403.

- Ниже представлено сопоставление предыдущих и новых кодов ошибок. При наличии зависимостей от кодов ошибок версии 5 обновите свой код соответствующим образом.

|Код версии 5|Код версии 7.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Отключено|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Заблокировано|InvalidRequest.Blocked


## <a name="non-breaking-changes"></a>Некритические изменения  

### <a name="headers"></a>Заголовки

- Добавлен необязательный заголовок запроса [Pragma](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#pragma). По умолчанию Bing возвращает кэшированное содержимое, если оно доступно. Чтобы Bing не возвращал кэшированное содержимое, установите для заголовка Pragma значение no-cache (например, Pragma: no-cache).

### <a name="query-parameters"></a>Параметры запроса

- Добавлен параметр запроса [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#answercount). С помощью этого параметра можно указать число ответов, которое должен включать ответ. Ответы выбираются на основе ранжирования. Например, если задать этот параметр равным трем (3), ответ будет включать три ответа с самым высоким приоритетом.  
  
- Добавлен параметр запроса [promote](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#promote). Используя этот параметр вместе с `answerCount`, можно явно включить один или несколько типов ответов независимо от их ранга. Например, для включения в ответ видео и изображений следует задать для параметра promote значение *videos,images*. Список ответов, который требуется включить, не учитывается в предельном количестве `answerCount`. Например, если для `answerCount` задано значение 2, а для `promote` установлено значение *videos,images*, в ответ могут быть включены веб-страницы, новости, видео и изображения.

### <a name="object-changes"></a>Изменения объектов

- Поле `someResultsRemoved` добавлено в объект [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#webanswer). Поле содержит логическое значение, которое указывает, были ли исключены из ответа некоторые результаты ответа при поиске в Интернете.  

