---
title: Аутентификация в API распознавания речи Bing | Документация Майкрософт
titlesuffix: Azure Cognitive Services
description: Запрос аутентификации для использования API распознавания речи Bing.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: 4d4b9f43b6b3a4127d70989414d467f724f6ec28
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972799"
---
# <a name="authenticate-to-the-speech-api"></a>Аутентификация в SAPI

Служба "Распознавание речи Bing" поддерживает следующие методы аутентификации:

- ключ подписки;
- маркер авторизации.

## <a name="use-a-subscription-key"></a>Использование ключа подписки

Чтобы использовать службу "Речь", вам потребуется подписка на интерфейс SAPI, входящий в службу Cognitive Services (прежнее название — Project Oxford). Вы можете получить ключи бесплатной пробной версии подписки на странице [подписки на Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Чтобы получить ключ, после выбора SAPI выберите **Получить API-ключ**. Страница возвращает первичный и вторичный ключ. Оба ключа привязаны к одной и той же квоте, поэтому вы можете использовать любой из них.

Для долгосрочного использования или получения увеличенной квоты, зарегистрируйтесь и получите [учетную запись Azure](https://azure.microsoft.com/free/).

Чтобы использовать SAPI REST, необходимо передать ключ подписки в поле `Ocp-Apim-Subscription-Key` заголовка запроса.

ИМЯ| Формат| ОПИСАНИЕ
----|-------|------------
Ocp-Apim-Subscription-Key | ASCII | YOUR_SUBSCRIPTION_KEY

Ниже приведен пример заголовка запроса:

```HTTP
POST https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codec=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: speech.platform.bing.com
Transfer-Encoding: chunked
Expect: 100-continue
```

> [!IMPORTANT]
> Если вы используете в приложении [клиентские библиотеки](../GetStarted/GetStartedClientLibraries.md), убедитесь, что ключ подписки позволяет получить маркер авторизации, как описано в следующем разделе. Клиентские библиотеки используют ключ подписки для получения маркера авторизации, а затем применяют полученный маркер для аутентификации.

## <a name="use-an-authorization-token"></a>Использование маркера авторизации

Вы также можете использовать маркер авторизации для подтверждения аутентификации и авторизации. Чтобы получить этот маркер, сначала нужно получить от SAPI ключ подписки, как описано в [предыдущем разделе](#use-a-subscription-key).

### <a name="get-an-authorization-token"></a>Получение маркера авторизации

Получив действительный ключ подписки, отправьте запрос POST в службу маркеров Cognitive Services. В ответе на него вы получите маркер авторизации в формате JWT (JSON Web Token).

> [!NOTE]
> Срок действия этого маркера составляет 10 минут. Чтобы обновить маркер, следуйте инструкциям из следующего раздела.

URI службы маркеров находится здесь:

```
https://api.cognitive.microsoft.com/sts/v1.0/issueToken
```

В этом примере кода показано, как получить маркер доступа. Замените `YOUR_SUBSCRIPTION_KEY` фактическим ключом подписки:

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

В этом примере используется curl под ОС Linux с помощью Bash. Если на вашей платформе нет служебной программы curl, установите ее. Этот пример будет работать также в Cygwin под ОС Windows, Git Bash, zsh и других оболочках.

```
curl -v -X POST "https://api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

```cs
    /*
     * This class demonstrates how to get a valid access token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```

---

Ниже приведен пример запроса POST:

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive
```

Если вам не удается получить маркер авторизации от службы маркеров, проверьте действительность ключа подписки. Если вы используете ключ бесплатной пробной версии, откройте страницу [подписки Cognitive Services](https://azure.microsoft.com/try/cognitive-services/), щелкните "Вход", чтобы войти с помощью учетной записи, которую вы применяли для получения ключа бесплатной пробной версии. Здесь вы сможете проверить, не истек ли срок действия и не превышена ли квота вашего ключа подписки.

### <a name="use-an-authorization-token-in-a-request"></a>Использование маркера авторизации в запросе

Каждый раз при обращении к SAPI нужно передавать маркер авторизации в заголовке `Authorization`. Заголовок `Authorization` должен содержать маркер доступа JWT.

В следующем примере показано, как использовать маркер авторизации при вызове SAPI REST.

> [!NOTE]
> Замените `YOUR_AUDIO_FILE` путем к заранее записанному звуковому файлу. Замените `YOUR_ACCESS_TOKEN` маркером авторизации, который вы получили [на предыдущем шаге](#get-an-authorization-token).

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/Powershell)

```Powershell

$SpeechServiceURI =
'https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed'

# $OAuthToken is the authrization token returned by the token service.
$RecoRequestHeader = @{
  'Authorization' = 'Bearer '+ $OAuthToken;
  'Transfer-Encoding' = 'chunked'
  'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
}

# Read audio into byte array
$audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

$RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

# Show the result
$RecoResponse

```

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -v -X POST "https://speech.platform.bing.com/speech/recognition/interactive/cognitiveservices/v1?language=en-us&format=detailed" -H "Transfer-Encoding: chunked" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
```

# <a name="ctabcsharp"></a>[C#](#tab/CSharp)

```cs
HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Authorization"] = "Bearer " + token;

// Send an audio file by 1024 byte chunks
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

---

### <a name="renew-an-authorization-token"></a>Обновление маркера авторизации

Срок действия маркера авторизации истекает после определенного периода времени (в настоящее время это 10 минут). Маркер авторизации необходимо обновить до того, как истечет срок его действия.

Ниже приведен пример кода на C#, в котором обновляется маркер авторизации:

```cs
    /*
     * This class demonstrates how to get a valid O-auth token.
     */
    public class Authentication
    {
        public static readonly string FetchTokenUri = "https://api.cognitive.microsoft.com/sts/v1.0";
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
                uriBuilder.Path += "/issueToken";

                var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
                Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
                return await result.Content.ReadAsStringAsync();
            }
        }
    }
```
