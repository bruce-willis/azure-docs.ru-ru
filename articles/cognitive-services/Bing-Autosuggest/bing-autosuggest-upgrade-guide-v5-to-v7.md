---
title: Обновление API автозаполнения Bing версии 5 до версии 7 | Документация Майкрософт
description: Определение частей приложения, которые необходимо обновить для использования версии 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 751EDCF0-0C8B-4C23-942C-FA06F5DAD3FD
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 01/12/2017
ms.author: scottwhi
ms.openlocfilehash: 5663a671711dba4f44c89e8221a729c6670ec8fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382120"
---
# <a name="autosuggest-api-upgrade-guide"></a>Руководство по обновлению API автозаполнения

В этом руководстве по обновлению определены изменения между версиями 5 и 7 API автозаполнения Bing. Руководство поможет вам определить части приложения, которые необходимо обновить для использования версии 7.

## <a name="breaking-changes"></a>Критические изменения

### <a name="endpoints"></a>Конечные точки

- Номер версии конечной точки изменен с 5 на 7. Например, https://api.cognitive.microsoft.com/bing/\*\*v7.0\*\*/Suggestions.

### <a name="error-response-objects-and-error-codes"></a>Объекты ответов на ошибки и коды ошибок

- Все невыполненные запросы теперь должны включать объект `ErrorResponse` в текст ответа.

- В объект `Error` добавлены следующие поля:  
  - `subCode`&mdash; разделяет код ошибки на дискретные группы, если это возможно.
  - `moreDetails`&mdash; дополнительные сведения об ошибке, описанной в поле `message`.

- Коды ошибок версии 5 заменены следующими возможными значениями `code` и `subCode`.

|Код|SubCode|ОПИСАНИЕ
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing возвращает ошибку ServerError при соблюдении любого из условий вложенного кода. Ответ включает ошибки, если код состояния HTTP — 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Заблокировано|Bing возвращает ошибку InvalidRequest, когда любая часть запроса является недопустимой. Например, когда отсутствует обязательный параметр или значение параметра является недопустимым.<br/><br/>В случае возникновения ошибки ParameterMissing или ParameterInvalidValue кодом состояния HTTP будет 400.<br/><br/>При ошибке HttpNotAllowed код состояния HTTP будет 410.
|RateLimitExceeded||Bing возвращает ошибку RateLimitExceeded при превышении квоты запросов в секунду (QPS) или запросов в месяц (QPM).<br/><br/>Bing возвращает код состояния HTTP 429 при превышении квоты QPS и 403 при превышении QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing возвращает ошибку InvalidAuthorization при невозможности выполнить аутентификацию вызывающей стороны. Например, когда заголовок `Ocp-Apim-Subscription-Key` отсутствует или ключ подписки является недопустимым.<br/><br/>Избыточность возникает, если указать более одного способа аутентификации.<br/><br/>При ошибке InvalidAuthorization кодом состояния HTTP будет 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing возвращает ошибку InsufficientAuthorization, когда вызывающая сторона не имеет разрешений на доступ к ресурсу. Это может произойти, если ключ подписки отключен или срок его действия истек. <br/><br/>При ошибке InsufficientAuthorization кодом состояния HTTP будет 403.

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

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Требования к использованию и отображению](./UseAndDisplayRequirements.md)
