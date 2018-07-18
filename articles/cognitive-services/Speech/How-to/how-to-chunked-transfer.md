---
title: Как использовать блоки при передаче аудиопотока | Документация Майкрософт
description: Использование блочной передачи для передачи аудиопотока в службу распознавания речи
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 7d02340932dfc547893c4c40cbe08978b7b93756
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380537"
---
# <a name="chunked-transfer-encoding"></a>Кодирование блочной передачи

Для преобразования речи в текст API распознавания речи Microsoft позволяет отправить аудио как один сплошной блок или разделить его на небольшие фрагменты. Для эффективной потоковой передачи аудио и сокращения задержки транскрибирования рекомендуется использовать [кодирование блочной передачи](https://en.wikipedia.org/wiki/Chunked_transfer_encoding) для передачи аудиозаписи в службу. Другие реализации могут привести к более высокой задержке, которую заметит пользователь. Дополнительные сведения о требованиях см. на странице [Аудиопотоки](../concepts.md#audio-streams).

> [!NOTE]
> Вы не можете загружать аудио длительностью более чем 10 секунд в одном запросе, а общая длительность запроса не может превышать 14 секунд.
> [!NOTE]
> Необходимо указать кодирование блочной передачи только в том случае, если для вызова службы распознавания речи вы используете [REST API](../GetStarted/GetStartedREST.md). В приложениях, которые используют [клиентские библиотеки](../GetStarted/GetStartedClientLibraries.md), не нужно настраивать кодировку блочной передачи.

Ниже показано, как можно задать кодировку блочной передачи и передать звуковой файл, разделенный на 1024-байтовых фрагмента.

```cs

HttpWebRequest request = null;
request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = @"speech.platform.bing.com";
request.ContentType = @"audio/wav; codec=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";

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
