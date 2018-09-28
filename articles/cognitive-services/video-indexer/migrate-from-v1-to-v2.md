---
title: Миграция с API Индексатора видео Azure версии 1 на версию 2
titlesuffix: Azure Cognitive Services
description: В этом разделе описывается переход с API Индексатора видео Azure версии 1 на версию 2.
services: cognitive services
author: juliako
manager: cgronlun
ms.service: cognitive-services
ms.component: video-indexer
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: juliako
ms.openlocfilehash: 3c70bbe11d94e6b03d615b8d1394ccdca6bd3790
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985634"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>Переход с API индексатора видео версии 1 на версию 2

> [!Note]
> 1 августа 2018 г. API Индексатора видео версии 1 был объявлен нерекомендуемым. Теперь следует использовать API Индексатора видео версии 2. <br/>См. [инструкции по разработке с помощью API Индексатора видео версии 2](https://api-portal.videoindexer.ai/). 

В этом разделе описываются изменения, реализованные в версии 2.  

## <a name="api-changes"></a>Изменения в API

### <a name="authorization-and-operations"></a>Авторизация и операции

В версии 2 в Индексаторе видео изменена модель проверки подлинности и авторизации API. Есть два набора API: 

* Авторизация 
* Операции

API **авторизации** используется для получения маркеров доступа для вызова API **операций**. API **операций** содержит все API Индексатора видео, такие как API отправки видео, получения аналитических сведений и других операций.

Оформив [подписку](video-indexer-get-started.md) на API **авторизации**, вы сможете получить маркеры доступа, передав ключ подписки (так же, как в версии 1.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Получение и использование маркера доступа для API-интерфейсов операций

При вызове API-интерфейсов **операций** ключ подписки больше использоваться не будет. Вместо этого вы передадите маркеры доступа, полученные API **авторизации**. 

Каждый запрос должен иметь допустимый маркер, соответствующий уровню доступа вызываемого API. Например, для операций на уровне пользователя (таких, как получение учетных записей) требуется маркер доступа пользователя. Для операций на уровне учетной записи (таких, как отображение всех видео в виде списка) требуется маркер доступа учетной записи. Для операций на уровне видео (таких, как повторная индексация видео) требуется маркер доступа учетной записи или маркер доступа видео.

Чтобы упростить задачу, выберите API **Authorization** > **GetAccounts**, чтобы получить список учетных записей без предварительного получения маркера доступа пользователя. Также можно отправить запрос на получение учетных записей с действующими маркерами, чтобы не выполнять дополнительный вызов на получение токена учетной записи.

Дополнительные сведения о различных маркерах доступа см. в разделе [Использование API Индексатора видео](video-indexer-use-apis.md).

### <a name="locations"></a>Расположения

Каждый вызов API должен содержать сведения о расположении вашей учетной записи Индексатора видео. Вызовы API без сведений о расположении или с неправильными сведениями о расположении завершаются ошибкой.

Применяются значения, описанные в таблице ниже. **Значение параметра** — это передаваемое значение при использовании API.

|**Имя**|**Значение параметра**|**Описание**|
|---|---|---|
|Пробная версия|trial|Используется для пробных учетных записей. Например, https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Запад США|westus2|Используется для региона Azure "Западная часть США 2".  Например, https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Северная Европа |northeurope|Используется для региона Azure "Северная Европа". Пример: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Восточная Азия|eastasia|Используется для региона Azure "Восточная Азия". Пример: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Модель данных

Индексатор видео теперь имеет упрощенную модель данных, что позволяет получать более понятные аналитические сведения. Дополнительные сведения о выходных данных, формируемых API версии 2, см. в руководстве по [анализу выходных данных Индексатора видео, полученных с помощью API версии 2](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

Определения API Индексатора видео обновлены соответствующим образом. Теперь они доступны для скачивания на [портале разработчика Индексатора видео](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>Примеры для сравнения версии 1 и версии 2

API-интерфейсы новой версии 2 включают три основных параметра:

1. [LOCATION] — как описано выше. Выберите одно из указанных значений: trial, westus2, northeurope или eastasia.
2. [YOUR_ACCOUNT_ID] — идентификатор GUID учетной записи. Предоставляется при получении всех учетных записей (см. ниже).
3. [YOUR_VIDEO_ID] — идентификатор видео (например, d4fa369abc). Возвращается при отправке или при поиске видео.

#### <a name="uploading-a-video-in-v1"></a>Отправка видео в версии 1

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>Отправка видео в версии 2

1. Получение маркера доступа для запроса на отправку

  Можно получить все учетные записи и их маркеры доступа:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Также можно получить маркер доступа для определенной учетной записи:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Отправка видео

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>Получение аналитических сведений в версии 1

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>Получение аналитических сведений в версии 2

1. Используйте маркер доступа учетной записи или получите маркер доступа уровня видео:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Получение аналитических сведений

  ```
  https://api.videoindexer.ai/[LOCATION]/Accounts[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>Получение состояния обработки видео в версии 1

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>Получение состояния обработки видео в версии 2

В API версии 2 состояние обработки возвращается как часть API получения индекса видео.

1. Используйте маркер доступа учетной записи или получите маркер доступа уровня видео:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Получение аналитических сведений

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>Дополнительная информация

[Использование API Индексатора видео](video-indexer-use-apis.md)

