---
title: Метод Examples в API перевода текстов корпорации Майкрософт | Документация Майкрософт
description: Использование метода Examples в API перевода текстов корпорации Майкрософт.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 9960f3be42090edaec1df935d70e4c1a0d25b691
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382409"
---
# <a name="text-api-30-dictionary-examples"></a>API перевода текстов 3.0: Dictionary Examples (примеры из словаря)

Этот метод предоставляет контекстные примеры использования термина, включенного в словарь. Эта операция используется в сочетании с [поиском по словарю](.\v3-0-dictionary-lookup.md).

## <a name="request-url"></a>Request URL (URL-адрес запроса)

Отправьте запрос `POST` на адрес:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Параметры запроса

В этой строке запроса передаются следующие параметры запроса.

<table width="100%">
  <th width="20%">Параметр запроса</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>api-version</td>
    <td>*Обязательный параметр*.<br/>Версия API, запрошенная клиентом. Значение должно быть `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Обязательный параметр*.<br/>Определяет язык вводимого текста. Целевой язык должен быть одним из [поддерживаемых языков](.\v3-0-languages.md) включенных в область `dictionary`.</td>
  </tr>
  <tr>
    <td>значение</td>
    <td>*Обязательный параметр*.<br/>Определяет язык выходного текста. Целевой язык должен быть одним из [поддерживаемых языков](.\v3-0-languages.md) включенных в область `dictionary`.</td>
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
    <td>*Необязательный*.<br/>Созданный клиентом идентификатор GUID, позволяющий уникально идентифицировать запрос. Этот заголовок можно опустить, если в строке запроса указан идентификатор трассировки в параметре с именем `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Тело запроса

Текст запроса является массивом в формате JSON. Каждый элемент этого массива представляет собой объект JSON со следующими свойствами.

  * `Text` — строка с термином, который нужно найти. Здесь должно передаваться значение из поля `normalizedText`, полученного в обратном переводе по предшествующему запросу [поиска по словарю](.\v3-0-dictionary-lookup.md). Также это может быть значение поля `normalizedSource`.

  * `Translation` — строка, содержащая переведенный текст, возвращенный предшествующей операцией [поиска по словарю](.\v3-0-dictionary-lookup.md). Здесь должно передаваться значение из поля `normalizedTarget`, полученного в списке `translations` по предшествующему запросу [поиска по словарю](.\v3-0-dictionary-lookup.md). Служба возвращает примеры для конкретной пары слов на языках оригинала и перевода.

Пример:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Действительны следующие ограничения.

* Массив может содержать не более 10 элементов.
* Длина текстового значения в одном элементе массива не может превышать 100 символов, включая пробелы.

## <a name="response-body"></a>Тело ответа

Успешный ответ возвращается в формате массива JSON с одним результатом для каждого элемента входного массива. Объект результатов содержит следующие свойства.

  * `normalizedSource` — строка, задающая нормализованную форму термина в оригинале. Обычно это значение должно совпадать со значением поля `Text` для соответствующего элемента в теле запроса.
    
  * `normalizedTarget` — строка, задающая нормализованную форму термина в переводе. Обычно это значение должно совпадать со значением поля `Translation` для соответствующего элемента в теле запроса.
  
  * `examples` — список примеров для пары терминов в оригинале и в переводе. Каждый элемент этого списка — это объект со следующими свойствами:

    * `sourcePrefix` — строка, которую нужно добавить _перед_ значением `sourceTerm` для создания полного примера. Не добавляйте символ пробела, так как он уже расположен в нужном месте. Это значение может быть пустой строкой.

    * `sourceTerm` — строка, совпадающая с термином, по которому выполнялся поиск. Эта строка объединяется с `sourcePrefix` и `sourceSuffix` для создания полного примера. Это значение выделяется для того, чтобы его было удобно отмечать в пользовательском интерфейсе, например, полужирным шрифтом.

    * `sourceSuffix` — строка, которую нужно добавить _после_ значения `sourceTerm` для создания полного примера. Не добавляйте символ пробела, так как он уже расположен в нужном месте. Это значение может быть пустой строкой.

    * `targetPrefix`: — строка, аналогичная `sourcePrefix`, но для языка перевода.

    * `targetTerm`: — строка, аналогичная `sourceTerm`, но для языка перевода.

    * `targetSuffix`: — строка, аналогичная `sourceSuffix`, но для языка перевода.

    > [!NOTE]
    > Если в словаре нет нужных примеров, возвращается ответ 200 (ОК) с пустым списком в параметре `examples`.

## <a name="examples"></a>Примеры

В этом примере показано, как найти примеры для конкретной пары терминов: английский оригинал `fly` и испанский перевод `volar`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Текст ответа (сокращен для ясности):

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
