---
title: REST API службы распознавания речи
description: Справочник по REST API службы распознавания речи.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: 6758cd658daf75beeea93bf9c719508cd271c8be
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032433"
---
# <a name="speech-service-rest-apis"></a>REST API службы распознавания речи

REST API единой службы распознавания речи Azure Cognitive Services похожи на API, предоставляемые функцией [API распознавания речи Bing](https://docs.microsoft.com/azure/cognitive-services/Speech). Конечные точки отличаются от конечных точек, используемых службой "Распознавание речи Bing". Региональные конечные точки доступны, и необходимо использовать ключ подписки, который соответствует используемой конечной точке.

## <a name="speech-to-text"></a>Преобразование речи в текст

В следующей таблице показаны конечные точки для REST API преобразования речи в текст. Используйте конечную точку, соответствующую региону, в котором зарегистрирована подписка.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

> [!NOTE]
> Если вы настроили акустическую, языковую модель или произношение, используйте пользовательскую конечную точку.

Этот API поддерживает только короткие фразы. Запросы могут содержать аудиозапись продолжительностью до 10 секунд и в общей сложности длиться не более 14 секунд. REST API возвращает только окончательные результаты, а не частичные или промежуточные. Служба "Речь" также имеет API [пакетной расшифровки](batch-transcription.md), который может расшифровывать аудиозаписи большей длины.

### <a name="query-parameters"></a>Параметры запроса

Следующие параметры можно включать в строку запроса REST.

|Имя параметра|Обязательный/необязательный|Значение|
|-|-|-|
|`language`|Обязательно|Идентификатор распознаваемого языка. См. сведения о [поддерживаемых языках](supported-languages.md#speech-to-text).|
|`format`|Необязательно<br>значение по умолчанию: `simple`|Формат результатов `simple` или `detailed`. Результаты simple включают `RecognitionStatus`, `DisplayText`, `Offset` и длительность. Результаты detailed включают несколько вариантов со значениями достоверности и четыре различных представления.|
|`profanity`|Необязательно<br>значение по умолчанию: `masked`|Способ обработки ненормативной лексики в результатах распознавания. Может принимать значения: `masked` (заменяет ненормативную лексику звездочками), `removed` (удаляет всю ненормативную лексику) или `raw` (включает ненормативную лексику в результаты распознавания).

### <a name="request-headers"></a>Заголовки запросов

В заголовок запроса HTTP отправляются следующие поля.

|Заголовок|Значение|
|------|-------|
|`Ocp-Apim-Subscription-Key`|Ключ подписки службы "Речь". Обязательно должен быть указан либо этот заголовок, либо `Authorization`.|
|`Authorization`|Маркеру авторизации предшествует слово `Bearer`. Обязательно должен быть указан либо этот заголовок, либо `Ocp-Apim-Subscription-Key`. См. раздел [Аутентификация](#authentication).|
|`Content-type`|Описывает формат и кодек звуковых данных. На данный момент в этом значении должно быть задано `audio/wav; codec=audio/pcm; samplerate=16000`.|
|`Transfer-Encoding`|Необязательный элемент. Если используется, чтобы разрешить отправку звуковых данных несколькими небольшими блоками вместо одного файла, нужно установить `chunked`.|
|`Expect`|Если используется блочная передача (в предыдущем параметре задано chunked), отправьте `Expect: 100-continue`. Служба "Речь" подтверждает первоначальный запрос и ожидает дополнительные данные.|
|`Accept`|Необязательный элемент. Если предоставлен, необходимо включить `application/json`, поскольку служба "Речь" предоставляет результаты в формате JSON. (Некоторые платформы веб-запроса предоставляют несовместимые значения по умолчанию, если они не указаны, поэтому рекомендуется всегда включать `Accept`.)|

### <a name="audio-format"></a>Формат аудио

Аудио отправляется в тексте HTTP-запроса `PUT`. Аудиопоток должен быть в 16-битном формате WAV с одноканальной (моно) ИКМ на 16 кГц.

### <a name="chunked-transfer"></a>Блочная передача

Блочная передача (`Transfer-Encoding: chunked`) может помочь снизить задержки распознавания, так как она позволяет службе "Речь" начать обработку звукового файла в процессе его передачи. REST API не поддерживает частичные или промежуточные результаты. Этот параметр предназначен исключительно для повышения скорости реагирования.

В следующем коде показано, как отправлять аудио блоками. `request` — это объект HTTPWebRequest, подключенный к соответствующей конечной точке REST. `audioFile` — путь к звуковому файлу на диске.

```csharp
using (fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
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

### <a name="example-request"></a>Пример запроса

Ниже приведен типичный запрос.

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

### <a name="http-status"></a>Состояние HTTP.

Состояние ответа HTTP показывает условия успеха или распространенные ошибки.

Код HTTP|Значение|Возможная причина
-|-|-|
100|Continue|Первоначальный запрос принят. Перейдите к отправке данных, которые остались. (Используется в случае блочной передачи.)
200|ОК|Запрос выполнен успешно; текст ответа представляет собой объект JSON.
400|Недопустимый запрос|Код языка не указан или не поддерживается; недопустимый звуковой файл.
401|Не авторизовано|Ключ подписки или маркер авторизации является недопустимым в указанном регионе, или недопустимая конечная точка.
403|Запрещено|Отсутствует ключ подписки или маркер авторизации.

### <a name="json-response"></a>Ответ JSON

Результаты возвращаются в формате JSON. Формат `simple` включает только следующие поля верхнего уровня.

|Имя поля|Содержимое|
|-|-|
|`RecognitionStatus`|Состояние, например, `Success` в случае успешного распознавания. Ознакомьтесь со следующей таблицей.|
|`DisplayText`|Распознанный текст после расстановки прописных букв, знаков препинания, обратной нормализации текста (преобразование произносимого текста в более короткие формы, например 200 вместо "двести" или "проф. Преображенский" вместо "профессор Преображенский") и маскировки ненормативной лексики. Появляется только в случае успешного распознавания.|
|`Offset`|Время (в единицах 100 нс), с которого в звуковом потоке начинается распознанная речь.|
|`Duration`|Длительность (в единицах 100 нс) распознанной речи в звуковом потоке.|

Поле `RecognitionStatus` может содержать следующие значения.

|Значение состояния|ОПИСАНИЕ
|-|-|
| `Success` | Распознавание успешно завершено и отображается поле DisplayText (отображаемый текст). |
| `NoMatch` | В аудиопотоке был обнаружена речь, но не были сопоставлены слова в целевом языке. Обычно означает, что язык распознавания — это не тот язык, на котором разговаривает пользователь. |
| `InitialSilenceTimeout` | Начало аудиопотока содержит только тишину, и время ожидания на появление речи в службе истекло. |
| `BabbleTimeout` | Начало аудиопотока содержит только шум, и время ожидания на появление речи в службе истекло. |
| `Error` | Служба распознавания обнаружила внутреннюю ошибку и не может продолжить работу. Повторите попытку, если это возможно. |

> [!NOTE]
> Если звуковой файл содержит только ненормативную лексику, а параметр запроса `profanity` имеет значение `remove`, служба не возвращает результат распознавания речи. 


Формат `detailed` включает те же поля, что и формат `simple`, а также поле `NBest`. Поле `NBest` — это список возможных интерпретаций той же речи, упорядоченных от наиболее вероятных к наименее вероятным. Первая запись совпадает с основным результатом распознавания. Каждая запись содержит следующие поля.

|Имя поля|Содержимое|
|-|-|
|`Confidence`|Оценка достоверности записи, от 0,0 (недостоверно) до 1,0 (полная достоверность)
|`Lexical`|Лексическая форма распознанного текста: конкретные распознанные слова.
|`ITN`|Форма распознанного текста после обратной нормализации ("каноническая" форма) с номерами телефонов, числами, сокращениями ("профессор Преображенский" до "проф. Преображенский") и другими выполненными преобразованиями.
|`MaskedITN`| Форма ITN с применением маскировки ненормативной лексики, если запрашивалась.
|`Display`| Отображаемая форма распознанного текста с расстановкой знаков препинания и прописных букв. Совпадает с `DisplayText` в результатах верхнего уровня.

### <a name="sample-responses"></a>Образцы ответов

Ниже приведен типичный ответ для распознавания `simple`.

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

Ниже приведен типичный ответ для распознавания `detailed`.

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="text-to-speech"></a>Преобразование текста в речь

Ниже приведены конечные точки REST для API преобразования текста в речь службы "Речь". Используйте конечную точку, соответствующую региону, в котором зарегистрирована подписка.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

> [!NOTE]
> Если вы создали пользовательский голос, используйте связанную пользовательскую конечную точку.

Служба "Речь" поддерживает выходные звуковые данные частотой 24 кГц в дополнение к выходным данным с частотой 16 кГц, поддерживаемым API распознавания речи Bing. Для использования в заголовке HTTP `X-Microsoft-OutputFormat` доступны четыре формата вывода частотой 24 кГц, поскольку есть два голоса частотой 24 кГц: `Jessa24kRUS` и `Guy24kRUS`.

Языковой стандарт | Язык   | пол; | Сопоставление имени службы
-------|------------|--------|------------
en-US  | Английский (США) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, Jessa24kRUS)" 
en-US  | Английский (США) | Male   | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, Guy24kRUS)"

Полный список доступных голосов приведен в разделе [Language and region support for Speech Service API](supported-languages.md#text-to-speech) (Поддержка языков и регионов API службы "Речь").

### <a name="request-headers"></a>Заголовки запросов

В заголовок запроса HTTP отправляются следующие поля.

|Заголовок|Значение|
|------|-------|
|`Authorization`|Маркеру авторизации предшествует слово `Bearer`. Обязательный элемент. См. раздел [Аутентификация](#authentication).|
|`Content-Type`|Тип входного содержимого: `application/ssml+xml`.|
|`X-Microsoft-OutputFormat`|Формат выходного звука. Ознакомьтесь со следующей таблицей.|
|`User-Agent`|Имя приложения. Обязательно. Это поле должно содержать менее 255 символов.|

Доступные форматы выходных звуковых данных (`X-Microsoft-OutputFormat`) объединяют скорость потока и кодировку.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `raw-8khz-8bit-mono-mulaw`
`riff-8khz-8bit-mono-mulaw`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

> [!NOTE]
> Если у выбранного голоса и формата вывода будет разная скорость передачи звука, то при необходимости звук будет обработан повторно. Тем не менее голоса частотой 24 кГц не поддерживают форматы выходных данных `audio-16khz-16kbps-mono-siren` и `riff-16khz-16kbps-mono-siren`. 

### <a name="request-body"></a>Тело запроса

Текст для преобразования в речь отправляется как текст HTTP-запроса `POST` либо в формате обычного текста (ASCII или UTF-8), либо в формате [языка разметки синтеза речи](speech-synthesis-markup.md) (SSML) (UTF-8). Запросы в формате обычного текста используют голос и язык службы по умолчанию. Отправьте SSML для использования другого голоса.

### <a name="sample-request"></a>Пример запроса

Следующий запрос HTTP использует текст запроса SSML для выбора голоса. Имя файла должно содержать не больше 1000 знаков.

```xml
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' 
    name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

### <a name="http-response"></a>Ответ HTTP

Состояние ответа HTTP показывает условия успеха или распространенные ошибки.

Код HTTP|Значение|Возможная причина
-|-|-|
200|ОК|Запрос выполнен успешно; текст ответа представляет собой звуковой файл.
400 |Ошибка запроса |Обязательный параметр отсутствует, пустой или его значение равно нулю. Или переданное либо обязательному, либо необязательному параметру значение является недопустимым. Распространенной проблемой является слишком длинный заголовок.
401|Не авторизовано |Запрос не авторизован. Убедитесь, что ваш ключ подписки или маркер являются допустимыми и находятся в правильных регионах.
413|Размер сущности запроса слишком большой|Входные данные SSM превышают длину 1024 символа.
|502|Недопустимый шлюз    | Проблема с сетью или сервером. Может также указывать на недопустимые заголовки.

Если состояние HTTP — `200 OK`, текст ответа содержит звуковой файл в затребованном формате. Этот файл можно воспроизводить, поскольку он передается или сохраняется в буфер или файл для последующего воспроизведения или другого использования.

## <a name="authentication"></a>Authentication

При отправке запроса к REST API службы "Речь" требуется ключ подписки или маркер доступа. Как правило, проще всего отправить ключ подписки напрямую. Затем служба "Речь" получает маркер доступа. Чтобы свести к минимуму время отклика, можно использовать маркер доступа.

Чтобы получить его, предоставьте ключ подписки для конечной точки `issueToken` региональной службы "Речь" в соответствии с таблицей ниже. Используйте конечную точку, соответствующую региону, в котором зарегистрирована подписка.

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-token-service.md)]

Каждый маркер доступа действителен в течение 10 минут. Новый маркер можно получить в любое время. При желании его можно получить непосредственно перед каждым запросом RESTAPI службы "Речь". Тем не менее, чтобы свести к минимуму трафик и задержки, мы советуем использовать один маркер в течение девяти минут.

В следующих разделах показано, как получить маркер и использовать его в запросе.

### <a name="get-a-token-http"></a>Получение маркера: HTTP

Ниже приведен пример HTTP-запроса для получения маркера. Замените `YOUR_SUBSCRIPTION_KEY` на ваш ключ подписки службы "Речь". Если ваша подписка не находится в регионе "Западная часть США", замените заголовок `Host` на имя узла в вашем регионе.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

В тексте ответа на этот запрос будет содержаться маркер доступа в формате JWT.

### <a name="get-a-token-powershell"></a>Получение маркера: PowerShell

В приведенном ниже скрипте Windows PowerShell показано, как получить маркер доступа. Замените `YOUR_SUBSCRIPTION_KEY` на ваш ключ подписки службы "Речь". Если ваша подписка не находится в регионе "Западная часть США", измените соответствующим образом имя узла в заданном URI.

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

### <a name="get-a-token-curl"></a>Получение маркера: cURL

cURL — это программа командной строки, доступная в Linux (и в подсистеме Windows для Linux). Приведенная ниже команда cURL позволяет получить маркер доступа. Замените `YOUR_SUBSCRIPTION_KEY` на ваш ключ подписки службы "Речь". Если ваша подписка не находится в регионе "Западная часть США", измените соответствующим образом имя узла в заданном URI.

> [!NOTE]
> Команда разделена на несколько строк для удобства чтения, но в командной строке ее нужно ввести одной строкой.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="get-a-token-c"></a>Получение маркера: C#

Приведенный ниже класс C# позволяет получить маркер доступа. Передайте ключ подписки службы "Речь" при создании экземпляра класса. Если ваша подписка не находится в регионе "Западная часть США", соответствующим образом измените имя узла `FetchTokenUri`.

```cs
/*
    * This class demonstrates how to get a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

### <a name="use-a-token"></a>Использование маркера

Чтобы использовать маркер в запросе REST API, укажите его в заголовке `Authorization` после слова `Bearer`. Ниже приведен пример запроса REST "Преобразование текста в речь", содержащий маркер. Замените `YOUR_ACCESS_TOKEN` на фактический маркер. В заголовке `Host` используйте правильное имя узла.

```xml
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.tts.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice name='Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)'>
    Hello, world!
</voice></speak>
```

### <a name="renew-authorization"></a>Обновление авторизации

Срок действия маркера авторизации истекает через 10 минут. Чтобы обновить авторизацию, получите новый маркер до истечения срока авторизации. Например, через девять минут.

Следующий код C# представляет собой альтернативу для класса, представленного выше. Класс `Authentication` автоматически получает новый маркер доступа каждые девять минут с помощью таймера. Такой подход гарантирует, что во время выполнения программы всегда доступен допустимый маркер.

> [!NOTE]
> Вместо использования таймера вы можете хранить метки времени, когда был получен последний маркер. Затем вы можете запрашивать новый, только в случае приближения окончания срока действия. Такой подход позволяет избежать ненужных запросов новых маркеров и может оказаться более подходящим для программ, редко отправляющих запросы речи.

Как и раньше, убедитесь, что значение `FetchTokenUri` соответствует вашему региону подписки. Передайте ключ подписки при создании экземпляра класса.

```cs
/*
    * This class demonstrates how to maintain a valid access token.
    */
public class Authentication
{
    public static readonly string FetchTokenUri = 
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;
    private Timer accessTokenRenewer;

    //Access token expires every 10 minutes. Renew it every 9 minutes.
    private const int RefreshTokenDuration = 9;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchToken(FetchTokenUri, subscriptionKey).Result;

        // renew the token on set duration.
        accessTokenRenewer = new Timer(new TimerCallback(OnTokenExpiredCallback),
                                        this,
                                        TimeSpan.FromMinutes(RefreshTokenDuration),
                                        TimeSpan.FromMilliseconds(-1));
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private void RenewAccessToken()
    {
        this.token = FetchToken(FetchTokenUri, this.subscriptionKey).Result;
        Console.WriteLine("Renewed token.");
    }

    private void OnTokenExpiredCallback(object stateInfo)
    {
        try
        {
            RenewAccessToken();
        }
        catch (Exception ex)
        {
            Console.WriteLine(string.Format("Failed renewing access token. Details: {0}", ex.Message));
        }
        finally
        {
            try
            {
                accessTokenRenewer.Change(TimeSpan.FromMinutes(RefreshTokenDuration), TimeSpan.FromMilliseconds(-1));
            }
            catch (Exception ex)
            {
                Console.WriteLine(string.Format("Failed to reschedule the timer to renew access token. Details: {0}", ex.Message));
            }
        }
    }

    private async Task<string> FetchToken(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

- [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
- [Настройка акустических моделей](how-to-customize-acoustic-models.md)
- [Настройка языковых моделей](how-to-customize-language-model.md)

