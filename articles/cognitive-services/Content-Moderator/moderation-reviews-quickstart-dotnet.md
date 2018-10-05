---
title: Быстрое начало. Создание проверок с помощью .NET — Content Moderator
titlesuffix: Azure Cognitive Services
description: Как создавать проверки с помощью пакета SDK Azure Content Moderator для .NET.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: ce90c5f691a0a8a333161f3135856d720d1de310
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226591"
---
# <a name="quickstart-create-reviews-using-net"></a>Быстрое начало. Создание проверок с помощью .NET

В этой статье содержатся сведения и примеры кода, которые помогут приступить к работе с [пакетом SDK Content Moderator для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) в следующих целях:
 
- создание набора проверок для модераторов-пользователей;
- получение состояния существующих проверок для модераторов-пользователей.

Как правило, содержимое проходит некоторую автоматическую модерацию перед пользовательской проверкой. В этой статье рассматривается только создание проверки для пользовательской модерации. Более полный сценарий описан в руководствах [Модерация контента Facebook](facebook-post-moderation.md) и [Модерирование каталога электронной коммерции](ecommerce-retail-catalog-moderation.md).

В этой статье предполагается, что вы уже работали с Visual Studio и C#.

## <a name="sign-up-for-content-moderator"></a>Регистрация в службе Content Moderator

Прежде чем использовать службы Content Moderator через REST API или пакет SDK, необходимо получить ключ подписки.
Изучите [краткое руководство](quick-start.md) о том, как можно получить ключ.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Зарегистрируйте учетную запись средства проверки, если вы еще не создали ее на предыдущем шаге

Если на портале Azure вы также получили Content Moderator, то также [зарегистрируйте учетную запись средства проверки](https://contentmoderator.cognitive.microsoft.com/) и создайте команду проверки. Чтобы вызвать API обзора, начать работу и просмотреть проверки в средстве проверок, вам понадобиться идентификатор команды и средство проверки.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Убедитесь, что ваш ключ API может вызвать API проверки для ее создания

Выполнив предыдущие шаги, вы можете получить два ключа Content Moderator, если вы начали работу в портале Azure. 

Если вы планируете использовать ключ API, предоставленный Azure, в своем примере пакета SDK, выполните шаги, указанные в разделе [Как использовать учетную запись Azure с инструментом проверки](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api), чтобы ваше приложение вызывало API проверки и ее создавало.

Если вы используете бесплатный пробный ключ, сгенерированный средством проверки, то ваша учетная запись средства проверки уже знает о ключе, поэтому никаких дополнительных шагов не требуется.

## <a name="create-your-visual-studio-project"></a>Создание проекта Visual Studio

1. Добавьте новый проект **Консольное приложение (.NET Framework)** в свое решение.

   В примере кода назовите проект **CreateReviews**.

1. Выберите этот проект в качестве единственного запускаемого проекта для решения.

### <a name="install-required-packages"></a>Установка необходимых пакетов

Установите следующие пакеты NuGet:

- Microsoft.Azure.CognitiveServices.ContentModerator;
- Microsoft.Rest.ClientRuntime
- Newtonsoft.Json.

### <a name="update-the-programs-using-statements"></a>Обновление инструкций using программы

Измените инструкции using в вашей программе.

    using Microsoft.Azure.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator;
    using Microsoft.CognitiveServices.ContentModerator.Models;
    using Newtonsoft.Json;
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Threading;

### <a name="create-the-content-moderator-client"></a>Создание клиента Content Moderator

Добавьте следующий фрагмент кода, чтобы создать клиент Content Moderator для своей подписки.

> [!IMPORTANT]
> Укажите в полях **AzureRegion** и **CMSubscriptionKey** значения идентификатора региона и ключа подписки соответственно.


    /// <summary>
    /// Wraps the creation and configuration of a Content Moderator client.
    /// </summary>
    /// <remarks>This class library contains insecure code. If you adapt this 
    /// code for use in production, use a secure method of storing and using
    /// your Content Moderator subscription key.</remarks>
    public static class Clients
    {
        /// <summary>
        /// The region/location for your Content Moderator account, 
        /// for example, westus.
        /// </summary>
        private static readonly string AzureRegion = "YOUR API REGION";

        /// <summary>
        /// The base URL fragment for Content Moderator calls.
        /// </summary>
        private static readonly string AzureBaseURL =
            $"https://{AzureRegion}.api.cognitive.microsoft.com";

        /// <summary>
        /// Your Content Moderator subscription key.
        /// </summary>
        private static readonly string CMSubscriptionKey = "YOUR API KEY";

        /// <summary>
        /// Returns a new Content Moderator client for your subscription.
        /// </summary>
        /// <returns>The new client.</returns>
        /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
        /// When you have finished using the client,
        /// you should dispose of it either directly or indirectly. </remarks>
        public static ContentModeratorClient NewClient()
        {
            // Create and initialize an instance of the Content Moderator API wrapper.
            ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

            client.Endpoint = AzureBaseURL;
            return client;
        }
    }

## <a name="create-a-class-to-associate-internal-content-information-with-a-review-id"></a>Создание класса для связывания внутренних сведений о содержимом с идентификатором проверки

Добавьте следующий класс в класс **Program**.
Этот класс используется, чтобы связать идентификатор проверки с внутренним идентификатором содержимого для элемента.

    /// <summary>
    /// Associates the review ID (assigned by the service) to the internal
    /// content ID of the item.
    /// </summary>
    public class ReviewItem
    {
        /// <summary>
        /// The media type for the item to review.
        /// </summary>
        public string Type;

        /// <summary>
        /// The URL of the item to review.
        /// </summary>
        public string Url;

        /// <summary>
        /// The internal content ID for the item to review.
        /// </summary>
        public string ContentId;

        /// <summary>
        /// The ID that the service assigned to the review.
        /// </summary>
        public string ReviewId;
    }

### <a name="initialize-application-specific-settings"></a>Инициализация параметров приложения

> [!NOTE]
> Для ключа службы Content Moderator установлен лимит числа запросов в секунду (RPS). Если его превысить, пакет SDK породит исключение с кодом ошибки 429. 
>
> Ключ уровня "Бесплатный" предусматривает ограничение в один RPS.

#### <a name="add-the-following-constants-to-the-program-class-in-programcs"></a>Добавьте следующие константы в класс **Program** в файле Program.cs.
    
    /// <summary>
    /// The minimum amount of time, in milliseconds, to wait between calls
    /// to the Image List API.
    /// </summary>
    private const int throttleRate = 3000;

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const double latencyDelay = 45;

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

#### <a name="add-the-following-constants-and-static-fields-to-the-program-class-in-programcs"></a>Добавьте следующие константы и статические поля в класс **Program** в файле Program.cs.

Измените эти значения с учетом данных своей подписки и команды.

> [!NOTE]
> Константе TeamName присваивается имя, указанное вами при создании подписки [инструмента проверки Content Moderator](https://contentmoderator.cognitive.microsoft.com/). Узнать значение TeamName можно в разделе **Credentials** (Учетные данные), щелкнув меню **Settings** (Параметры) (значок шестеренки).
>
> Имя группы указано в поле **Id** (Идентификатор) в разделе **API**.

    /// <summary>
    /// The name of the team to assign the review to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Content Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "YOUR REVIEW TEAM ID";

    /// <summary>
    /// The optional name of the subteam to assign the review to.
    /// </summary>
    private const string Subteam = null;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "YOUR API ENDPOINT";

    /// <summary>
    /// The media type for the item to review.
    /// </summary>
    /// <remarks>Valid values are "image", "text", and "video".</remarks>
    private const string MediaType = "image";

    /// <summary>
    /// The URLs of the images to create review jobs for.
    /// </summary>
    private static readonly string[] ImageUrls = new string[] {
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg"
        // add more if you want
    };

    /// <summary>
    /// The metadata key to initially add to each review item.
    /// </summary>
    private const string MetadataKey = "sc";

    /// <summary>
    /// The metadata value to initially add to each review item.
    /// </summary>
    private const string MetadataValue = "true";

#### <a name="add-the-following-static-fields-to-the-program-class-in-programcs"></a>Добавьте следующие статические поля в класс **Program** в файле Program.cs.

Эти поля используются для отслеживания состояния приложения.

    /// <summary>
    /// A static reference to the text writer to use for logging.
    /// </summary>
    private static TextWriter writer;

    /// <summary>
    /// The cached review information, associating a local content ID
    /// to the created review ID for each item.
    /// </summary>
    private static List<ReviewItem> reviewItems =
        new List<ReviewItem>();

## <a name="create-a-method-to-write-messages-to-the-log-file"></a>Создание метода для записи сообщений в файл журнала

Добавьте следующий метод в класс **Program**. 

    /// <summary>
    /// Writes a message to the log file, and optionally to the console.
    /// </summary>
    /// <param name="message">The message.</param>
    /// <param name="echo">if set to <c>true</c>, write the message to the console.</param>
    private static void WriteLine(string message = null, bool echo = false)
    {
        writer.WriteLine(message ?? String.Empty);

        if (echo)
        {
            Console.WriteLine(message ?? String.Empty);
        }
    }

## <a name="create-a-method-to-create-a-set-of-reviews"></a>Создание метода для создания набора проверок

Как правило, у разработчика определена бизнес-логика для идентификации входящих изображений, текста или видео, которые нужно проверять. Однако сейчас просто используйте фиксированный список изображений.

Добавьте следующий метод в класс **Program**.

    /// <summary>
    /// Create the reviews using the fixed list of images.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void CreateReviews(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Creating reviews for the following images:", true);

        // Create the structure to hold the request body information.
        List<CreateReviewBodyItem> requestInfo =
            new List<CreateReviewBodyItem>();

        // Create some standard metadata to add to each item.
        List<CreateReviewBodyItemMetadataItem> metadata =
            new List<CreateReviewBodyItemMetadataItem>(
            new CreateReviewBodyItemMetadataItem[] {
                new CreateReviewBodyItemMetadataItem(
                    MetadataKey, MetadataValue)
            });

        // Populate the request body information and the initial cached review information.
        for (int i = 0; i < ImageUrls.Length; i++)
        {
            // Cache the local information with which to create the review.
            var itemInfo = new ReviewItem()
            {
                Type = MediaType,
                ContentId = i.ToString(),
                Url = ImageUrls[i],
                ReviewId = null
            };

            WriteLine($" - {itemInfo.Url}; with id = {itemInfo.ContentId}.", true);

            // Add the item informaton to the request information.
            requestInfo.Add(new CreateReviewBodyItem(
                itemInfo.Type, itemInfo.Url, itemInfo.ContentId,
                CallbackEndpoint, metadata));

            // Cache the review creation information.
            reviewItems.Add(itemInfo);
        }

        var reviewResponse = client.Reviews.CreateReviewsWithHttpMessagesAsync(
            "application/json", TeamName, requestInfo);

        // Update the local cache to associate the created review IDs with
        // the associated content.
        var reviewIds = reviewResponse.Result.Body;
        for (int i = 0; i < reviewIds.Count; i++)
        {
            Program.reviewItems[i].ReviewId = reviewIds[i];
        }

        WriteLine(JsonConvert.SerializeObject(
        reviewIds, Formatting.Indented));

        Thread.Sleep(throttleRate);
    }

## <a name="create-a-method-to-get-the-status-of-existing-reviews"></a>Создание метода для получения состояния существующих проверок

Добавьте следующий метод в класс **Program**. 

> [!Note]
> На практике в качестве URL-адреса обратного вызова `CallbackEndpoint` указывается URL-адрес, на который отправляются результаты проверки вручную (с помощью HTTP-запроса POST).
> Можно изменить этот метод для проверки состояния ожидающих проверок.

    /// <summary>
    /// Gets the review details from the server.
    /// </summary>
    /// <param name="client">The Content Moderator client.</param>
    private static void GetReviewDetails(ContentModeratorClient client)
    {
        WriteLine(null, true);
        WriteLine("Getting review details:", true);
        foreach (var item in reviewItems)
        {
            var reviewDetail = client.Reviews.GetReviewWithHttpMessagesAsync(
                TeamName, item.ReviewId);

            WriteLine(
                $"Review {item.ReviewId} for item ID {item.ContentId} is " +
                $"{reviewDetail.Result.Body.Status}.", true);
            WriteLine(JsonConvert.SerializeObject(
                reviewDetail.Result.Body, Formatting.Indented));

            Thread.Sleep(throttleRate);
        }
    }

## <a name="add-code-to-create-a-set-of-reviews-and-check-its-status"></a>Добавление кода для создания набора проверок и проверки их состояния

Добавьте следующий код в метод **Main**.

Этот код моделирует многие операции, выполняемые для определения списка и управления им, а также для проверки изображений с его помощью. Возможности ведения журнала позволяют просматривать объекты ответа, создаваемые вызовами пакета SDK, отправленными к службе Content Moderator.

    using (TextWriter outputWriter = new StreamWriter(OutputFile, false))
    {
        writer = outputWriter;
        using (var client = Clients.NewClient())
        {
            CreateReviews(client);
            GetReviewDetails(client);

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propigate.");
            Thread.Sleep(latencyDelay * 1000);

            GetReviewDetails(client);
        }

        writer = null;
        outputWriter.Flush();
        outputWriter.Close();
    }

    Console.WriteLine();
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();

## <a name="run-the-program-and-review-the-output"></a>Запуск программы и просмотр выходных данных

Ниже приведен пример выходных данных.

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.

Войдите в инструмент проверки Content Moderator, чтобы просмотреть ожидающую проверку изображений с меткой **sc** со значением **true**. Вы также увидите теги по умолчанию **a** и **r**, а также настраиваемые теги, которые были определены в инструменте проверки. 

Нажмите кнопку **Next** (Далее) для отправки.

![Проверка изображений модераторами-пользователями](images/moderation-reviews-quickstart-dotnet.PNG)

Нажмите любую клавишу чтобы продолжить.

    Waiting 45 seconds for results to propagate.

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.

    Press any key to exit...

## <a name="check-out-the-following-output-in-the-log-file"></a>Просмотрите приведенные ниже выходные данные в файле журнала.

> [!NOTE]
> В выходном файле строки "\{teamname}" и "\{callbackUrl}" отражают значения для полей `TeamName` и `CallbackEndpoint` соответственно.

Идентификаторы проверок и URL-адреса изображений будут отличаться при каждом запуске приложения. По завершению проверки поле `reviewerResultTags` отображает теги, назначенные рецензентом элементу.

    Creating reviews for the following images:
        - https://moderatorsampleimages.blob.core.windows.net/samples/sample1.jpg; with id = 0.
    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Pending.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Pending",
        "reviewerResultTags": [],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

    Getting review details:
    Review 201712i46950138c61a4740b118a43cac33f434 for item ID 0 is Complete.
    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="your-callback-url-if-provided-receives-this-response"></a>Этот ответ возвращается на URL-адрес обратного вызова, если он указан.

Вы получите примерно такой ответ:

    {
        "ReviewId": "201801i48a2937e679a41c7966e838c92f5e649",
        "ModifiedOn": "2018-01-06T05:04:40.5525865Z",
        "ModifiedBy": "yourusername",
        "CallBackType": "Review",
        "ContentId": "0",
        "ContentType": "Image",
        "Metadata": {
            "sc": "true"
            },
        "ReviewerResultTags": {
            "a": "False",
            "r": "False",
        }
    }


## <a name="next-steps"></a>Дополнительная информация

Получите [пакет SDK Content Moderator для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) и [решение Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) для выполнения инструкций из этого и других кратких руководств по Content Moderator для .NET и приступите к интеграции.
