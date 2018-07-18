---
title: 'Azure Content Moderator: модерация видео | Документация Майкрософт'
description: Модерация видео позволяет выполнять поиск содержимого только для взрослых и (или) содержимого непристойного характера.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: ef58f5990d4a0a19ab2b8c61b42ab2a0754dc6fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382489"
---
# <a name="video-moderation"></a>Модерация видео

Сегодня число ежедневных просмотров видеороликов на популярных веб-сайтах и в социальных сетях измеряется миллиардами. И этот показатель продолжает расти. Применив службы на основе машинного обучения для поиска содержимого только для взрослых и (или) содержимого непристойного характера, вы сможете сократить затраты на модерацию.

## <a name="sign-up-for-the-content-moderator-media-processor-public-preview"></a>Регистрация для использования обработчика мультимедиа Content Moderator (предварительная версия)

### <a name="create-a-free-azure-account"></a>Создание бесплатной учетной записи Azure

[Здесь](https://azure.microsoft.com/free/) можно создать бесплатную учетную запись Azure, если у вас ее еще нет.

### <a name="create-an-azure-media-services-account"></a>Создание учетной записи служб мультимедиа Azure

Возможности Content Moderator по обработке видео доступны в виде **обработчика мультимедиа**, бесплатно предоставляемого в Службах мультимедиа Azure в общедоступной предварительной версии.

[Создайте учетную запись Служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) в подписке Azure.

### <a name="get-azure-active-directory-credentials"></a>Получение учетных данных Azure Active Directory

   1. Из [статьи о портале Служб мультимедиа Azure](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad) вы можете узнать, как с помощью портала Azure получить учетные данные для аутентификации в Azure Active Directory.
   1. Прочтите [статью о Службах мультимедиа Azure .NET](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad), чтобы научиться использовать учетные данные Azure Active Directory с пакетом SDK для .NET.

   > [!NOTE]
   > В примере кода в этом кратком руководстве используется метод **аутентификации субъекта-службы**, который описан в обеих указанных выше статьях.

Получив учетные данные AMS, вы можете использовать обработчик мультимедиа двумя способами.

## <a name="use-azure-media-services-explorer"></a>Использование обозревателя Служб мультимедиа Azure

С помощью интерактивного [обозревателя Служб мультимедиа Azure (AMS)](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) можно просматривать учетную запись AMS, передавать видео и выполнять проверки в обработчике мультимедиа Content Moderator. [Скачайте и установите его](https://github.com/Azure/Azure-Media-Services-Explorer/releases) из GitHub, а также [изучите исходный код](http://github.com/Azure/Azure-Media-Services-Explorer), чтобы лучше разобраться в применении пакета SDK для AMS.

![Обозреватель Служб мультимедиа Azure с Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>Краткое руководство для .NET с использованием Visual Studio и C#

1. Добавьте в свое решение новый проект **Консольное приложение (.NET Framework)**.

   В примере кода назовите проект **VideoModeration**.

1. Выберите этот проект единственным запускаемым проектом для решения.

### <a name="install-required-packages"></a>Установка необходимых пакетов

Установите следующие пакеты NuGet, которые можно получить на [сайте NuGet](https://www.nuget.org/):

- windowsazure.mediaservices;
- windowsazure.mediaservices.extensions.

### <a name="update-the-programs-using-statements"></a>Обновление инструкций using в программе

Измените инструкции using в вашей программе.

    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.IO;
    using System.Threading;
    using Microsoft.WindowsAzure.Storage.Blob;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using System.Collections.Generic;


### <a name="initialize-application-specific-settings"></a>Инициализация параметров приложения

Добавьте следующие статические поля в класс **Program** в файле Program.cs:

    // declare constants and globals
    private static CloudMediaContext _context = null;
    private static CloudStorageAccount _StorageAccount = null;

    // Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
    // a list of Blobs to be processed.
    static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
    static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
    static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

    private static StorageCredentials _StorageCredentials = null;

    // Azure Media Services authentication. See the quickstart for how to get these.
    private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
    private const string CLIENT_ID = "YOUR CLIENT ID"
    private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

    // REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
    private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

    // Content Moderator Media Processor Nam
    private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

    // Input and Output files in the current directory of the executable
    private const string INPUT_FILE = "VIDEO FILE NAME";
    private const string OUTPUT_FOLDER = "";

### <a name="create-a-preset-file-json"></a>Создание файла предустановки (json)

Создайте в текущем каталоге JSON-файл с номером версии.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>Добавление кода в метод Main

С помощью этого метода Main сначала создается контекст мультимедиа Azure, а затем контекст хранилища Azure, который позволяет использовать видео из хранилища больших двоичных объектов.
Остальная часть кода позволяет проверить видео из локальной папки или одного либо нескольких больших двоичных объектов в контейнере хранилища Azure.
Закомментировав разные строки кода, вы можете попробовать все варианты поочередно.

    // Create Azure Media Context
    CreateMediaContext();

    // Create Storage Context
    CreateStorageContext();

    // Use a file as the input.
    // IAsset asset = CreateAssetfromFile();
    
    // -- OR ---
    
    // Or a blob as the input
    // IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

    // Then submit the asset to Content Moderator
    // RunContentModeratorJob(asset);

    //-- OR ----

    // Just run the content moderator on all blobs in a list (from a Blob Container)
    RunContentModeratorJobOnBlobs();

### <a name="add-the-code-to-create-an-azure-media-context"></a>Добавление кода для создания контекста мультимедиа Azure

    /// <summary>
    /// Creates a media context from azure credentials
    /// </summary>
    static void CreateMediaContext()
    {
        // Get Azure AD credentials
        var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
            new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
            AzureEnvironments.AzureCloudEnvironment);

        // Initialize an Azure AD token
        var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

        // Create a media context
        _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
    }

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Добавление кода для создания контекста хранилища Azure
Контекст хранилища, созданный на основе учетных данных хранилища, позволяет получить доступ к хранилищу BLOB-объектов.

    /// <summary>
    /// Creates a storage context from the AMS associated storage name and key
    /// </summary>
    static void CreateStorageContext()
    {
        // Get a reference to the storage account associated with a Media Services account. 
        if (_StorageCredentials == null)
        {
            _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
        }
        _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
    }

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Добавление кода для создания ресурсов мультимедиа Azure из локального файла и (или) хранилища BLOB-объектов
Обработчик мультимедиа Content Moderator запускает задания для **ресурсов** на платформе Служб мультимедиа Azure.
С помощью этих методов создаются ресурсы из локального файла или связанного BLOB-объекта.

    /// <summary>
    /// Creates an Azure Media Services Asset from the video file
    /// </summary>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromFile()
    {
        return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
    }

    /// <summary>
    /// Creates an Azure Media Services asset from your blog storage
    /// </summary>
    /// <param name="Blob"></param>
    /// <returns>Asset</returns>
    static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
    {
        // Create asset from the FIRST blob in the list and return it
        return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
    }

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Добавление в контейнер кода для проверки коллекции видео (сохраненной в виде больших двоичных объектов)

    /// <summary>
    /// Runs the Content Moderator Job on all Blobs in a given container name
    /// </summary>
    static void RunContentModeratorJobOnBlobs()
    {
        // Get the reference to the list of Blobs. See the following method.
        var blobList = GetBlobsList();

        // Iterate over the Blob list items or work on specific ones as needed
        foreach (var sourceBlob in blobList)
        {
            // Create an Asset
            IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                               _StorageCredentials, AssetCreationOptions.None);
            asset.Update();

            // Submit to Content Moderator
            RunContentModeratorJob(asset);
        }
    }

    /// <summary>
    /// Get all blobs in your container
    /// </summary>
    /// <returns></returns>
    static IEnumerable<IListBlobItem> GetBlobsList()
    {
        // Get a reference to the Container within the Storage Account
        // that has the files (blobs) for moderation
        CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
        CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

        // Get the reference to the list of Blobs 
        var blobList = MediaBlobContainer.ListBlobs();
        return blobList;
    }

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Добавление метода для выполнения задания Content Moderator

    /// <summary>
    /// Run the Content Moderator job on the designated Asset from local file or blob storage
    /// </summary>
    /// <param name="asset"></param>
    static void RunContentModeratorJob(IAsset asset)
    {
        // Grab the presets
        string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

        // grab instance of Azure Media Content Moderator MP
        IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

        // create Job with Content Moderator task
        IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
                asset.AssetFiles.First() + "_" + Guid.NewGuid()));

        ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
                mp, configuration,
                TaskOptions.None);
        contentModeratorTask.InputAssets.Add(asset);
        contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
            AssetCreationOptions.None);

        job.Submit();


        // Create progress printing and querying tasks
        Task progressPrintTask = new Task(() =>
        {
            IJob jobQuery = null;
            do
            {
                var progressContext = _context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                    .First();
                    Console.WriteLine(string.Format("{0}\t{1}",
                    DateTime.Now,
                    jobQuery.State));
                    Thread.Sleep(10000);
             }
             while (jobQuery.State != JobState.Finished &&
             jobQuery.State != JobState.Error &&
             jobQuery.State != JobState.Canceled);
        });
        progressPrintTask.Start();

        Task progressJobTask = job.GetExecutionProgressTask(
        CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            ErrorDetail error = job.Tasks.First().ErrorDetails.First();
            Console.WriteLine(string.Format("Error: {0}. {1}",
            error.Code,
            error.Message));
        }

        DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
    }

### <a name="add-a-couple-of-helper-functions"></a>Добавление нескольких вспомогательных функций

Эти методы позволяют скачать выходной файл Content Moderator в формате JSON из ресурса Служб мультимедиа Azure и отслеживать состояние задания модерации, чтобы программа могла регистрировать в консоли эти сведения о состоянии.

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    // event handler for Job State
    static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
                Console.WriteLine();
                Console.WriteLine("Job finished.");
                break;
            case JobState.Canceling:
            case JobState.Queued:
            case JobState.Scheduled:
            case JobState.Processing:
                Console.WriteLine("Please wait...\n");
                break;
            case JobState.Canceled:
                Console.WriteLine("Job is canceled.\n");
                break;
            case JobState.Error:
                Console.WriteLine("Job failed.\n");
                break;
            default:
                break;
        }
    }

### <a name="run-the-program-and-review-the-output"></a>Запуск программы и просмотр выходных данных

Когда задание модерации содержимого будет выполнено, изучите полученный ответ в формате JSON. Он состоит из следующих элементов:

- сводка по видео;
- **видеоролики**, обозначенные как "**fragments**";
- **ключевые кадры**, обозначенные как "**events**" с флагом необходимости проверки **"reviewRecommended" (принимает значение true или false)**, который присваивается на основе оценок **Asult** и **Racy**;
- значения **start** (начало), **duration** (длительность), **totalDuration** (общая длительность) и **timestamp** (метка времени), выраженные в тактах. Чтобы получить значение в секундах, поделите величину в тактах на значение **timescale** (шкала времени).
 
> [!NOTE]

> - `adultScore` обозначает потенциальное наличие и прогнозируемую оценку содержимого, которое в определенных обстоятельствах может считаться порнографическим или предназначенным только для взрослых.
> - `racyScore` обозначает потенциальное наличие и прогнозируемую оценку содержимого, которое в определенных обстоятельствах может считаться сексуально окрашенными или не предназначенным для детей.
> - Оценки `adultScore` и `racyScore` принимают значения от 0 до 1. Чем выше оценка, тем более подходящей модель считает соответствующую категорию. Эта предварительная версия использует статистическую модель прогнозирования, а не оценки, кодированные вручную. Корпорация Майкрософт рекомендует протестировать ее на своих данных, чтобы проверить применимость анализа по каждой категории.
> - `reviewRecommended` принимает значения true или false в зависимости от внутренних порогов оценки. Клиенты могут на выбор использовать значения по умолчанию или настраивать собственные пороги в соответствии с действующими политиками.
>

    {
    "version": 2,
    "timescale": 90000,
    "offset": 0,
    "framerate": 50,
    "width": 1280,
    "height": 720,
    "totalDuration": 18696321,
    "fragments": [
    {
      "start": 0,
      "duration": 18000
    },
    {
      "start": 18000,
      "duration": 3600,
      "interval": 3600,
      "events": [
        [
          {
            "reviewRecommended": false,
            "adultScore": 0.00001,
            "racyScore": 0.03077,
            "index": 5,
            "timestamp": 18000,
            "shotIndex": 0
          }
        ]
      ]
    },
    {
      "start": 18386372,
      "duration": 119149,
      "interval": 119149,
      "events": [
        [
          {
            "reviewRecommended": true,
            "adultScore": 0.00000,
            "racyScore": 0.91902,
            "index": 5085,
            "timestamp": 18386372,
            "shotIndex": 62
          }
        ]
      ]
    }
    ]
    }

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как создавать [обзоры видео](video-reviews-quickstart-dotnet.md) на основе выходных данных модерации.

Добавьте в обзоры видео [модерацию расшифровки](video-transcript-moderation-review-tutorial-dotnet.md).

Ознакомьтесь с подробным руководством по созданию [полного решения для модерации видео и расшифровки](video-transcript-moderation-review-tutorial-dotnet.md).

[Скачайте решение Visual Studio](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) для работы с этим и другими краткими руководствами по Content Moderator для .NET.
