---
title: Краткое руководство по запуску заданий модерации в Content Moderator с помощью .NET
titlesuffix: Azure Cognitive Services
description: Как запускать задания модерации с использованием пакета SDK Azure Content Moderator для .NET.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: sajagtap
ms.openlocfilehash: 6045d6daf2abace6e2b38bd6fd6e22516e3a60a0
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227446"
---
# <a name="quickstart-start-moderation-jobs-using-net"></a>Краткое руководство по запуску заданий модерации с помощью .NET

В этой статье содержатся сведения и примеры кода, которые помогут вам приступить к работе с [пакетом SDK Content Moderator для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Вы научитесь выполнять такие задачи:
 
- запуск задания модерации для проверки содержимого и создания проверок для модераторов-пользователей;
- получение состояния ожидающей проверки;
- отслеживание и получение окончательного состояния проверки;
- отправка результата на URL-адрес обратного вызова.

В этой статье предполагается, что вы уже работали с Visual Studio и C#.

## <a name="sign-up-for-content-moderator"></a>Регистрация в службе Content Moderator

Прежде чем использовать службы Content Moderator через REST API или пакет SDK, необходимо получить ключ подписки.
Изучите [краткое руководство](quick-start.md) о том, как можно получить ключ.

## <a name="sign-up-for-a-review-tool-account-if-not-completed-in-the-previous-step"></a>Регистрация учетной записи средства проверки (если не сделано на предыдущем этапе)

Если вы получили Content Moderator на портале Azure, [зарегистрируйте учетную запись средства проверки](https://contentmoderator.cognitive.microsoft.com/) и создайте команду проверки. Чтобы вызывать API проверки для запуска заданий и просматривать результаты в средстве проверки, вам понадобится идентификатор команды и средство проверки.

## <a name="ensure-your-api-key-can-call-the-review-api-for-review-creation"></a>Проверка, может ли ключ API вызвать API проверки для создания соответствующих заданий

Если вы начали с портала Azure, после выполнения предыдущих шагов у вас может получиться два ключа Content Moderator. 

Если в своем примере пакета SDK вы планируете использовать ключ API, предоставленный платформой Azure, выполните [эти действия](review-tool-user-guide/credentials.md#use-the-azure-account-with-the-review-tool-and-review-api), чтобы разрешить приложению вызывать API проверки и создавать соответствующие задания.

Если вы используете бесплатный пробный ключ, сгенерированный средством проверки, ваша учетная запись средства проверки уже знает об этом ключе, поэтому никакие дополнительные действия не требуются.

## <a name="define-a-custom-moderation-workflow"></a>Определение пользовательского рабочего процесса модерации

Задание модерации проверяет содержимое с помощью интерфейсов API и использует **рабочий процесс**, чтобы определить, следует ли создавать проверки.
Хотя инструмент проверки содержит рабочий процесс по умолчанию, давайте [определим пользовательский рабочий процесс](Review-Tool-User-Guide/Workflows.md) для этого краткого руководства.

Имя рабочего процесса используется в коде, который запускает задание модерации.

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

Чтобы создать для своей подписки клиент Content Moderator, добавьте следующий фрагмент кода.

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

### <a name="initialize-application-specific-settings"></a>Инициализация параметров приложения

Добавьте следующие константы и статические поля в класс **Program** в файле Program.cs.

> [!NOTE]
> Константе TeamName присваивается имя, указанное вами при создании подписки Content Moderator. Получить TeamName можно на [веб-сайте Content Moderator](https://westus.contentmoderator.cognitive.microsoft.com/).
> После входа в меню **Settings** (Параметры) (значок шестеренки) выберите **Credentials** (Учетные данные).
>
> Имя группы указано в поле **Id** (Идентификатор) в разделе **API**.


    /// <summary>
    /// The moderation job will use this workflow that you defined earlier.
    /// See the quickstart article to learn how to setup custom workflows.
    /// </summary>
    private const string WorkflowName = "OCR";
    
    /// <summary>
    /// The name of the team to assign the job to.
    /// </summary>
    /// <remarks>This must be the team name you used to create your 
    /// Content Moderator account. You can retrieve your team name from
    /// the Content Moderator web site. Your team name is the Id associated 
    /// with your subscription.</remarks>
    private const string TeamName = "***";

    /// <summary>
    /// The URL of the image to create a review job for.
    /// </summary>
    private const string ImageUrl =
        "https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png";

    /// <summary>
    /// The name of the log file to create.
    /// </summary>
    /// <remarks>Relative paths are relative to the execution directory.</remarks>
    private const string OutputFile = "OutputLog.txt";

    /// <summary>
    /// The number of seconds to delay after a review has finished before
    /// getting the review results from the server.
    /// </summary>
    private const int latencyDelay = 45;

    /// <summary>
    /// The callback endpoint for completed reviews.
    /// </summary>
    /// <remarks>Reviews show up for reviewers on your team. 
    /// As reviewers complete reviews, results are sent to the
    /// callback endpoint using an HTTP POST request.</remarks>
    private const string CallbackEndpoint = "";

## <a name="add-code-to-auto-moderate-create-a-review-and-get-the-job-details"></a>Добавление кода для автоматической модерации, создания проверки и получения сведений о задании

> [!Note]
> На практике в качестве URL-адреса обратного вызова **CallbackEndpoint** указывается URL-адрес, на который отправляются результаты проверки вручную (с помощью HTTP-запроса POST).

Сначала добавьте в метод **main** следующий код.

    using (TextWriter writer = new StreamWriter(OutputFile, false))
    {
        using (var client = Clients.NewClient())
        {
            writer.WriteLine("Create review job for an image.");
            var content = new Content(ImageUrl);
        
            // The WorkflowName contains the name of the workflow defined in the online review tool.
            // See the quickstart article to learn more.
            var jobResult = client.Reviews.CreateJobWithHttpMessagesAsync(
                    TeamName, "image", "contentID", WorkflowName, "application/json", content, CallbackEndpoint);

            // Record the job ID.
            var jobId = jobResult.Result.Body.JobIdProperty;

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                jobResult.Result.Body, Formatting.Indented));

            Thread.Sleep(2000);
            writer.WriteLine();

            writer.WriteLine("Get review job status.");
            var jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
                    TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
                    jobDetails.Result.Body, Formatting.Indented));

            Console.WriteLine();
            Console.WriteLine("Perform manual reviews on the Content Moderator site.");
            Console.WriteLine("Then, press any key to continue.");
            Console.ReadKey();

            Console.WriteLine();
            Console.WriteLine($"Waiting {latencyDelay} seconds for results to propagate.");
            Thread.Sleep(latencyDelay * 1000);

            writer.WriteLine("Get review details.");
            jobDetails = client.Reviews.GetJobDetailsWithHttpMessagesAsync(
            TeamName, jobId);

            // Log just the response body from the returned task.
            writer.WriteLine(JsonConvert.SerializeObject(
            jobDetails.Result.Body, Formatting.Indented));
        }
        writer.Flush();
        writer.Close();
    }

> [!NOTE]
> Ключ службы Content Moderator предусматривает ограничение частоты запросов в секунду (RPS). Если превысить ограничение, пакет SDK порождает исключение с кодом ошибки 429. 
>
> Ключ уровня "Бесплатный" предусматривает ограничение в один RPS.

## <a name="run-the-program-and-review-the-output"></a>Запуск программы и просмотр выходных данных

В консоли отображается пример выходных данных.

    Perform manual reviews on the Content Moderator site.
    Then, press any key to continue.

Войдите в инструмент проверки Content Moderator, чтобы просмотреть ожидающую проверку изображений.

Нажмите кнопку **Next** (Далее) для отправки.

![Проверка изображений модераторами-пользователями](images/ocr-sample-image.PNG)

## <a name="see-the-sample-output-in-the-log-file"></a>Просмотр примера выходных данных в файле журнала

> [!NOTE]
> В выходном файле строки **Teamname**, **ContentId**, **CallBackEndpoint** и **WorkflowId** отражают значения, которые вы использовали ранее.

    Create moderation job for an image.
    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

    Get review details.
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": [
        {
            "Ts": "2018-01-07T00:38:26.7714671",
            "Msg": "Successfully got hasText response from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:26.4181346",
            "Msg": "Getting hasText from Moderator"
        },
        {
            "Ts": "2018-01-07T00:38:25.5122828",
            "Msg": "Starting Execution - Try 1"
        }
        ]
    }


## <a name="your-callback-url-if-provided-receives-this-response"></a>Этот ответ возвращается на URL-адрес обратного вызова, если он указан.

Вы получите примерно такой ответ:

> [!NOTE]
> В ответе на обратный вызов строки **ContentId** и **WorkflowId** отражают значения, использованные вами ранее.

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe",
        "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
        "WorkFlowId": "OCR",
        "Status": "Complete",
        "ContentType": "Image",
        "CallBackType": "Job",
        "ContentId": "contentID",
        "Metadata": {
            "hastext": "True",
            "ocrtext": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
            "imagename": "contentID"
        }
    }


## <a name="next-steps"></a>Дополнительная информация

Получите [пакет SDK Content Moderator для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) и [решение для Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator). Они вам понадобятся для работы с этим и другими руководствами по Content Moderator для .NET, а также для реализации ваших интеграционных решений.
