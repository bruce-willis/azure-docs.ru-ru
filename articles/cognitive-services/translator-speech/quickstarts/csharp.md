---
title: Краткое руководство по использованию API перевода речи с C#
titlesuffix: Azure Cognitive Services
description: Сведения и примеры кода для быстрого начала работы с API перевода речи.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: quickstart
ms.date: 3/5/2018
ms.author: v-jaswel
ROBOTS: NOINDEX
ms.openlocfilehash: 7d1f5968fe6ddffce3194f070b6a17aca4d3d9ab
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995046"
---
# <a name="quickstart-translator-speech-api-with-c"></a>Краткое руководство по использованию API перевода речи с C# 
<a name="HOLTop"></a>

В этой статье показано использование API перевода речи для перевода слов, произнесенных в файле WAV.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода на компьютерах под управлением Windows потребуется [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Будет работать бесплатный выпуск Community Edition.)

Вам необходимо иметь файл WAV, который называется "speak.wav" в той же папке, что и исполняемый файл, который будете компилировать из приведенного ниже кода. Этот файл WAV должен быть в стандартном формате PCM: 16 бит, 16 кГц, моно.

Необходимо иметь [учетную запись Cognitive Services API](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **Microsoft Translator Speech API**. Вам понадобится платный ключ подписки, доступный из [панели мониторинга Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Преобразование текста

Следующий код преобразовывает текст с одного языка на другой.

1. Создайте новый проект C# в избранной IDE.
2. Добавьте указанный ниже код.
3. Замените значение `key` ключом доступа, допустимым для подписки.
4. Запустите программу.

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading;
using System.Threading.Tasks;

namespace TranslateSpeechQuickStart
{
    class Program
    {
        static string host = "wss://dev.microsofttranslator.com";
        static string path = "/speech/translate";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        async static Task Send (ClientWebSocket client, string input_path)
        {
            var audio = File.ReadAllBytes(input_path);
            var audio_out_buffer = new ArraySegment<byte>(audio);
            Console.WriteLine("Sending audio.");
            await client.SendAsync(audio_out_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            /* Make sure the audio file is followed by silence.
             * This lets the service know that the audio input is finished. */
            var silence = new byte[3200000];
            var silence_buffer = new ArraySegment<byte>(silence);
            await client.SendAsync(silence_buffer, WebSocketMessageType.Binary, true, CancellationToken.None);

            Console.WriteLine("Done sending.");
            await client.CloseAsync(WebSocketCloseStatus.NormalClosure, "", CancellationToken.None);
        }

        async static Task Receive(ClientWebSocket client, string output_path)
        {
            var inbuf = new byte[102400];
            var segment = new ArraySegment<byte>(inbuf);
            var stream = new FileStream(output_path, FileMode.Create);

            Console.WriteLine("Awaiting response.");
            while (client.State == WebSocketState.Open)
            {
                var result = await client.ReceiveAsync(segment, CancellationToken.None);
                switch (result.MessageType)
                {
                    case WebSocketMessageType.Close:
                        Console.WriteLine("Received close message. Status: " + result.CloseStatus + ". Description: " + result.CloseStatusDescription);
                        await client.CloseAsync(WebSocketCloseStatus.NormalClosure, string.Empty, CancellationToken.None);
                        break;
                    case WebSocketMessageType.Text:
                        Console.WriteLine("Received text.");
                        Console.WriteLine(Encoding.UTF8.GetString(inbuf).TrimEnd('\0'));
                        break;
                    case WebSocketMessageType.Binary:
                        Console.WriteLine("Received binary data: " + result.Count + " bytes.");
                        stream.Write(inbuf, 0, result.Count);
                        break;
                }
            }

            stream.Close();
            stream.Dispose();
        }

        async static void TranslateSpeech()
        {
            var client = new ClientWebSocket();
            client.Options.SetRequestHeader ("Ocp-Apim-Subscription-Key", key);

            string from = "en-US";
            string to = "it-IT";
            string features = "texttospeech";
            string voice = "it-IT-Elsa";
            string api = "1.0";

            string input_path = "speak.wav";
            string output_path = "speak2.wav";

            string uri = host + path +
                "?from=" + from +
                "&to=" + to +
                "&api-version=" + api +
                "&features=" + features +
                "&voice=" + voice;

            Console.WriteLine("uri: " + uri);
            Console.WriteLine("Opening connection.");
            await client.ConnectAsync(new Uri(uri), CancellationToken.None);
            Console.WriteLine("Connection open.");
            Task.WhenAll(Send(client, input_path), Receive(client, output_path)).Wait();
        }

        static void Main()
        {
            TranslateSpeech();
            Console.ReadLine();
        }

    }
}
```

**Ответ переведенной речи**

Успешным результатом будет создание файла с названием "speak2.wav". Этот файл содержит переводы слов произнесенных в "speak.wav".

[Вверх](#HOLTop)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Учебник перевода речи](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>См. также 

[Microsoft Translator Speech API](../overview.md) (API перевода речи Microsoft)
[API Reference](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference) (Ссылка на API)
