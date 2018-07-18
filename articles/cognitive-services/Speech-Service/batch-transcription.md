---
title: API пакетного транскрибирования Azure | Документация Майкрософт для Azure
description: Примеры
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.technology: Speech to Text
ms.topic: article
ms.date: 04/26/2018
ms.author: panosper
ms.openlocfilehash: 01bbf4ca19b0fb702aa76d5149fb0e38389fe455
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054829"
---
# <a name="batch-transcription"></a>Пакетное транскрибирование

Пакетное транскрибирование идеально подходит для вариантов использования с большим объемом аудиофайлов. Оно позволяет разработчику указывать на звуковые файлы и получать транскрибирование в асинхронном режиме.

## <a name="batch-transcription-api"></a>API пакетного транскрибирования

С API пакетного транскрибирования приведенный выше сценарий возможен. Этот API предоставляет асинхронное транскрибирование речи в текст и другие дополнительные возможности.

> [!NOTE]
> API пакетного транскрибирования идеально подходит для колл-центров, где обычно накапливаются тысячи часов разговоров. Философия API Fire & Forget (нажать и забыть) упрощает транскрибирование большого объема звукозаписей.

### <a name="supported-formats"></a>Поддерживаемые форматы

API пакетного транскрибирования становится стандартом для всех автономных сценариев в колл-центрах и предоставляет поддержку всех связанных форматов. В настоящее время поддерживаются такие форматы:

ИМЯ| Канал  |
----|----------|
MP3 |   Mono   |   
MP3 |  Stereo  | 
WAV |   Mono   |
WAV |  Stereo  |

Пакетное транскрибирование будет разделять левый и правый каналы во время транскрибирования стереопотоков. Два файла JSON с результатом создаются из одного канала. Временные метки каждой фразы позволяют разработчику создавать упорядоченную окончательную расшифровку. В следующем примере JSON показаны выходные данные канала.

    ```
       {
        "recordingsUrl": "https://mystorage.blob.core.windows.net/cris-e2e-datasets/TranscriptionsDataset/small_sentence.wav?st=2018-04-19T15:56:00Z&se=2040-04-21T15:56:00Z&sp=rl&sv=2017-04-17&sr=b&sig=DtvXbMYquDWQ2OkhAenGuyZI%2BYgaa3cyvdQoHKIBGdQ%3D",
        "resultsUrls": {
            "channel_0": "https://mystorage.blob.core.windows.net/bestor-87a0286f-304c-4636-b6bd-b3a96166df28/TranscriptionData/24265e4c-e459-4384-b572-5e3e7795221f?sv=2017-04-17&sr=b&sig=IY2qd%2Fkgtz2PwRe2C88BphH4Hv%2F1VCb1UVJ33xsw%2BEY%3D&se=2018-04-23T14:48:24Z&sp=r"
        },
        "statusMessage": "None.",
        "id": "0bb95356-ff06-469d-acc7-81f9144a269a",
        "createdDateTime": "2018-04-20T14:11:57.167",
        "lastActionDateTime": "2018-04-20T14:12:54.643",
        "status": "Succeeded",
        "locale": "en-US"
    },
    ```

> [!NOTE]
> API пакетного транскрибирования использует службу REST для запроса расшифровок, их состояния и связанных результатов. Он основан на платформе .NET и не имеет внешних зависимостей. В следующем разделе описано использование этого API.

## <a name="authorization-token"></a>Маркер авторизации

Как и для всех функций единой службы обработки речи, пользователю необходимо создать ключ подписки на[портале Azure](https://portal.azure.com). Кроме того, ключ API нужно получить на портале распознавания речи. Ниже приведены шаги для создания ключа API.

1. Войдите на сайт https://customspeech.ai.

2. Щелкните "Подписки".

3. Щелкните параметр `Generate API Key` (Создать ключ API).

    ![Представление загрузки](media/stt/Subscriptions.jpg)

4. Скопируйте и вставьте ключ в клиентском коде, как показано в примере ниже.

> [!NOTE]
> Если вы планируете использовать пользовательскую модель, вам также потребуется ее идентификатор. В представлении сведений о конечной точке находится не идентификатор развертывания или конечной точки, а идентификатор модели, который можно получить, щелкнув пункт сведений об этой модели.

## <a name="sample-code"></a>Пример кода

Использование API является достаточно простым. В следующем примере кода нужно указать ключ подписки и ключ API.

```cs
   static async Task TranscribeAsync()
        { 
            // Creating a Batch transcription API Client
            var client = 
                await CrisClient.CreateApiV1ClientAsync(
                    "<your msa>", // MSA email
                    "<your api key>", // API key
                    "stt.speech.microsoft.com",
                    443).ConfigureAwait(false);
            
            var newLocation = 
                await client.PostTranscriptionAsync(
                    "<selected locale i.e. en-us>", // Locale 
                    "<your subscripition key>", // Subscription Key
                    new Uri("<SAS URI to your file>")).ConfigureAwait(false);

            var transcription = await client.GetTranscriptionAsync(newLocation).ConfigureAwait(false);

            while (true)
            {
                transcription = await client.GetTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                if (transcription.Status == "Failed" || transcription.Status == "Succeeded")
                {
                    Console.WriteLine("Transcription complete!");

                    if (transcription.Status == "Succeeded")
                    {
                        var resultsUri = transcription.ResultsUrls["channel_0"];

                        WebClient webClient = new WebClient();

                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(resultsUri, filename);

                        var results = File.ReadAllText(filename);
                        Console.WriteLine(results);
                    }

                    await client.DeleteTranscriptionAsync(transcription.Id).ConfigureAwait(false);

                    break;
                }
                else
                {
                    Console.WriteLine("Transcription status: " + transcription.Status);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }

            Console.ReadLine();
        }
```

> [!NOTE]
> Ключ подписки, указанный в предыдущем фрагменте кода, — это ключ из ресурса службы речи (предварительная версия), который создается на портале Azure. Ключи, полученные из ресурса "Пользовательская служба распознавания речи", не будут работать.


Обратите внимание на асинхронную настройку для отправки аудио и получения состояния транскрибирования. Создан клиент NET HTTP. Существует метод `PostTranscriptions` для отправки сведений об аудиофайле и метод `GetTranscriptions` для получения результатов. `PostTranscriptions` возвращает дескриптор, который `GetTranscriptions` использует для получения состояния транскрибирования.

Текущий пример кода не определяет какие-либо пользовательские модели. Служба будет использовать базовые модели для расшифровки файлов. Если пользователь хочет указать модели, можно передать с тем же методом идентификаторы акустической и языковой моделей. 

Если пользователь не хочет использовать базовые модели, необходимо передать идентификаторы нужных моделей.

> [!NOTE]
> Для базового транскрибирования не нужно объявлять конечные точки базовых моделей. Если пользователь хочет использовать пользовательские модели, необходимо предоставить идентификаторы конечных точек, как в [примере](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI). Если пользователь хочет использовать акустическую базовую модель с базовой языковой моделью, тогда ему нужно объявить только идентификатор конечной точки пользовательской модели. Внутренне наша система определит базовую модель партнера (акустическая или языковая) и использует ее для выполнения запроса на транскрибирование.

### <a name="supported-storage"></a>Поддерживаемое хранилище

В настоящее время поддерживается только хранилище BLOB-объектов Azure.

## <a name="downloading-the-sample"></a>Скачивание примера

Показанный здесь пример находится на [GitHub](https://github.com/PanosPeriorellis/Speech_Service-BatchTranscriptionAPI).

> [!NOTE]
> Обычно для аудиотранскрибирования требуется временной интервал равный длительности аудиофайла плюс 2–3 минуты.

## <a name="next-steps"></a>Дополнительная информация

* [Получение пробной версии службы "Речь"](https://azure.microsoft.com/try/cognitive-services/)
