---
title: REST API службы распознавания речи | Документация Майкрософт
description: Справочник по REST API службы распознавания речи.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 05/09/2018
ms.author: v-jerkin
ms.openlocfilehash: a25c2b7ea7fdfcc6bcaa10baff3a5ae14ae9753b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37044826"
---
# <a name="speech-service-rest-apis"></a>REST API службы распознавания речи

Интерфейсы REST API единой службы распознавания речи похожи на API, предоставляемые функцией [SAPI](https://docs.microsoft.com/azure/cognitive-services/Speech) (прежнее название — служба распознавания речи Bing). Конечные точки этих API отличаются от конечных точек, используемых предыдущей службой распознавания речи.

## <a name="speech-to-text"></a>Преобразование речи в текст

В API преобразования речи в текст только используемые конечные точки отличаются от конечных точек в предыдущем API службы распознавания речи. Новые конечные точки приведены в таблице ниже. Используйте конечную точку, соответствующую региону, в котором зарегистрирована подписка.

[!include[](includes/endpoints-speech-to-text.md)]

В иных аспектах API преобразования речи в текст похож на [REST API](https://docs.microsoft.com/azure/cognitive-services/speech/getstarted/getstartedrest) предыдущей службы SAPI.

REST API преобразования речи в текст поддерживает только короткие фразы. Запросы могут содержать аудиозапись продолжительностью до 10 секунд и в общей сложности длиться не более 14 секунд. REST API возвращает только окончательные результаты, а не частичные или промежуточные.

> [!NOTE]
> Если вы настроили акустическую, языковую модель или произношение, используйте пользовательскую конечную точку.

## <a name="text-to-speech"></a>Преобразование текста в речь

Новый API преобразования текста в речь поддерживает аудиовыход частотой 24 кГц. Заголовок `X-Microsoft-OutputFormat` теперь может содержать следующие значения.

|||
|-|-|
`raw-16khz-16bit-mono-pcm`         | `audio-16khz-16kbps-mono-siren`
`riff-16khz-16kbps-mono-siren`     | `riff-16khz-16bit-mono-pcm`
`audio-16khz-128kbitrate-mono-mp3` | `audio-16khz-64kbitrate-mono-mp3`
`audio-16khz-32kbitrate-mono-mp3`  | `raw-24khz-16bit-mono-pcm`
`riff-24khz-16bit-mono-pcm`        | `audio-24khz-160kbitrate-mono-mp3`
`audio-24khz-96kbitrate-mono-mp3`  | `audio-24khz-48kbitrate-mono-mp3`

Теперь служба распознавания речи предоставляет два голоса с частотой речи 24 кГц.

Языковой стандарт | Язык   | Пол | Сопоставление имени службы
-------|------------|--------|------------
en-US  | Английский (США) | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, Jessa24kRUS)" 
en-US  | Английский (США) | Male   | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, Guy24kRUS)"

Ниже приведены конечные точки REST для API преобразования текста в речь единой службы распознавания речи. Используйте конечную точку, соответствующую региону, в котором зарегистрирована подписка.

[!include[](includes/endpoints-text-to-speech.md)]

Помните об этих различиях, когда будете обращаться к [документации по REST API](https://docs.microsoft.com/azure/cognitive-services/speech/api-reference-rest/bingvoiceoutput) для предыдущего SAPI.

## <a name="authentication"></a>Authentication

При отправке запроса к REST API службы распознавания речи требуется маркер доступа. Чтобы получить его, предоставьте ключ подписки для региональной конечной точки `issueToken` службы распознавания речи, показанной в таблице ниже. Используйте конечную точку, соответствующую региону, в котором зарегистрирована подписка.

[!include[](includes/endpoints-token-service.md)]

Каждый маркер доступа действителен в течение 10 минут. Новый маркер можно получить в любое время. При необходимости даже перед каждым запросом REST API распознавания речи. Тем не менее, чтобы свести к минимуму трафик и задержки, мы советуем использовать один токен в течение девяти минут.

В следующих разделах показано, как получить маркер и использовать его в запросе.

### <a name="getting-a-token-http"></a>Получение маркера: HTTP

Ниже приведен пример HTTP-запроса для получения маркера. Замените `YOUR_SUBSCRIPTION_KEY` на ваш ключ подписки службы "Речь". Если ваша подписка не зарегистрирована в регионе "Западная часть США", замените заголовок `Host` на имя узла в вашем регионе.

```
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

В тексте ответа на этот запрос будет содержаться маркер доступа в формате JWT.

### <a name="getting-a-token-powershell"></a>Получение маркера: PowerShell

В приведенном ниже скрипте Windows PowerShell показано, как получить маркер доступа. Замените `YOUR_SUBSCRIPTION_KEY` на ваш ключ подписки службы "Речь". Если ваша подписка не находится в регионе "Западная часть США", соответствующим образом измените имя узла в заданном URI.

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

### <a name="getting-a-token-curl"></a>Получение маркера: cURL

cURL — это программа командной строки, доступная в Linux (и в подсистеме Windows для Linux). Приведенная ниже команда cURL позволяет получить маркер доступа. Замените `YOUR_SUBSCRIPTION_KEY` на ваш ключ подписки службы "Речь". Если ваша подписка не находится в регионе "Западная часть США", соответствующим образом измените имя узла в заданном URI.

> [!NOTE]
> Команда разделена на несколько строк для удобства чтения, но в командной строке ее нужно ввести одной строкой.

```
curl -v -X POST 
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" 
 -H "Content-type: application/x-www-form-urlencoded" 
 -H "Content-Length: 0" 
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

### <a name="getting-a-token-c"></a>Получение маркера: C#

Ниже приведен класс C#, который позволяет получить маркер доступа. Передайте ключ подписки службы распознавания речи при создании экземпляра класса. Если ваша подписка не находится в регионе "Западная часть США", соответствующим образом измените имя узла `FetchTokenUri`.

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

### <a name="using-a-token"></a>Использование маркера

Чтобы использовать маркер в запросе REST API, укажите его в заголовке `Authorization` после слова `Bearer`. Ниже приведен пример запроса REST для преобразования текста в речь, содержащий маркер. Укажите фактический маркер вместо `YOUR_ACCESS_TOKEN` и правильное имя узла в заголовке `Host`.

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

### <a name="renewing-authorization"></a>Возобновление авторизации

Срок действия маркера авторизации истекает через 10 минут. Чтобы обновить авторизацию, получите новый маркер до истечения срока текущего, например через девять минут. 

Следующий код C# представляет собой альтернативу для класса, представленного выше. Класс `Authentication` автоматически получает новый маркер доступа каждые девять минут с использованием таймера. Такой подход гарантирует, что во время выполнения программы всегда доступен допустимый маркер.

> [!NOTE]
> Вместо того чтобы использовать таймер, можно сохранить метку времени получения текущего маркера, а затем запросить новый только в том случае, если срок действия текущего маркера истекает. Такой подход позволяет избежать ненужных запросов новых маркеров и может оказаться более подходящим для программ, редко отправляющих запросы на распознавание речи.

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

* [Получение пробной версии службы "Речь"](https://azure.microsoft.com/try/cognitive-services/)
* [Настройка модели речи](how-to-customize-speech-models.md)
