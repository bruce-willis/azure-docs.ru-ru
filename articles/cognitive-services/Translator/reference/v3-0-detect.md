---
title: Метод Detect в API перевода текстов версии 3 от Майкрософт | Документация Майкрософт
description: Использование метода Detect в API перевода текстов от Майкрософт.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 7e81e91230e1ada4423d77d22134b1b64df65d9d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382405"
---
# <a name="text-api-30-detect"></a>API перевода текстов 3.0: Detect

Определяет язык для фрагмента текста.

## <a name="request-url"></a>Request URL (URL-адрес запроса)

Отправьте запрос `POST` на адрес:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Параметры запроса

В этой строке запроса передаются следующие параметры запроса.

<table width="100%">
  <th width="20%">Параметр запроса</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>api-version</td>
    <td>*Обязательный параметр*.<br/>Версия API, запрошенная клиентом. Должно быть такое значение: `3.0`.</td>
  </tr>
</table> 

Заголовки запроса:

<table width="100%">
  <th width="20%">Заголовки</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>_Один заголовок_<br/>_авторизации_</td>
    <td>*Обязательный заголовок запроса*.<br/>См. [описание доступных параметров для аутентификации](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Обязательный заголовок запроса*.<br/>Указывает тип содержимого для полезных данных. Возможные значения: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Обязательный заголовок запроса*.<br/>Длина текста запроса.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Необязательный*.<br/>Созданный клиентом идентификатор GUID, позволяющий уникально идентифицировать запрос. Обратите внимание, что этот заголовок можно опустить, если в строке запроса указан идентификатор трассировки в параметре с именем `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Тело запроса

Текст запроса является массивом в формате JSON. Каждый элемент этого массива представляет собой объект JSON со строковым свойством `Text`. Определение языка применяется к значению свойства `Text`. Ниже представлен пример текста запроса.

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Действительны следующие ограничения.

* Массив может содержать не более 100 элементов.
* Длина текстового значения в одном элементе массива не может превышать 10 000 символов, включая пробелы.
* Общий объем текста запроса не может превышать 50 000 символов, включая пробелы.

## <a name="response-body"></a>Тело ответа

Успешный ответ возвращается в формате массива JSON с одним результатом для каждой строки входного массива. Объект результатов содержит следующие свойства:

  * `language` — код обнаруженного языка.

  * `score` — значение типа float, обозначающее достоверность результата. Может принимать ноль или единицу, где низкая оценка обозначает низкую достоверность.

  * `isTranslationSupported` — логическое значение, которое имеет значение True, если обнаруженный язык поддерживается для перевода текста.

  * `isTransliterationSupported` — логическое значение, которое имеет значение True, если обнаруженный язык поддерживается для транслитерации.
  
  * `alternatives` — массив других возможных языков. Каждый элемент этого массива содержит другой объект с такими же свойствами, как перечислено выше: `language`, `score`, `isTranslationSupported` и `isTransliterationSupported`.

Пример ответа в формате JSON:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Заголовки ответов

<table width="100%">
  <th width="20%">Заголовки</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>X-RequestId</td>
    <td>Сформированное службой значение для идентификации запроса. Оно используется для устранения неполадок.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Коды состояния ответа

Ниже приведены возможные коды состояния HTTP, которые возвращает запрос. 

<table width="100%">
  <th width="20%">Код состояния</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>200</td>
    <td>Успешно.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Один из параметров запроса отсутствует или является недопустимым. Исправьте параметры запроса и повторите попытку.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Не удалось выполнить аутентификацию запроса. Убедитесь, что указаны допустимые учетные данные.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Запрос не авторизован. Подробные сведения можно найти в сообщении об ошибке. Чаще всего это означает, что достигнут лимит бесплатных операций перевода, включенных в пробную подписку.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Вызывающая сторона отправляет слишком много запросов.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Произошла непредвиденная ошибка. Если ошибка не устранена, сообщите о ней, включив следующие данные: дата и время сбоя, идентификатор запроса из заголовка ответа `X-RequestId` и идентификатор клиента из заголовка запроса `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Сервер временно недоступен. Повторите запрос. Если ошибка не устранена, сообщите о ней, включив следующие данные: дата и время сбоя, идентификатор запроса из заголовка ответа `X-RequestId` и идентификатор клиента из заголовка запроса `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Примеры

В следующем примере показано, как получить список языков, поддерживаемых для перевода текста.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```

---
