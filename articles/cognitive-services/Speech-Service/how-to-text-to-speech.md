---
title: Использование функции "Преобразование текста в речь" в голосовых службах | Документация Майкрософт
description: Ознакомьтесь с тем, как в голосовых службах преобразовывать текст в речь.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 6c358b5a40b1d8e91c2e1af5eb493b13604cf82e
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045064"
---
# <a name="use-text-to-speech-in-speech-service"></a>Использование функции преобразования текста в речь в голосовых службах

Голосовая служба предоставляет функцию "Преобразование текста в речь" с помощью простого HTTP-запроса. Текст POST запроса будет передан в соответствующую конечную точку, откуда будет возвращен службой в виде звукового файла (`.wav`), в котором будет содержаться синтезированная речь. Затем ваше приложение может использовать этот звук как угодно.

Тело запроса POST для преобразовывания текста в речь может быть обычным текстом (в формате ASCII или UTF8) или документом [SSML](speech-synthesis-markup.md). Для преобразования простых текстовых запросов будет использоваться голос по умолчанию. В большинстве случаев используется текст в формате SSML. HTTP-запрос должен включать маркер проверки подлинности. 

Ниже приведены региональные конечные точки преобразования текста в речь. Используйте ту из них, которая соответствует вашей подписке.

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="specify-a-voice"></a>Указание голоса

Чтобы указать голос, используйте тег `<voice>` [SSML](speech-synthesis-markup.md). Например: 

```xml
<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
  <voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
  </voice>
</speak>
```

Чтобы узнать список допустимых голосов и их имена см. раздел [Text to Speech voices](supported-languages.md#text-to-speech) (Голоса преобразования текста в речь).

## <a name="make-a-request"></a>Выполнение запроса

HTTP-запрос по преобразованию текста в речь выполняется в режиме POST с текстом, который в теле запроса будет заменен на звук. Максимальная длина тела HTTP-запроса — 1024 символа. Для запроса обязательными являются следующие заголовки. 

Заголовок|Значения|Комментарии
-|-|-
|`Content-Type` | `application/ssml+xml` | Формат входного текста.
|`X-Microsoft-OutputFormat`|     `raw-16khz-16bit-mono-pcm`<br>`audio-16khz-16kbps-mono-siren`<br>`riff-16khz-16kbps-mono-siren`<br>`riff-16khz-16bit-mono-pcm`<br>`audio-16khz-128kbitrate-mono-mp3`<br>`audio-16khz-64kbitrate-mono-mp3`<br>`audio-16khz-32kbitrate-mono-mp3`<br>`raw-24khz-16bit-mono-pcm`<br>`riff-24khz-16bit-mono-pcm`<br>`audio-24khz-160kbitrate-mono-mp3`<br>`audio-24khz-96kbitrate-mono-mp3`<br>`audio-24khz-48kbitrate-mono-mp3` | Формат выходного звука.
|`User-Agent`   |имя приложения; | Обязательным также является имя приложения, длина которого не должна превышать 255 символов.
| `Authorization`   | Маркер проверки подлинности можно получить путем предоставления ключа подписки службе токенов. Каждый из маркеров действителен 10 мин. См. раздел [REST APIs: Authentication](rest-apis.md#authentication) (Речевые службы REST API, подраздел "Аутентификация").

> [!NOTE]
> Если у выбранного голоса и формата вывода будет разная скорость передачи звука, то при необходимости звук будет обработан повторно. Форматы входных данных `audio-16khz-16kbps-mono-siren` и `riff-16khz-16kbps-mono-siren` не поддерживают частоту 24кГц. 

Ниже приведен пример запроса.

```xml
POST /cognitiveservices/v1
HTTP/1.1
Host: westus.tts.speech.microsoft.com
X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm
Content-Type: application/ssml+xml
User-Agent: Test TTS application
Authorization: (authorization token)

<speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'>
<voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>
    Hello, world!
</voice> </speak>
```

В теле ответа со статусом 200 содержится звук в указанном формате вывода.

```
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

Если возникнет ошибка, то будут использованы коды состояния, приведенные ниже. В теле ответа ошибки также содержится описание проблемы.

|Код|ОПИСАНИЕ|Проблема|
|-|-|-|
400 |Ошибка запроса |Обязательный параметр отсутствует, пустой или его значение равно нулю. Или переданное либо обязательному, либо необязательному параметру значение является недопустимым. Распространенной проблемой является слишком длинный заголовок.
401|Не авторизовано |Запрос не авторизован. Убедитесь, что ваш ключ подписки или маркер являются допустимыми.
413|Размер запрашиваемой сущности слишком большой|Входные данные SSM превышают длину 1024 символа.
|502|Недопустимый шлюз    | Проблема с сетью или сервером. Может также указывать на недопустимые заголовки.

Дополнительные сведения об API-интерфейсах REST функции "Преобразование текста в речь" см. в разделе [REST APIs](rest-apis.md#text-to-speech) (Речевые службы REST API).

## <a name="next-steps"></a>Дополнительная информация

- [Получение пробной версии службы "Речь"](https://azure.microsoft.com/try/cognitive-services/)
- [Quickstart: Recognize speech using the Cognitive Services Speech C# SDK](quickstart-csharp-windows.md) (Быстрое начало. Распознавание речи с помощью Cognitive Services Speech в пакетах SDK в C#)
