---
title: Azure Content Moderator — создание проверок для расшифровки видео с помощью .NET | Документация Майкрософт
description: Создание проверок для расшифровки видео с помощью пакета SDK Azure Content Moderator для .NET
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/19/2018
ms.author: sajagtap
ms.openlocfilehash: 3286da6e38f0fba02386d877a835fb694ed0fdec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "35380724"
---
# <a name="create-video-transcript-reviews-using-net"></a>Создание проверок для расшифровки видео .NET

В этой статье содержатся сведения и примеры кода, которые помогут быстро приступить к работе с пакетом SDK Content Moderator для C# и выполнить следующее:

- создать проверку видео для оценки человеком;
- добавить в эту проверку расшифровку видео, для которой выполнена модерация;
- опубликовать проверку.

## <a name="prerequisites"></a>предварительным требованиям

В этой статье предполагается, что вы уже [выполнили модерацию видео](video-moderation-api.md) и [создали проверку видео](video-reviews-quickstart-dotnet.md) в средстве проверки для принятия решений человеком. Теперь вы хотите добавить расшифровку видео, прошедшую модерацию, в это средство проверки.

В этой статье также предполагается, что вы уже работали с Visual Studio и C#.

### <a name="sign-up-for-content-moderator-services"></a>Регистрация в службах Content Moderator

Прежде чем использовать службы Content Moderator через REST API или пакет SDK, необходимо получить ключ подписки.

На информационной панели Content Moderator вы можете узнать свой ключ подписки, последовательно выбрав **Settings (Параметры)** > **Credentials (Учетные данные)** > **API** > **Trial Ocp-Apim-Subscription-Key (Тестовый ключ подписки)**. Дополнительные сведения см. в статье [обзорных сведений](overview.md).

### <a name="prepare-your-video-for-review"></a>Подготовка видео к проверке

Добавьте расшифровку в проверку видео. Это видео должно быть опубликовано в Интернете. Вам нужно знать его конечную точку потоковой передачи. Наличие конечной точки потоковой передачи позволяет средству проверки воспроизвести видео.

![Эскиз демонстрационного видео](images/ams-video-demo-view.PNG)

- Скопируйте **URL-адрес**, опубликованный на странице [демонстрации Служб мультимедиа Azure](https://aka.ms/azuremediaplayer?url=https%3A%2F%2Famssamples.streaming.mediaservices.windows.net%2F91492735-c523-432b-ba01-faba6c2206a2%2FAzureMediaServicesPromo.ism%2Fmanifest) и сохраните его в качестве URL-адреса манифеста.

## <a name="create-your-visual-studio-project"></a>Создание проекта Visual Studio

1. Добавьте новый проект **Консольное приложение (.NET Framework)** в свое решение.

1. Присвойте проекту имя **VideoTranscriptReviews**.

1. Выберите этот проект единственным запускаемым проектом для решения.

### <a name="install-required-packages"></a>Установка необходимых пакетов

Установите следующие пакеты NuGet для проекта TermLists.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>Обновление инструкций using в программе

Измените инструкции using в вашей программе следующим образом.

    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;
    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;


### <a name="add-private-properties"></a>Добавление закрытых свойств

Добавьте следующие закрытые свойства в пространство имен VideoTranscriptReviews, в класс Program.

Где это дополнительно указано, замените примеры значений реальными значениями свойств.


    namespace VideoReviews
    {
        class Program
        {
            // NOTE: Replace this example location with the location for your Content Moderator account.
            /// <summary>
            /// The region/location for your Content Moderator account, 
            /// for example, westus.
            /// </summary>
            private static readonly string AzureRegion = "YOUR CONTENT MODERATOR REGION";

            // NOTE: Replace this example key with a valid subscription key.
            /// <summary>
            /// Your Content Moderator subscription key.
            /// </summary>
            private static readonly string CMSubscriptionKey = "YOUR CONTENT MODERATOR KEY";

            // NOTE: Replace this example team name with your Content Moderator team name.
            /// <summary>
            /// The name of the team to assign the job to.
            /// </summary>
            /// <remarks>This must be the team name you used to create your 
            /// Content Moderator account. You can retrieve your team name from
            /// the Conent Moderator web site. Your team name is the Id associated 
            /// with your subscription.</remarks>
            public static readonly string TeamName = "YOUR CONTENT MODERATOR TEAM ID";

            /// <summary>
            /// The base URL fragment for Content Moderator calls.
            /// </summary>
            private static readonly string AzureBaseURL =
                $"{AzureRegion}.api.cognitive.microsoft.com";

            /// <summary>
            /// The minimum amount of time, in milliseconds, to wait between calls
            /// to the Content Moderator APIs.
            /// </summary>
            private const int throttleRate = 2000;


### <a name="create-content-moderator-client-object"></a>Создание объекта для клиента Content Moderator

Добавьте следующие определения методов в пространство имен VideoTranscriptReviews, в класс Program.

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        return new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey))
        {
            BaseUrl = AzureBaseURL
        };
    }

## <a name="create-a-video-review"></a>Создание проверки видео

Создайте проверку видео с помощью **ContentModeratorClient.Reviews.CreateVideoReviews**. Дополнительные сведения см. в[ документации по API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4).

**CreateVideoReviews** принимает следующие обязательные параметры.
1. Строка, обозначающая тип MIME, который должен иметь значение "application/json". 
1. Имя команды Content Moderator
1. Объект **IList<CreateVideoReviewsBodyItem>**. Каждый объект **CreateVideoReviewsBodyItem** представляет отдельную проверку видео. В этом кратком руководстве проверки создаются по одной.

**CreateVideoReviewsBodyItem** имеет несколько свойств. Необходимо задать по меньшей мере следующие свойства.
- **Content**. URL-адрес видео для проверки.
- **ContentId**. Идентификатор, который будет присвоен проверке видео.
- **Status**. Укажите здесь значение "Unpublished" (Неопубликованное). Если значение не задано, по умолчанию используется Pending (Ожидание), что означает, что проверка видео уже опубликована и ожидает подтверждения человеком. После публикации проверки видео вы не сможете добавлять в нее кадры, расшифровки или результаты модерации расшифровки.

> [!NOTE]
> **CreateVideoReviews** возвращает IList<string>. Каждая из этих строк содержит идентификатор проверки видео. Эти идентификаторы являются глобально уникальными и их значения не совпадают со значениями свойства **ContentId**. 

Добавьте следующие определения методов в пространство имен VideoReviews, в класс Program.

    /// <summary>
    /// Create a video review. For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="id">The ID to assign to the video review.</param>
    /// <param name="content">The URL of the video to review.</param>
    /// <returns>The ID of the video review.</returns>
    private static string CreateReview(ContentModeratorClient client, string id, string content)
    {
        Console.WriteLine("Creating a video review.");

        List<CreateVideoReviewsBodyItem> body = new List<CreateVideoReviewsBodyItem>() {
            new CreateVideoReviewsBodyItem
            {
                Content = content,
                ContentId = id,
                /* Note: to create a published review, set the Status to "Pending".
                However, you cannot add video frames or a transcript to a published review. */
                Status = "Unpublished",
            }
        };

        var result = client.Reviews.CreateVideoReviews("application/json", TeamName, body);

        Thread.Sleep(throttleRate);

        // We created only one review.
        return result[0];
    }

> [!NOTE]
> Ключ службы Content Moderator предусматривает ограничение частоты на количество запросов в секунду (RPS). Если превысить ограничение, пакет SDK создает исключение с кодом ошибки 429. 
>
> Ключ уровня "Бесплатный" предусматривает ограничение в один RPS.

## <a name="add-transcript-to-video-review"></a>Добавление расшифровки в проверку видео

Добавьте расшифровку в проверку видео с помощью **ContentModeratorClient.Reviews.AddVideoTranscript**. **AddVideoTranscript** принимает следующие обязательные параметры.
1. Идентификатор команды Content Moderator.
1. Идентификатор проверки видео, полученный от **CreateVideoReviews**.
1. Объект **Stream**, который содержит расшифровку.

Расшифровка должна иметь формат WebVTT. Дополнительные сведения см. в статье [о формате Web Video Text Tracks (WebVTT)](https://www.w3.org/TR/webvtt1/).

> [!NOTE]
> Эта программа использует пример расшифровки в формате VTT. В реальном решении можно [создать расшифровку из видео](https://docs.microsoft.com/azure/media-services/media-services-index-content) с помощью службы индексатора мультимедийных данных Azure.

Добавьте следующие определения методов в пространство имен VideoTranscriptReviews, в класс Program.

    /// <summary>
    /// Add a transcript to the indicated video review.
    /// The transcript must be in the WebVTT format.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b8b2e7151f0b10d451fe
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscript(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript to the review with ID {0}.", review_id);
        client.Reviews.AddVideoTranscript(TeamName, review_id, new MemoryStream(System.Text.Encoding.UTF8.GetBytes(transcript)));
        Thread.Sleep(throttleRate);
    }

## <a name="add-a-transcript-moderation-result-to-video-review"></a>Добавление результата модерации расшифровки в проверку видео

Помимо самой расшифровки, в проверку видео следует добавить результат модерации для этой расшифровки. Это можно сделать с помощью **ContentModeratorClient.Reviews.AddVideoTranscriptModerationResult**. Дополнительные сведения см. в[ документации по API](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff).

**AddVideoTranscriptModerationResult** принимает следующие обязательные параметры.
1. Строка, обозначающая тип MIME, который должен иметь значение "application/json". 
1. Имя команды Content Moderator
1. Идентификатор проверки видео, полученный от **CreateVideoReviews**.
1. Список IList<TranscriptModerationBodyItem>. Объект **TranscriptModerationBodyItem** имеет следующие свойства.
- **Terms**. Список IList<TranscriptModerationBodyItemTermsItem>. Объект **TranscriptModerationBodyItemTermsItem** имеет следующие свойства.
- **Index**. Отсчитываемый от нуля индекс терминов.
- **Term**. Строка, содержащая термин.
- **Timestamp**. Строка, указывающая время в расшифровке (в секундах), в котором обнаружен указанный термин.

Расшифровка должна иметь формат WebVTT. Дополнительные сведения см. в статье [о формате Web Video Text Tracks (WebVTT)](https://www.w3.org/TR/webvtt1/).

Добавьте следующие определения методов в пространство имен VideoTranscriptReviews, в класс Program. Этот метод передает расшифровку в метод **ContentModeratorClient.TextModeration.ScreenText**. Также он преобразует результат в IList<TranscriptModerationBodyItem> и передает его в **AddVideoTranscriptModerationResult**.

    /// <summary>
    /// Add the results of moderating a video transcript to the indicated video review.
    /// For more information, see the API reference:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7b93ce7151f0b10d451ff
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    /// <param name="transcript">The video transcript.</param>
    static void AddTranscriptModerationResult(ContentModeratorClient client, string review_id, string transcript)
    {
        Console.WriteLine("Adding a transcript moderation result to the review with ID {0}.", review_id);

        // Screen the transcript using the Text Moderation API. For more information, see:
        // https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f
        Screen screen = client.TextModeration.ScreenText("eng", "text/plain", transcript);

        // Map the term list returned by ScreenText into a term list we can pass to AddVideoTranscriptModerationResult.
        List<TranscriptModerationBodyItemTermsItem> terms = new List<TranscriptModerationBodyItemTermsItem>();
        if (null != screen.Terms)
        {
            foreach (var term in screen.Terms)
            {
                if (term.Index.HasValue)
                {
                    terms.Add(new TranscriptModerationBodyItemTermsItem(term.Index.Value, term.Term));
                }
            }
        }

        List<TranscriptModerationBodyItem> body = new List<TranscriptModerationBodyItem>()
        {
            new TranscriptModerationBodyItem()
            {
                Timestamp = "0",
                Terms = terms
            }
        };

        client.Reviews.AddVideoTranscriptModerationResult("application/json", TeamName, review_id, body);

        Thread.Sleep(throttleRate);
    }


## <a name="publish-video-review"></a>Публикация проверки видео

Для публикации проверки видео применяется **ContentModeratorClient.Reviews.CreateVideoReviews**. **PublishVideoReview** принимает следующие обязательные параметры.
1. Имя команды Content Moderator
1. Идентификатор проверки видео, полученный от **CreateVideoReviews**.

Добавьте следующие определения методов в пространство имен VideoReviews, в класс Program.

    /// <summary>
    /// Publish the indicated video review. For more information, see the reference API:
    /// https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/59e7bb29e7151f0b10d45201
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    /// <param name="review_id">The video review ID.</param>
    private static void PublishReview(ContentModeratorClient client, string review_id)
    {
        Console.WriteLine("Publishing the review with ID {0}.", review_id);
        client.Reviews.PublishVideoReview(TeamName, review_id);
        Thread.Sleep(throttleRate);
    }

## <a name="putting-it-all-together"></a>Сборка

Добавьте определение метода **Main** в пространство имен VideoTranscriptReviews, в класс Program. И наконец, завершите класс Program и пространство имен VideoTranscriptReviews.

> [!NOTE]
> Эта программа использует пример расшифровки в формате VTT. В реальном решении можно [создать расшифровку из видео](https://docs.microsoft.com/azure/media-services/media-services-index-content) с помощью службы индексатора мультимедийных данных Azure. 

    static void Main(string[] args)
    {
        using (ContentModeratorClient client = NewClient())
        {
            // Create a review with the content pointing to a streaming endpoint (manifest)
            var streamingcontent = "https://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest";
            string review_id = CreateReview(client, "review1", streamingcontent);

            var transcript = @"WEBVTT

            01:01.000 --> 02:02.000
            First line with a crap word in a transcript.

            02:03.000 --> 02:25.000
            This is another line in the transcript.
            ";

            AddTranscript(client, review_id, transcript);

            AddTranscriptModerationResult(client, review_id, transcript);

            // Publish the review
            PublishReview(client, review_id);

            Console.WriteLine("Open your Content Moderator Dashboard and select Review > Video to see the review.");
            Console.WriteLine("Press any key to close the application.");
            Console.Read();
        }
    }

## <a name="run-the-program-and-review-the-output"></a>Запуск программы и просмотр выходных данных

Запустив созданное приложение, вы увидите следующие строки выходных данных:

    Creating a video review.
    Adding a transcript to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Adding a transcript moderation result to the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Publishing the review with ID 201801v5b08eefa0d2d4d64a1942aec7f5cacc3.
    Open your Content Moderator Dashboard and select Review > Video to see the review.
    Press any key to close the application.

## <a name="navigate-to-your-video-transcript-review"></a>Переход к проверке расшифровки видео

Теперь вы можете открыть созданную проверку расшифровки видео в средстве проверки Content Moderator, перейдя к экрану **Review (Проверка)**>**Video (Видео)**>**Transcript (Расшифровка)**.

Здесь вы увидите следующие компоненты:
- две строки добавленной вами расшифровки;
- нецензурный термин, обнаруженный и выделенный службой модерации текста.
- Нажатием на текст расшифровки вы можете запустить воспроизведение видео с указанной метки времени

![Проверка расшифровки видео для оценки человеком](images/ams-video-transcript-review.PNG)

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как создать [проверку видео](video-reviews-quickstart-dotnet.md) в средстве проверки.

Ознакомьтесь с подробным руководством по разработке [полного решения для модерации видео](video-transcript-moderation-review-tutorial-dotnet.md).

[Скачайте решение Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) для работы с этим и другими краткими руководствами по Content Moderator для .NET .
