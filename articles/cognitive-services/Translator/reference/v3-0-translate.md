---
title: Метод Translate в API перевода текстов Microsoft | Документация Майкрософт
titleSuffix: Cognitive Services
description: Использование метода Translate в API перевода текстов Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.technology: microsoft translator
ms.topic: article
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: d8d5e1e2fac747fa733f1d92c08008b7eac2a1bc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382852"
---
# <a name="text-api-30-translate"></a>API перевода текстов 3.0

Этот интерфейс позволяет переводить текст.

## <a name="request-url"></a>Request URL (URL-адрес запроса)

Отправьте запрос `POST` на следующий адрес.

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Параметры запроса

В таблице ниже приведены параметры, которые передаются в строке запроса.

<table width="100%">
  <th width="20%">Параметр запроса</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>api-version</td>
    <td>*Обязательный параметр.*<br/>Версия API, запрошенная клиентом. Необходимое значение: `3.0`.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Необязательный параметр.*<br/>Определяет язык оригинального текста. Чтобы просмотреть, какие языки доступны для перевода, выполните поиск [поддерживаемых языков](.\v3-0-languages.md), используя область `translation`. Если параметр `from` не указан, исходный язык определяется автоматически.</td>
  </tr>
  <tr>
    <td>значение</td>
    <td>*Обязательный параметр.*<br/>Определяет язык выходного текста. Целевой язык должен быть одним из [поддерживаемых языков](.\v3-0-languages.md), включенных в область `translation`. Например, используйте параметр `to=de`, чтобы перевести на немецкий.<br/>Вы можете одновременно переводить на различные языки, использовав этот параметр в строке запроса несколько раз. Например, используйте параметр `to=de&to=it`, чтобы перевести на немецкий и итальянский.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td>*Необязательный параметр.*<br/>Определяет, является ли текст перевода обычным или HTML-текстом. Любой код HTML должен быть полным элементом с правильным форматом. Возможные значения: `plain` (по умолчанию) или `html`.</td>
  </tr>
  <tr>
    <td>category</td>
    <td>*Необязательный параметр.*<br/>Строка, где указано категорию (домен) перевода. Этот параметр позволяет получить переводы из пользовательской системы, созданной с помощью [Custom Translator](../customization.md). Значение по умолчанию: `general`.</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td>*Необязательный параметр.*<br/>Указывает способ обработки нецензурной лексики в переводе. Возможные значения: `NoAction` (по умолчанию), `Marked` или `Deleted`. Способы работы с нецензурной лексикой см. в [этом разделе](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td>*Необязательный параметр.*<br/>Указывает, каким образом нецензурная лексика должна помечаться в переводе. Возможные значения: `Asterisk` (по умолчанию) или `Tag`. Способы работы с нецензурной лексикой см. в [этом разделе](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td>*Необязательный параметр.*<br/>Указывает, следует ли применять выравнивание из оригинального текста в переводе. Возможные значения: `true` или `false` (по умолчанию). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td>*Необязательный параметр.*<br/>Указывает, следует ли включать границы предложения оригинального и переведенного текста. Возможные значения: `true` или `false` (по умолчанию).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td>*Необязательный параметр.*<br/>Указывает автоматический язык, если не удается определить язык оригинального текста. Если параметр не указан `from`, язык определяется автоматически. Если обнаружение завершается сбоем, используется язык `suggestedFrom`.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Необязательный параметр.*<br/>Указывает сценарий оригинального текста.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Необязательный параметр.*<br/>Указывает скрипт переведенного текста.</td>
  </tr>
</table> 

Заголовки запроса:

<table width="100%">
  <th width="20%">Заголовки</th>
  <th>ОПИСАНИЕ</th>
  <tr>
    <td>_Один заголовок_<br/>_авторизации_</td>
    <td>*Обязательный заголовок запроса*.<br/>См. [описание доступных способов аутентификации](./v3-0-reference.md#authentication).</td>
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

Текст запроса является массивом в формате JSON. Каждый элемент этого массива представляет собой объект JSON со строковым свойством `Text`, который являет собой строку для перевода.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Действительны следующие ограничения.

* Массив может содержать не более 25 элементов.
* Общий объем текста запроса не должен превышать 5000 символов, в том числе пробелы.

## <a name="response-body"></a>Тело ответа

Успешный ответ возвращается в формате массива JSON с одним результатом для каждой строки входного массива. Объект результата содержит следующие свойства.

  * `detectedLanguage` — объект, описывающий распознанный язык с помощью следующих свойств:

      * `language` — строку, которая представляет код обнаруженного языка.

      * `score` — значение с плавающей запятой, обозначающее достоверность результата. Может принимать ноль или единицу, где низкая оценка обозначает низкую достоверность.

    Свойство `detectedLanguage` присутствует в объекте результатов только при запросе автоматического распознавания языка.

  * `translations` — массив результатов перевода. Размер массива совпадает с количеством языков, указанных с помощью параметра запроса `to`. Каждый элемент массива содержит:

    * `to` — строка, которая содержит код целевого языка.

    * `text` — строка с текстом перевода.

    * `transliteration` — объект, который возвращает переведенный текст в сценарии, указанном в параметре `toScript`.

      * `script` — строка, которая указывает целевой сценарий.   

      * `text` — строка, которая возвращает целевой текст в целевом сценарии.

    Если не выполняется транслитерация, объект `transliteration` не включается.

    * `alignment` — объект с одним свойством строки `proj`, который преобразовывает оригинальный текст в переведенный. Сведения о выравнивании предоставляются, только когда параметр запроса `includeAlignment` имеет значение `true`. Сведения о выравнивании возвращаются в виде строкового значения в следующем формате: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Двоеточие разделяет начальный и конечный индексы, дефис — языки, а пробел — слова. Одно слово может соответствовать нулю, одному или нескольким словам другого языка. При этом сопоставленные слова могут не располагаться рядом. Если сведения о выравнивании недоступны, элемент Alignment будет пустым. Примеры и ограничения см. в разделе [Получение сведений о выравнивании](#obtain-alignment-information).

    * `sentLen` — объект, возвращающий границы предложения в оригинальном и переведенном текстах.

      * `srcSentLen` — массив целых чисел, представляющих значения длины предложений в оригинальном тексте. Длина массива соответствует количеству предложений, а значения — длине каждого предложения.

      * `transSentLen` — массив целых чисел, представляющих значения длины предложений в переведенном тексте. Длина массива соответствует количеству предложений, а значения — длине каждого предложения.

    Границы предложения включены только тогда, когда параметр запроса `includeSentenceLength` имеет значение `true`.

  * `sourceText` — объект с одним свойством строки `text`, который возвращает оригинальный текст в сценарии по умолчанию исходного языка. `sourceText` свойство присутствует только в том случае, если входные данные выражаются в сценарии, который не является обычным для этого языка. Например, если входные данные были на арабском языке, но написаны латинским алфавитом, тогда параметр `sourceText.text` вернет текст на арабском языке, преобразованным в арабский сценарий.

Примеры ответов JSON приведены в [этом разделе](#examples).

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
    <td>Один из параметров запроса отсутствует или имеет недопустимое значение. Исправьте параметры запроса и повторите попытку.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Не удалось выполнить проверку подлинности запроса. Убедитесь, что указаны допустимые учетные данные.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>Запрос не авторизован. Подробности можно проверить в сообщении об ошибке. Чаще всего это означает, что достигнут лимит бесплатных переводов, включенных в пробную подписку.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Вызывающая сторона отправляет слишком много запросов.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Произошла непредвиденная ошибка. Если ошибка сохраняется, передайте отчет о ней, включив следующие данные: дата и время сбоя, идентификатор запроса из заголовка ответа `X-RequestId` и идентификатор клиента из заголовка запроса `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Сервер временно недоступен. Повторите запрос. Если ошибка сохраняется, передайте отчет о ней, включив следующие данные: дата и время сбоя, идентификатор запроса из заголовка ответа `X-RequestId` и идентификатор клиента из заголовка запроса `X-ClientTraceId`.</td>
  </tr>
</table> 

## <a name="examples"></a>Примеры

### <a name="translate-a-single-input"></a>Перевод отдельного элемента

В этом примере показано, как перевести одно предложение с английского языка на упрощенный китайский.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Текст ответа:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

Массив `translations` содержит один элемент, который обеспечивает перевод одного элемента текста в оригинальных данных.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Перевод отдельного элемента с использованием автоматического определения языка

В этом примере показано, как перевести одно предложение с английского языка на упрощенный китайский. В запросе не указан язык ввода. Вместо этого используется автоматическое определение исходного языка.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Текст ответа:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
Ответ похож на ответ из предыдущего примера. Так как было запрошено автоматическое определение языка, ответ также включает сведения о языке оригинального текста. 

### <a name="translate-with-transliteration"></a>Перевод методом транслитерации

Давайте расширим предыдущий пример, добавив метод транслитерации. В следующем запросе используется китайский текст, написанный латинским алфавитом.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Текст ответа:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"text":"nǐ hǎo , nǐ jiào shén me míng zì ？","script":"Latn"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

Результат перевода теперь включает свойство `transliteration`, которое возвращает переведенный текст, написанный символами латиницы.

### <a name="translate-multiple-pieces-of-text"></a>Перевод нескольких фрагментов текста

Одновременный перевод нескольких строк — это просто вопрос задания массива строк в тексте запроса.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

Текст ответа:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Перевод на несколько языков

В этом примере показано, как перевести одинаковый оригинальный текст на несколько языков в одном запросе.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

Текст ответа:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Работа с нецензурной лексикой

Обычно служба переводов сохраняет в переводе ненормативную лексику, которая присутствует в источнике. Степень нецензурности и оскорбительность контекста отличаются в разных культурах и в результате этого степень нецензурности в переводе может вырасти или уменьшится.

Если необходимо избежать появления ненормативной лексики при переводе (независимо от наличия ненормативной лексики в источнике), можно использовать параметр фильтрации ненормативной лексики. Этот параметр позволяет выбрать, что нужно делать с нецензурной лексикой: удалить, пометить ее соответствующими тегами (что дает возможность добавить собственный метод постобработки) или ничего не предпринимать. Допустимые значения `ProfanityAction`: `Deleted` , `Marked` и `NoAction`(по умолчанию).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Действие</th>
  <tr>
    <td>`NoAction`</td>
    <td>Это поведение по умолчанию. Ненормативная лексика оригинального текста сохранится в переводе.<br/><br/>
    **Пример исходного текста (японский).** 彼はジャッカスです。<br/>
    **Пример перевода (на русский).** Он — придурок.
    </td>
  </tr>
  <tr>
    <td>`Deleted`</td>
    <td>Оскорбительные слова будут удалены из выходных данных без замены.<br/><br/>
    **Пример исходного текста (японский).** 彼はジャッカスです。<br/>
    **Пример перевода (на русский)**: Он —.
    </td>
  </tr>
  <tr>
    <td>`Marked`</td>
    <td>Нецензурную лексику в выходных данных заменяют маркеры. Маркер зависит от параметра `ProfanityMarker`.<br/><br/>
Если установлен параметр `ProfanityMarker=Asterisk`, нецензурную лексику заменяют символы `***`:<br/>
    **Пример исходного текста (японский).** 彼はジャッカスです。<br/>
    **Пример перевода (на русский)**: Он – \*\*\*.<br/><br/>
Если установлен параметр `ProfanityMarker=Tag`, нецензурная лексика выделяется XML-тегами &lt;profanity&gt; и &lt;/profanity/&gt;:<br/>
    **Пример исходного текста (японский).** 彼はジャッカスです。<br/>
    **Пример перевода (на русский).** Он – &lt;profanity&gt;придурок&lt;/profanity&gt;.
  </tr>
</table> 

Например: 

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Возвращаемые данные:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Сравнение:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Последний запрос возвращает:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Перевод содержимого с исправлениями и определение переведенного текста

Чаще всего переводится содержимое, которое включает исправления, например содержимое HTML-страниц или XML-документа. Включите параметр запроса `textType=html` при переводе содержимого с тегами. Кроме того, иногда бывает полезно исключить из перевода конкретное содержимое. С помощью атрибута `class=notranslate` можно указать содержимое, которое должно остаться не переведенным. В следующем примере содержимое внутри первого элемента `div` не будет переведено, в то время как содержимое во втором элементе `div` будет переведено.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Пример запроса приведен ниже.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

Ответ:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Получить сведения о выравнивании

Чтобы получить сведения о выравнивании, укажите параметр `includeAlignment=true` в строке запроса.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

Ответ:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

Сведения о выравнивании начинаются со строки `0:2-0:1`. Это значит, что первые три символа в исходном тексте (`The`) сопоставляются с двумя первыми символами в переведенном тексте (`La`).

Обратите внимание на следующие ограничения:

* Сведения о выравнивании возвращаются только для группы языковых пар:
  - с английского на любой другой язык;
  - с любого другого языка на английский, за исключением китайского упрощенного, китайского традиционного и латышского на английский;
  - с японского на корейский или с корейского на японский.
* Вы не получите сведения о выравнивании, если предложение является законченным переводом. Примеры таких переводов — "Это тест", "Я люблю тебя" и другие предложения, встречающиеся с высокой частотой.

### <a name="obtain-sentence-boundaries"></a>Установка границ предложения

Чтобы получать сведения о длине предложения в исходном тексте и переводе, укажите параметр `includeSentenceLength=true` в строке запроса.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

Ответ:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Перевод с помощью динамического словаря

Если вы уже знаете перевод, который хотите применить к слову или фразе, вы можете указать его в запросе как исправление. Динамический словарь безопасен только для составных существительных, таких как имена собственные и названия продуктов.

В разметке используется следующий синтаксис:

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Например, рассмотрим следующее русское предложение: "Слово "словоматик" — это словарная запись". Чтобы сохранить при переводе слово _словоматик_, необходимо отправить запрос:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

Результат:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Эта возможность работает одинаково как с `textType=text`, так и с `textType=html`. Компонент должен использоваться только в случае необходимости. Соответствующий и гораздо лучший способ настройки перевода — это использование концентратора Custom Translator. Custom Translator обеспечивает полное использование контекста и статистические значения вероятности. Если вы имеете или можете позволить себе создавать учебные данные, которые показывают вашу работу или фразу в контексте, вы получите гораздо лучшие результаты. [Узнайте больше о Custom Translator](../customization.md).
 





