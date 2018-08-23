---
title: Обновление API Bing для поиска изображений версии 5 до версии 7 | Документация Майкрософт
description: Идентифицирует части приложения, которые необходимо обновить для использования версии 7.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: b4d785eafe9ced6fb12e2dac3282dfd286849eb6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380309"
---
# <a name="image-search-api-upgrade-guide"></a>Руководство по обновлению API для поиска изображений

В этом руководстве по обновлению определены изменения между версиями 5 и 7 API Bing для поиска изображений. Руководство поможет определить компоненты приложения, которые необходимо обновить для использования версии 7.

## <a name="breaking-changes"></a>Критические изменения

### <a name="endpoints"></a>Конечные точки

- Номер версии конечной точки изменен с 5 на 7. Например, https://api.cognitive.microsoft.com/bing/\*\*v7.0\*\*/images/search.

### <a name="error-response-objects-and-error-codes"></a>Объекты ответов на ошибки и коды ошибок

- Все невыполненные запросы теперь должны включать в тело ответа объект `ErrorResponse`.

- В объект `Error` добавлены следующие поля:  
  - `subCode`&mdash;Разделяет код ошибки на дискретные сегменты, если это возможно
  - `moreDetails`&mdash;Дополнительные сведения об ошибке, описанной в поле `message`
   

- Коды ошибок версии 5 заменены следующими возможными значениями `code` и `subCode`.

|Код|SubCode|ОПИСАНИЕ
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing возвращает ServerError (ошибку сервера) каждый раз при возникновении любого из условий вложенного кода. Ответ включает в себя ошибки, если код состояния HTTP — 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Заблокировано|Bing возвращает ошибку InvalidRequest (недопустимый запрос) всякий раз, когда любая часть запроса недопустима. Например, отсутствует обязательный параметр или значение параметра недопустимо.<br/><br/>В случае ошибки ParameterMissing (отсутствующий параметр) или ParameterInvalidValue (недопустимое значение параметра) кодом состояния HTTP будет 400.<br/><br/>При ошибке HttpNotAllowed (HTTP запрещен) будет наблюдаться код состояния HTTP 410.
|RateLimitExceeded||Bing возвращает ошибку RateLimitExceeded (превышение лимита частоты) всякий раз при превышении квоты запросов в секунду (QPS) или запросов в месяц (QPM).<br/><br/>Bing возвращает код состояния HTTP 429 при превышении квоты QPS и 403 при превышении QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing возвращает InvalidAuthorization, когда Bing не может проверить подлинность вызывающего объекта. Например, когда заголовок `Ocp-Apim-Subscription-Key` отсутствует или при недопустимом ключе подписки.<br/><br/>Избыточность возникает, если указать более одного способа проверки подлинности.<br/><br/>При ошибке InvalidAuthorization кодом состояния HTTP будет 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing возвращает InsufficientAuthorization, когда вызывающая сторона не имеет разрешений на доступ к ресурсу. Это может произойти, если ключ подписки отключен или срок его действия истек. <br/><br/>При ошибке InsufficientAuthorization кодом состояния HTTP будет 403.

- Ниже представлено сопоставление предыдущих кодов ошибок с новыми. При наличии зависимостей от кодов ошибок версии 5 обновите свой код соответствующим образом.

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



### <a name="query-parameters"></a>Параметры запроса

- Параметр запроса `modulesRequested` переименован в [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules).  
  
- Объект Annotations переименован в Tags. Сведения о значении Tags см. в описании параметра [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules).  

- Изменен список поддерживаемых рынков для значения фильтра ShoppingSources (доступен только вариант en-US). Ознакомьтесь с описанием параметра [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype).  
 

### <a name="image-insights-changes"></a>Изменения в аналитических сведениях об изображении
  
- Поле `annotations` объекта [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) переименовано в `imageTags`.  
  
- Объект `AnnotationModule` переименован в [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetagsmodule).  
  
- Объект `Annotation` переименован в [Tag](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#tag), а также удалено поле `confidence`.  
  
- Поле `insightsSourcesSummary` объекта [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) переименовано в `insightsMetadata`.  
  
- Объект `InsightsSourcesSummary` переименован в [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightsmetadata).  
  
- Добавлена конечная точка `https://api.cognitive.microsoft.com/bing/v7.0/images/details`. Используйте эту конечную точку для запроса аналитических сведений об изображении вместо точки /images/search. Ознакомьтесь со статьей, посвященной [аналитическим сведениям об изображении](./image-insights.md). 
  
- Следующие параметры запроса теперь можно использовать только с конечной точкой `/images/details`:  
  
    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken)  
    -   [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modules)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#ct)  
  
- Объект `ImageInsightsResponse` переименован в [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  
  
- Изменены типы данных указанных ниже полей в объекте [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsights).  
  
    -   Тип поля `relatedCollections` изменен с `ImageGallery[]` на [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedcollectionsmodule).  
  
    -   Тип поля `pagesIncluding` изменен с `Image[]` на [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Тип поля `relatedSearches` изменен с `Query[]` на [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#relatedsearchesmodule).  
  
    -   Тип поля `recipes` изменен с `Recipe[]` на [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recipesmodule).  
  
    -   Тип поля `visuallySimilarImages` изменен с `Image[]` на [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Тип поля `visuallySimilarProducts` изменен с `ProductSummaryImage[]` на [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagesmodule).  
  
    -   Удален объект `ProductSummaryImage`, а связанные с продуктом поля перенесены в объект [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image). Объект `Image` включает в себя связанные с продуктом поля, только если изображение входит в состав визуально похожих продуктов в ответе с аналитическими сведениями об изображении.  
  
    -   Тип поля `recognizedEntityGroups` изменен с `RecognizedEntityGroup[]` на [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#recognizedentitiesmodule).  
  
-   Поле `categoryClassification` переименовано с [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) в `annotations`, а его тип изменен на `AnnotationsModule`.  

### <a name="images-answer"></a>Ответ при поиске изображений

-   Из объекта [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) удалены поля displayShoppingSourcesBadges и displayRecipeSourcesBadges.  
  
-   Поле `nextOffsetAddCount` объекта [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) переименовано в `nextOffset`. Также изменился способ использования смещения. Ранее параметру запроса [offset](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offset) необходимо было задавать значение `nextOffsetAddCount` + предыдущее значение смещения + число изображений в результатах. Теперь параметру `offset` задается значение `nextOffset`.  
    
  
## <a name="non-breaking-changes"></a>Некритические изменения

### <a name="query-parameters"></a>Параметры запроса
  
- В качестве возможного значения фильтра [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) добавлено значение Transparent (Прозрачный). Фильтр Transparent возвращает только изображения с прозрачным фоном.

- В качестве возможного значения фильтра [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) добавлено значение Any (Любой). Фильтр Any возвращает только изображения с лицензией.
  
- Добавлены параметры запроса [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxfilesize) и [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minfilesize). Используйте эти фильтры, чтобы возвращать изображения в определенном диапазоне размеров файлов.  
  
- Добавлены параметры запроса [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#maxwidth) и [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#minwidth). Используйте эти фильтры, чтобы возвращать изображения в определенном диапазоне высоты и ширины.  

### <a name="object-changes"></a>Изменения объектов
  
- В объект [Offer](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#offer) добавлены поля `description` и `lastUpdated`.  
  
- В объект [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagegallery) добавлено поле `name`.  
  
- В объект [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) добавлено поле `similarTerms`. В этом поле содержится список условий, которые на смыслу похожи на строку запроса пользователя.  
