---
title: Метод поиска по словарю API перевода текстов от корпорации Майкрософт | Документация Майкрософт
description: Использование метода поиска по словарю в API перевода текстов от корпорации Майкрософт.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 5a186f60dc099b095c00056d965aa92618c2c708
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868091"
---
# <a name="text-api-30-dictionary-lookup"></a>API перевода текстов 3.0. Поиск по словарю

Предоставляет альтернативные переводы слова и небольших идиоматических выражений. Каждый перевод содержит части речи и список обратных переводов. Обратные переводы позволяют пользователю понять перевод в контексте. Операция [Dictionary Example](.\v3-0-dictionary-examples.md) позволяет продолжить детализацию, чтобы увидеть пример использования каждой пары переводов.

## <a name="request-url"></a>Request URL (URL-адрес запроса)

Отправьте запрос `POST` по адресу:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0
```

## <a name="request-parameters"></a>Параметры запроса

В этой строке запроса передаются следующие параметры запроса:

<table width="100%">
  <th width="20%">Параметр запроса</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>api-version</td>
    <td>*Обязательный параметр*.<br/>Версия API, запрошенная клиентом. Этот параметр должен содержать значение `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Обязательный параметр*.<br/>Определяет язык вводимого текста. Исходный язык должен быть одним из [поддерживаемых языков](.\v3-0-languages.md), включенных в область `dictionary`.</td>
  </tr>
  <tr>
    <td>значение</td>
    <td>*Обязательный параметр*.<br/>Определяет язык выходного текста. Целевой язык должен быть одним из [поддерживаемых языков](.\v3-0-languages.md), включенных в область `dictionary`.</td>
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

Текст запроса является массивом в формате JSON. Каждый элемент этого массива представляет собой объект JSON со строковым свойством `Text`, который соответствует термину для поиска.

```json
[
    {"Text":"fly"}
]
```

Действительны следующие ограничения.

* Массив может содержать не более 10 элементов.
* Длина текстового значения в одном элементе массива не может превышать 100 символов, включая пробелы.

## <a name="response-body"></a>Тело ответа

Успешный ответ возвращается в формате массива JSON с одним результатом для каждого элемента входного массива. Объект результатов содержит следующие свойства.

  * `normalizedSource`. Строка, задающая нормализованную форму исходного термина. Например если запросом является "JOHN", нормализованной формой будет "john". Содержимое этого поля становится входными данными для [примеров поиска](.\v3-0-dictionary-examples.md).
    
  * `displaySource`. Строка, задающая исходный термин в форме, наиболее подходящем для отображения конечному пользователю. Например, если входное значение "JOHN", форма просмотра будет отражать обычное правописание имени: "John". 

  * `translations`. Список переводов для исходного термина. Каждый элемент списка — это объект со следующими свойствами:

    * `normalizedTarget`. Строка, задающая нормализованную форму этого термина в целевом языке. Это значение должно использоваться как входные данные для [примеров поиска](.\v3-0-dictionary-examples.md).

    * `displayTarget`. Строка, задающая термин целевого языка в форме, наиболее подходящей для отображения конечному пользователю. Как правило, это будет отличаться от `normalizedTarget` с учетом регистра букв. Например, имя собственное, такое как "Juan" будет иметь `normalizedTarget = "juan"` и `displayTarget = "Juan"`.

    * `posTag`. Строка, связывающая этот термин с тегом части речи.

        | Имя тега | ОПИСАНИЕ  |
        |----------|--------------|
        | ADJ      | Прилагательные   |
        | ADV      | Наречия      |
        | CONJ     | Союзы |
        | DET      | Определители  |
        | MODAL    | Команды        |
        | NOUN     | Существительные        |
        | PREP     | Предлоги |
        | PRON     | Местоимения     |
        | VERB     | Команды        |
        | OTHER    | Другие        |

        В качестве примечания о внедрении эти теги были определены с помощью тегов частей речи, помеченных английской стороной, а затем выполнялся наиболее частый тег для каждой исходной или целевой пары. Поэтому, если пользователи часто переводят испанское слово на английский язык с тегом другой части речи, теги могут оказаться неправильными (в отношении испанского слова).

    * `confidence`. Значение в диапазоне от 0,0 до 1,0, которое представляет "достоверность" (или, точнее, "вероятность в данных обучения") этой пары переводов. Сумма оценок достоверности для одного исходного слова может равняться или не равняться 1,0. 

    * `prefixWord`. Строка, предоставляющая слово для отображения в качестве префикса перевода. В настоящее время это гендерный определитель существительных на языках с гендерными детерминантами. Например, префиксом испанского слова "mosca" является "la", поскольку "mosca" на испанском языке — это женское имя существительное. Это зависит только от перевода, а не от источника. Если префикс отсутствует, это будет пустая строка.
    
    * `backTranslations`. Список обратных переводов целевого объекта. Например, исходные слова, на которые может быть переведен целевой объект. Список, как правило, содержит искомое слово, которое было запрошено (например, если искомое слово будет "fly", тогда гарантируется, что "fly" будет в списке `backTranslations`). Однако оно не обязательно будет находиться на первом месте. Каждый элемент списка `backTranslations` — это объект, описанный следующими свойствами:

        * `normalizedText`. Строка, задающая нормализованную форму исходного термина, который представляет собой обратный перевод целевого объекта. Это значение должно использоваться как входные данные для [примеров поиска](.\v3-0-dictionary-examples.md).        

        * `displayText`. Строка, задающая исходный термин, который представляет собой обратный перевод целевого объекта в форме, наиболее подходящей для отображения конечному пользователю.

        * `numExamples`. Целое число, представляющее собой количество доступных примеров для данной пары переводов. Фактические примеры должны быть получены с помощью отдельного вызова [примеров поиска](.\v3-0-dictionary-examples.md). Номер предназначен главным образом для облегчения отображения в UX. Например, пользовательский интерфейс может добавить гиперссылку на обратный перевод, если число примеров больше нуля, и показать обратный перевод как обычный текст, если примеров нет. Обратите внимание, что фактическое количество примеров, возвращаемых вызовом [примеров поиска](.\v3-0-dictionary-examples.md), может быть меньше `numExamples`, так как дополнительная фильтрация может применяться на лету для удаления плохих примеров.
        
        * `frequencyCount`. Целое число, представляющее частоту этой пары переводов в данных. Основное назначение этого поля — предоставить пользовательский интерфейс со средствами сортировки обратных переводов, поэтому наиболее частыми являются первые условия.

    > [!NOTE]
    > Если в словаре нет искомого слова, ответ будет равен 200 (ОК), а список `translations` будет пустым.

## <a name="examples"></a>Примеры

В этом примере показано, как искать альтернативные переводы английского термина на испанском языке `fly`.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly'}]"
```

---

Текст ответа (сокращен для ясности):

```
[
    {
        "normalizedSource":"fly",
        "displaySource":"fly",
        "translations":[
            {
                "normalizedTarget":"volar",
                "displayTarget":"volar",
                "posTag":"VERB",
                "confidence":0.4081,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":4637},
                    {"normalizedText":"flying","displayText":"flying","numExamples":15,"frequencyCount":1365},
                    {"normalizedText":"blow","displayText":"blow","numExamples":15,"frequencyCount":503},
                    {"normalizedText":"flight","displayText":"flight","numExamples":15,"frequencyCount":135}
                ]
            },
            {
                "normalizedTarget":"mosca",
                "displayTarget":"mosca",
                "posTag":"NOUN",
                "confidence":0.2668,
                "prefixWord":"",
                "backTranslations":[
                    {"normalizedText":"fly","displayText":"fly","numExamples":15,"frequencyCount":1697},
                    {"normalizedText":"flyweight","displayText":"flyweight","numExamples":0,"frequencyCount":48},
                    {"normalizedText":"flies","displayText":"flies","numExamples":9,"frequencyCount":34}
                ]
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```

В этом примере показано, что происходит, когда искомый термин не существует для действительной пары словаря.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/lookup?api-version=3.0&from=en&to=es" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly123456'}]"
```

---

Так как этот термин не найден в словаре, текст ответа включает пустой список `translations`.

```
[
    {
        "normalizedSource":"fly123456",
        "displaySource":"fly123456",
        "translations":[]
    }
]
```
