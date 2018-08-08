---
title: Устранение неполадок в пакете SDK для службы "Речь" в Cognitive Services
description: Устранение неполадок в пакете SDK для распознавания речи в Cognitive Services
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: ff8aba562cfd2d6d54c708ee7fdc4c6ca7185f29
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284128"
---
# <a name="troubleshooting-speech-services-sdk"></a>Устранение неполадок в пакете SDK для распознавания речи

В этой статье содержится информация, которая поможет решить неполадки, которые могут возникнуть при использовании пакета SDK для распознавания речи.

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>Ошибка `WebSocket Upgrade failed with an authentication error (403).`

Может быть указана неправильная конечная точка для вашего региона или службы. Еще раз проверьте универсальный код ресурса (URI), чтобы убедиться в том, что он правильный. Также посмотрите в следующем разделе, поскольку это может быть проблема с ключом подписки или авторизацией токена безопасности.

## <a name="error-http-403-forbidden-or-error-http-401-unauthorized"></a>Ошибка `HTTP 403 Forbidden` или ошибка `HTTP 401 Unauthorized`

Эта ошибка часто вызвана проблемами аутентификации. Запросы на соединение без допуска `Ocp-Apim-Subscription-Key` или `Authorization` заголовка отклоняются по состоянию 401 или 403.

* Если вы используете ключ подписки для аутентификации, причиной может быть:

    - отсутствие или недопустимость ключа подписки,
    - вы превысили квоту использования подписки

* Если вы используете токен авторизации для аутентификации, причиной может быть:

    - токен авторизации недопустимый
    - токен авторизации истек

### <a name="validate-your-subscription-key"></a>Проверьте ключ подписки

Вы можете убедиться, что у вас есть допустимый ключ подписки, выполнив одну из приведенных ниже команд.

> [!NOTE]
> В указанном порядке замените `YOUR_SUBSCRIPTION_KEY` и `YOUR_REGION` своим собственным ключом подписки и ассоциированной областью.

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

### <a name="validate-an-authorization-token"></a>Проверьте маркер проверки подлинности

Если используется токен авторизации для аутентификации, выполните одну из приведенных ниже команд, чтобы убедиться, что токен авторизации по-прежнему допустимый. Токены допустимы в течение 10 минут.

> [!NOTE]
> Замените `YOUR_AUDIO_FILE` на путь к предварительно записанному аудиофайлу, `YOUR_ACCESS_TOKEN` на токен авторизации, возвращенный на предыдущем шаге, и `YOUR_REGION` на правильную область.

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'
    
    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }
    
    # Read audio into byte array
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")
    
    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes
    
    # Show the result
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

---

## <a name="error-http-400-bad-request"></a>Ошибка `HTTP 400 Bad Request`

Обычно эта ошибка возникает, когда текст запроса содержит недопустимые аудиоданные. Поддерживается только формат `WAV`. Также проверьте заголовки запроса, чтобы убедиться, что вы указываете соответствующие `Content-Type` и `Content-Length`.

## <a name="error-http-408-request-timeout"></a>Ошибка `HTTP 408 Request Timeout`

Ошибка, скорее всего, из-за отсутствия аудиоданных в службе. Эта ошибка также может быть вызвана проблемами с сетью.

## <a name="the-recognitionstatus-in-the-response-is-initialsilencetimeout"></a>`RecognitionStatus` в ответе — `InitialSilenceTimeout`

Аудиоданные обычно являются причиной возникновения проблемы. Например: 

* В начале аудиозаписи есть продолжительный участок тишины. Служба прекратит распознавание через несколько секунд и вернет `InitialSilenceTimeout`.
* Аудиозапись создана с помощью неподдерживаемого кодека, что приводит к тому, что аудиоданные воспринимаются как тишина.

## <a name="next-steps"></a>Дополнительная информация

* [Заметки о выпуске](releasenotes.md)

