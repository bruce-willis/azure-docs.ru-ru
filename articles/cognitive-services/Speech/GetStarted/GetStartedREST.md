---
title: Начало работы с API распознавания речи Microsoft с использованием REST | Документация Майкрософт
description: Использование REST для доступа к API распознавания речи в Microsoft Cognitive Services для преобразование устной речи в текст.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 53785cdfd75c23910802f2be20e6305817b3b097
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380604"
---
# <a name="get-started-with-speech-recognition-by-using-the-rest-api"></a>Начало работы с API распознавания речи с использованием REST

С помощью облачной службы речи можно разрабатывать приложения с помощью REST API для преобразования устной речи в текст.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="subscribe-to-the-speech-api-and-get-a-free-trial-subscription-key"></a>Подписка на API распознавания речи и получение ключа бесплатной пробной версии подписки

Speech API — элемент Cognitive Services (ранее Project Oxford). Вы можете получить ключи бесплатной пробной версии подписки на странице [подписки на Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Чтобы получить ключ, после выбора SAPI выберите **Получить API-ключ**. Страница возвращает первичный и вторичный ключ. Оба ключа привязаны к одной и той же квоте, поэтому вы можете использовать любой из них.

> [!IMPORTANT]
>* Получить ключ подписки. Чтобы получить доступ к REST API, необходим [ключ подписки](https://azure.microsoft.com/try/cognitive-services/).
>
>* Используйте свой ключ подписки В следующих образцах REST замените надпись YOUR_SUBSCRIPTION_KEY своим ключом подписки. 
>
>* Обратитесь к странице [Authenticate to the Speech API](../how-to/how-to-authentication.md) (Аутентификация в API речи) для получения ключа подписки.

### <a name="prerecorded-audio-file"></a>Предварительно записанный аудиофайл

В этом примере мы используем записанный звуковой файл, чтобы показать, как использовать REST API. Запишите аудио файл, где вы произносите короткую фразу. Например: "Какая сегодня погода?" или "Найди забавные фильмы". API распознавания речи также поддерживает внешний микрофонный вход.

> [!NOTE]
> Для примера требуется аудиозапись файла в формате WAV (**Один канал PCM (канал импульсно-кодовой модуляции), 16 кГц**).

## <a name="build-a-recognition-request-and-send-it-to-the-speech-recognition-service"></a>Создайте запрос распознавания и отправьте его в службу распознавания речи

Следующий шаг для распознавания речи — это отправить запрос POST для конечных точек HTTP речи с правильными заголовком и текстом.

### <a name="service-uri"></a>URI службы

Службу универсального кода ресурса (URI) распознавания речи определяют [режимы распознавания](../concepts.md#recognition-modes) и [языки распознавания](../concepts.md#recognition-languages):

```HTTP
https://speech.platform.bing.com/speech/recognition/<RECOGNITION_MODE>/cognitiveservices/v1?language=<LANGUAGE_TAG>&format=<OUTPUT_FORMAT>
```

Параметр `<RECOGNITION_MODE>` указывает режим распознавания и должен иметь одно из следующих значений: `interactive`, `conversation` или `dictation`. Это необходимый путь к ресурсу в универсальном коде ресурса (URI). Для получения дополнительных сведений см. раздел [Recognition modes](../concepts.md#recognition-modes) (Режимы распознавания).

`<LANGUAGE_TAG>` – обязательный параметр в строке запроса. Он определяет целевой язык преобразования аудио, например `en-US` для английского языка (США). Для получения дополнительных сведений см. раздел [Recognition languages](../concepts.md#recognition-languages) (Языки распознавания).

`<OUTPUT_FORMAT>` — необязательный параметр в строке запроса. Допустимые значения: `simple` и `detailed`. По умолчанию служба возвращает результаты в формате `simple`. Для получения дополнительных сведений см. раздел [Формат выходных данных](../concepts.md#output-format).

Некоторые примеры универсального кода ресурса (URI) службы перечислены в следующей таблице.

| Режим распознавания  | Язык | Формат выходных данных | URI службы |
|---|---|---|---|
| `interactive` | pt-BR | значение по умолчанию | https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=pt-BR |
| `conversation` | en-US | Подробно |https://speech.platform.bing.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed |
| `dictation` | fr-FR | Простая | https://speech.platform.bing.com/speech/recognition/dictation/cognitiveservices/v1?language=fr-FR&format=simple |

> [!NOTE]
> Служба универсального кода ресурса (URI) необходима только в том случае, когда приложение использует REST API для вызова службы распознавания речи. Если используется одна из [клиентских библиотек](GetStartedClientLibraries.md), нет необходимости знать, какая служба универсального кода ресурса (URI) используется. Клиентские библиотеки могут использовать различные службы универсального кода ресурса (URI), которые используются только для определенной клиентской библиотеки. Дополнительные сведения см. в документации к нужной клиентской библиотеке.

### <a name="request-headers"></a>Заголовки запросов

В заголовке запроса необходимо установить следующие поля.

- `Ocp-Apim-Subscription-Key`. Каждый раз при вызове службы в заголовок `Ocp-Apim-Subscription-Key` необходимо вводить ключ подписки. Служба речи также поддерживает маркеры авторизации вместо ключей подписки. Дополнительные сведения см. в разделе [Authenticate to the Speech API](../How-to/how-to-authentication.md) (Аутентификация в API речи).
- `Content-type`. Поле `Content-type` описывает формат и кодек звукового потока. В настоящее время поддерживаются только файлы формата WAV (Один канал PCM (канал импульсно-кодовой модуляции), 16 кГц). Значением параметра Content-type для данного формата будет `audio/wav; codec=audio/pcm; samplerate=16000`.

Поле `Transfer-Encoding` является необязательным. Если вы зададите значение `chunked` для этого поля, то сможете разделить звук на мелкие блоки. Для получения дополнительных сведений см. раздел [Chunked transfer encoding](../How-to/how-to-chunked-transfer.md) (Поблочная передача кодировки).

Ниже приведен пример заголовка запроса.

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="send-a-request-to-the-service"></a>Отправка запроса к службе

В следующем примере показано, как отправить запрос распознавания речи в конечные точки REST речи. Здесь используется режим распознавания `interactive`.

> [!NOTE]
> Замените `YOUR_AUDIO_FILE` путем к предварительно записанному звуковому файлу. Замените `YOUR_SUBSCRIPTION_KEY` фактическим ключом подписки.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authorization token returned by the token service.
$RecoRequestHeader = @{
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY';
  'Transfer-Encoding' = 'chunked';
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

В этом примере используется curl под ОС Linux с помощью Bash. Если на вашей платформе нет служебной программы curl, установите ее. Этот пример будет работать также в Cygwin под ОС Windows, Git Bash, zsh и других оболочках.

> [!NOTE]
> Сохраните `@` перед именем аудиофайла при замене `YOUR_AUDIO_FILE` на путь к предварительно записанному аудиофайлу, так как он указывает, что значение `--data-binary` — это имя файла, а не данные.

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

// Send an audio file by 1024 byte chunks
using (FileStream fs = new FileStream(YOUR_AUDIO_FILE, FileMode.Open, FileAccess.Read))
{

    /*
    * Open a request stream and write 1024 byte chunks in the stream one at a time.
    */
    byte[] buffer = null;
    int bytesRead = 0;
    using (Stream requestStream = request.GetRequestStream())
    {
        /*
        * Read 1024 raw bytes from the input audio file.
        */
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        // Flush
        requestStream.Flush();
    }
}
```

---

## <a name="process-the-speech-recognition-response"></a>Обработка ответа распознавания речи

После обработки запроса служба речи возвращает результаты ответа в формате JSON.

> [!NOTE]
> Если предыдущий код возвращает ошибку, см. раздел [Troubleshooting](../troubleshooting.md) (Устранение неполадок) для обнаружения возможных причин.

Следующий фрагмент кода — пример того, как можно прочитать ответ из потока.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell
# show the response in JSON format
ConvertTo-Json $RecoResponse
```

# <a name="curltabcurl"></a>[curl](#tab/curl)

В этом примере curl тут же возвращает ответное сообщение в строке. Если необходимо показать его в формате JSON, можно использовать дополнительные инструменты, например jq.

```
curl -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE | jq
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

```cs
/*
* Get the response from the service.
*/
Console.WriteLine("Response:");
using (WebResponse response = request.GetResponse())
{
    Console.WriteLine(((HttpWebResponse)response).StatusCode);

    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    {
        responseString = sr.ReadToEnd();
    }

    Console.WriteLine(responseString);
    Console.ReadLine();
}
```

---

В следующем примере показан ответ в формате JSON.

```json
OK
{
  "RecognitionStatus": "Success",
  "Offset": 22500000,
  "Duration": 21000000,
  "NBest": [{
    "Confidence": 0.941552162,
    "Lexical": "find a funny movie to watch",
    "ITN": "find a funny movie to watch",
    "MaskedITN": "find a funny movie to watch",
    "Display": "Find a funny movie to watch."
  }]
}
```

## <a name="limitations"></a>Ограничения

REST API имеет некоторые ограничения.

- Он поддерживает аудиопотоки, длина которых не более 15 секунд.
- Он не поддерживает промежуточные результаты во время распознавания. Пользователи получают только окончательный результат распознавания.

Чтобы снять эти ограничения, используйте [клиентские библиотеки](GetStartedClientLibraries.md) речи. Или можете напрямую использовать [протокол речи WebSocket](../API-Reference-REST/websocketprotocol.md).

## <a name="whats-next"></a>Что дальше?

- Чтобы узнать, как использовать REST API в C#, Java, т. д., см. эти [образцы приложений](../samples.md).
- Чтобы найти и исправить ошибки, см. раздел [Troubleshooting](../troubleshooting.md) (Устранение неполадок).
- Чтобы использовать дополнительные функции, ознакомьтесь с кратким руководством по использованию [клиентских библиотек](GetStartedClientLibraries.md) речи.

### <a name="license"></a>Лицензия

Все пакеты SDK и образцы Cognitive Services лицензированы лицензией MIT. Дополнительные сведения можно найти в разделе [Лицензия](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
