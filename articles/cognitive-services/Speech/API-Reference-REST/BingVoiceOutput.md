---
title: API преобразования текста в речь службы "Речь" Майкрософт | Документация Майкрософт
description: Используйте API преобразования текста в речь для преобразования текста в речь в режиме реального времени с применением разных языков и голосов
services: cognitive-services
author: priyaravi20
manager: yanbo
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 03/16/2017
ms.author: priyar
ms.openlocfilehash: 4b633cefa37c11511a8171d5a7f61b03dfaa4466
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380769"
---
# <a name="bing-text-to-speech-api"></a>API преобразования текста в речь Bing

## <a name="Introduction"></a>Введение

С помощью API преобразования текста в речь Bing ваше приложение может отправить HTTP-запросы к облачному серверу, где текст мгновенно синтезируется в человеческую речь и возвращается как звуковой файл. Этот API можно использовать во множестве разных контекстов для преобразования текста в речь в режиме реального времени с применением разных языков и голосов.

## <a name="VoiceSynReq"></a>Запрос синтеза голоса

### <a name="Subscription"></a>Маркер авторизации

Каждый запрос синтеза голоса требует маркер доступа JSON Web Token (JWT). Маркер доступа JWT передается в заголовке запроса речи. Срок действия маркера истекает в течение 10 минут. Сведения о подписке и получении ключей API, которые позволяют получить действующий маркер доступа JWT, см. на странице [подписки Cognitive Services](https://azure.microsoft.com/try/cognitive-services/).

Ключ API передается службе токенов. Например: 

```HTTP
POST https://api.cognitive.microsoft.com/sts/v1.0/issueToken
Content-Length: 0
```

Обязательная информация заголовка для маркера доступа выглядит так.

ИМЯ| Формат | ОПИСАНИЕ
----|----|----
Ocp-Apim-Subscription-Key | ASCII | Ваш ключ подписки

Служба токенов возвращает маркер доступа JWT в виде `text/plain`. Затем JWT передается в качестве `Base64 access_token` к конечной точке речи как заголовок авторизации с префиксом `Bearer` в виде строки. Например: 

`Authorization: Bearer [Base64 access_token]`

Клиенты должны использовать следующую конечную точку для доступа к службе преобразования текста в речь:

`https://speech.platform.bing.com/synthesize`

>[!NOTE]
>Пока вы не получили маркер доступа с помощью ключа подписки, как было описано ранее, эта ссылка создает ошибку `403 Forbidden`.

### <a name="Http"></a>Заголовки HTTP

В следующей таблице показаны заголовки HTTP, которые используются для запросов синтеза голоса.

Заголовок |Значение |Комментарии
----|----|----
Content-Type | application/ssml+xml | Тип входного содержимого.
X-Microsoft-OutputFormat | **1.** ssml-16khz-16bit-mono-tts <br> **2.** raw-16khz-16bit-mono-pcm <br>**3.** audio-16khz-16kbps-mono-siren <br> **4.** riff-16khz-16kbps-mono-siren <br> **5.** riff-16khz-16bit-mono-pcm <br> **6.** audio-16khz-128kbitrate-mono-mp3 <br> **7.** audio-16khz-64kbitrate-mono-mp3 <br> **8.** audio-16khz-32kbitrate-mono-mp3 | Формат выходного звука.
X-Search-AppId | Идентификатор GUID (только шестнадцатеричный, без дефисов) | Идентификатор, однозначно определяющий клиентское приложение. Может быть идентификатором магазина приложений. Если идентификатор для приложения недоступен, то пользователь может его создать.
X-Search-ClientID | Идентификатор GUID (только шестнадцатеричный, без дефисов) | Идентификатор, однозначно определяющий экземпляр приложения для каждой установки.
User-Agent | имя приложения; | Обязательным также является имя приложения, длина которого не должна превышать 255 символов.
Авторизация | Маркер авторизации |  См. раздел <a href="#Subscription">Токен авторизации</a>.

### <a name="InputParam"></a>Входные параметры

Запросы к API преобразования текста в речь Bing выполняются с помощью вызовов HTTP POST. Заголовки указаны в предыдущем разделе. Текст содержит входные данные языка разметки синтеза речи (SSML), представляющие текст для синтезирования. Описание разметки, используемой для управления аспектами речи, такими как язык и пол говорящего, см. в статье о [спецификации SSML W3C](http://www.w3.org/TR/speech-synthesis/).

>[!NOTE]
>Максимально поддерживаемый размер входных данных SSML — 1024 символа, включая все теги.

###  <a name="SampleVoiceOR"></a>Пример: запрос на вывод в голосовом формате

Ниже приведен пример запроса на вывод в голосовом формате:

```HTTP
POST /synthesize
HTTP/1.1
Host: speech.platform.bing.com

X-Microsoft-OutputFormat: riff-8khz-8bit-mono-mulaw
Content-Type: application/ssml+xml
Host: speech.platform.bing.com
Content-Length: 197
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>Microsoft Bing Voice Output API</voice></speak>
```

## <a name="VoiceOutResponse"></a>Ответ на вывод в голосовом формате

API преобразования текста в речь Bing использует HTTP POST для обратной отправки клиенту аудио. Ответ API содержит аудиопоток и кодек, а также соответствует запрашиваемому формату выходных данных. Аудио, возвращаемое для данного запроса, не должно превышать 15 секунд.

### <a name="SuccessfulRecResponse"></a>Пример: успешно синтезированный ответ

Ниже приведен пример кода ответа JSON на успешный запрос синтеза голоса. Комментарии и форматирование кода распространяются только на этот пример и игнорируются при фактическом ответе.

```HTTP
HTTP/1.1 200 OK
Content-Length: XXX
Content-Type: audio/x-wav

Response audio payload
```

### <a name="RecFailure"></a>Пример: сбой синтеза

В следующем примере кода показан ответ JSON на запрос синтеза голоса:

```HTTP
HTTP/1.1 400 XML parser error
Content-Type: text/xml
Content-Length: 0
```

### <a name="ErrorResponse"></a>Сообщения об ошибках

Ошибка | ОПИСАНИЕ
----|----
HTTP/400 недопустимый запрос | Обязательный параметр пропущен, пустой или содержит значение NULL, или переданное либо обязательному, либо необязательному параметру значение является недопустимым. Одна из причин получения ответа "Недопустимый" — передача строкового значения, длина которого превышает допустимую. Включено краткое описание проблемного параметра.
HTTP/401 — недостаточно прав | Запрос не авторизован.
HTTP/413 — сущность запроса слишком большая  | Входные данные SSML больше, чем поддерживается.
HTTP/502 — неверный шлюз | Имеется проблема, связанная с сетью или сервером.

Ниже приведен пример сообщения об ошибке:

```HTTP
HTTP/1.0 400 Bad Request
Content-Length: XXX
Content-Type: text/plain; charset=UTF-8

Voice name not supported
```

## <a name="ChangeSSML"></a>Изменение голосовых выходных данных через SSML

API преобразования текста в речь Майкрософт поддерживает SSML 1.0, как определено в [языке разметки синтеза речи (SSML) версии 1.0](http://www.w3.org/TR/2009/REC-speech-synthesis-20090303/) W3C. В этом разделе приведены примеры изменения определенных характеристик созданных голосовых выходных данных, таких как скорость речи, произношение и т. д., с помощью тегов SSML.

1. Добавление разрыва

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)'> Welcome to use Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.</voice> </speak>
  ```

2. Изменение скорости речи

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody rate="+30.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

3. Произношение

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'> <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme></voice> </speak>
  ```

4. Изменение громкости

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody volume="+20.00%">Welcome to use Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

5. Изменение тона

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Welcome to use <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody></voice> </speak>
  ```

6. Изменение контура ударения

  ```
  <speak version='1.0' xmlns="http://www.w3.org/2001/10/synthesis" xml:lang='en-US'><voice  name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'><prosody contour="(80%,+20%) (90%,+30%)" >Good morning.</prosody></voice> </speak>
  ```

> [!NOTE]
> Обратите внимание, что звуковые данные должны быть 8- или 16-битными WAV-файлами в следующем формате: **код CRC** (CRC-32): 4 байта (DWORD) с допустимым диапазоном 0x00000000 ~ 0xFFFFFFFF; **флаг формата звука**: 4 байта (DWORD) с допустимым диапазоном 0x00000000 ~ 0xFFFFFFFF; **размер выборки**: 4 байта (DWORD) с допустимым диапазоном 0x00000000 ~ 0x7FFFFFFF; **размер двоичного основного текста**: 4 байта (DWORD) с допустимым диапазоном 0x00000000 ~ 0x7FFFFFFF; **двоичный основной текст**: n байт.

## <a name="SampleApp"></a>Пример приложения

Дополнительные сведения о реализации см. на странице [примера приложения преобразования текста в речь с помощью Visual C# .NET](https://github.com/Microsoft/Cognitive-Speech-TTS/blob/master/Samples-Http/CSharp/TTSProgram.cs).

## <a name="SupLocales"></a>Поддерживаемые языковые стандарты и голоса

В следующей таблице приведены некоторые из поддерживаемых языковых стандартов и связанные с ними голоса.

Языковой стандарт | Пол | Сопоставление имени службы
---------|--------|------------
ar-EG* | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ar-EG, Hoda)"
ar-SA | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ar-SA, Naayf)"
bg-BG | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (bg-BG, Ivan)"
ca-ES | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ca-ES, HerenaRUS)"
cs-CZ | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (cs-CZ, Jakub)"
da-DK | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (da-DK, HelleRUS)"
de-AT | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-AT, Michael)"
de-CH | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-CH, Karsten)"
de-DE | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-DE, Hedda)"
de-DE | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-DE, HeddaRUS)"
de-DE | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (de-DE, Stefan, Apollo)"
el-GR | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (el-GR, Stefanos)"
en-AU | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-AU, Catherine)"
en-AU | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-AU, HayleyRUS)"
en-CA | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-CA, Linda)"
en-CA | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-CA, HeatherRUS)"
en-GB | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-GB, Susan, Apollo)"
en-GB | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-GB, HazelRUS)"
en-GB | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-GB, George, Apollo)"
en-IE | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IE, Sean)"
en-IN | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IN, Heera, Apollo)"
en-IN | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IN, PriyaRUS)"
en-IN | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IN, Ravi, Apollo)"
en-US | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, ZiraRUS)"
en-US | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, JessaRUS)"
en-US | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-US, BenjaminRUS)"
es-ES | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-ES, Laura, Apollo)"
es-ES | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-ES, HelenaRUS)"
es-ES | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-ES, Pablo, Apollo)"
es-MX | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-MX, HildaRUS)"
es-MX | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (es-MX, Raul, Apollo)"
fi-FI | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fi-FI, HeidiRUS)"
fr-CA | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-CA, Caroline)"
fr-CA | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-CA, HarmonieRUS)"
fr-CH | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-CH, Guillaume)"
fr-FR | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-FR, Julie, Apollo)"
fr-FR | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-FR, HortenseRUS)"
fr-FR | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (fr-FR, Paul, Apollo)"
he-IL| Male| "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (he-IL, Asaf)"
hi-IN | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hi-IN, Kalpana, Apollo)"
hi-IN | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hi-IN, Kalpana)"
hi-IN | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hi-IN, Hemant)"
hr-HR | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hr-HR, Matej)"
hu-HU | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (hu-HU, Szabolcs)"
id-ID | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (id-ID, Andika)"
it-IT | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (it-IT, Cosimo, Apollo)"
ja-JP | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ja-JP, Ayumi, Apollo)"
ja-JP | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ja-JP, Ichiro, Apollo)"
ja-JP | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ja-JP, HarukaRUS)"
ja-JP | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ja-JP, LuciaRUS)"
ja-JP | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ja-JP, EkaterinaRUS)"
ko-KR | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ko-KR, HeamiRUS)"
ms-MY | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ms-MY, Rizwan)"
nb-NO | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (nb-NO, HuldaRUS)"
nl-NL | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (nl-NL, HannaRUS)"
pl-PL | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (pl-PL, PaulinaRUS)"
pt-BR | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (pt-BR, HeloisaRUS)"
pt-BR | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (pt-BR, Daniel, Apollo)"
pt-PT | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (pt-PT, HeliaRUS)"
ro-RO | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ro-RO, Andrei)"
ru-RU | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ru-RU, Irina, Apollo)"
ru-RU | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ru-RU, Pavel, Apollo)"
sk-SK | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (sk-SK, Filip)"
sl-SI | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (sl-SI, Lado)"
sv-SE | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (sv-SE, HedvigRUS)"
ta-IN | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (ta-IN, Valluvar)"
th-TH | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (th-TH, Pattara)"
tr-TR | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (tr-TR, SedaRUS)"
vi-VN | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (vi-VN, An)"
zh-CN | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-CN, HuihuiRUS)"
zh-CN | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-CN, Yaoyao, Apollo)"
zh-CN | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-CN, Kangkang, Apollo)"
zh-HK | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-HK, Tracy, Apollo)"
zh-HK | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-HK, TracyRUS)"
zh-HK | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-HK, Danny, Apollo)"
zh-TW | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-TW, Yating, Apollo)"
zh-TW | Female | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-TW, HanHanRUS)"
zh-TW | Male | "Голос для преобразования текста в речь службы распознавания речи Microsoft Server (zh-TW, Zhiwei, Apollo)"
 *ar-EG поддерживает современный арабский литературный язык (MSA).

> [!NOTE]
> Обратите внимание, что для оптимизации возможностей API распознавания речи Bing после 31.03.2018 не поддерживаются такие названия служб: **Голос для преобразования текста в речь службы распознавания речи Microsoft Server (cs-CZ, Vit)** и **Голос для преобразования текста в речь службы распознавания речи Microsoft Server (en-IE, Shaun)**. Укажите в коде обновленные имена.

## <a name="TrouNSupport"></a>Устранение неполадок и поддержка

Публикуйте на форуме MSDN все вопросы и проблемы, связанные со [службой "Речь" Bing](https://social.msdn.microsoft.com/Forums/en-US/home?forum=SpeechService). Включая подробные сведения, такие как:

* Пример полной строки запроса.
* Полные выходные данные невыполненных запросов, включая идентификаторы журналов (если это применимо).
* Процент запросов, завершающихся неудачей.
