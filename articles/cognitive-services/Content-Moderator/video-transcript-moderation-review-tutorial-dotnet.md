---
title: 'Руководство: модерация видео и расшифровок речи с использованием .NET — Content Moderator'
titlesuffix: Azure Cognitive Services
description: Инструкции по модерации видео и расшифровок в .NET с помощью Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 1/27/2018
ms.author: sajagtap
ms.openlocfilehash: 12f03352373bebecb74b9dd8d31470ac337f5e71
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227577"
---
# <a name="tutorial-video-and-transcript-moderation"></a>Руководство: модерация видео и расшифровок речи

API-интерфейсы для работы с видео Content Moderator позволяют выполнять модерацию видео и создавать проверки видео в средстве пользовательской проверки. 

В этом подробном руководстве содержатся сведения о создании решения модерации видео и расшифровок с использованием автоматизированной модерации и создания проверок человеком в процессе.

Загрузите [консольное приложение C#](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) для этого руководства. Консольное приложение использует пакет SDK и связанные пакеты для выполнения следующих задач:

- сжатие входных видеоданных для ускорения обработки;
- модерация видео для получения снимков и кадров с аналитическими сведениями;
- использование меток времени кадров для создания эскизов (изображений);
- отправка меток времени и эскизов для создания проверок видео;
- преобразование речи видео в текст (расшифровка) с помощью API индексатора мультимедийных данных;
- модерация расшифровки с помощью службы модерации текста;
- добавление прошедших модерацию расшифровок в проверку видео.

## <a name="sample-program-outputs"></a>Пример выходных данных программы

Прежде чем приступить к работе, рассмотрим следующий пример выходных данных программы.

- [Выходные данные консоли](#program-output)
- [Проверка видео](#video-review-default-view)
- [Представление расшифровки](#video-review-transcript-view)

## <a name="prerequisites"></a>Предварительные требования

1. Зарегистрируйтесь на веб-сайте [средства проверки Content Moderator](https://contentmoderator.cognitive.microsoft.com/) и [создайте пользовательские теги](Review-Tool-User-Guide/tags.md), которые консольное приложение C# назначает из кода. На следующем снимке экрана показаны пользовательские теги.

  ![Пользовательские теги модерации видео](images/video-tutorial-custom-tags.png)

1. Чтобы запустить пример приложения, требуется учетная запись Azure и учетная запись Служб мультимедиа Azure. Кроме того, необходим доступ к закрытой предварительной версии Content Moderator. Необходимы также учетные данные проверки подлинности Azure Active Directory. Сведения о получении этой информации см. в [кратком руководстве по API модерации видео](video-moderation-api.md).

1. Измените файл `App.config` и добавьте имя клиента, конечные точки службы и ключи подписки Active Directory, указанные `#####`. Вам понадобятся следующие сведения:

|Ключ|ОПИСАНИЕ|
|-|-|
|`AzureMediaServiceRestApiEndpoint`|Конечная точка для API Служб мультимедиа Azure (AMS)|
|`ClientSecret`|Ключи подписки для Службы мультимедиа Azure|
|`ClientId`|Идентификатор клиента для Служб мультимедиа Azure|
|`AzureAdTenantName`|Имя клиента Active Directory, представляющего вашу организацию|
|`ContentModeratorReviewApiSubscriptionKey`|Ключ подписки для API проверки Content Moderator|
|`ContentModeratorApiEndpoint`|Конечная точка для API Content Moderator|
|`ContentModeratorTeamId`|Идентификатор команды Content Moderator|

## <a name="getting-started"></a>Приступая к работе

Класс `Program` в `Program.cs` — главная точка входа в приложение модерации видео.

### <a name="methods-of-class-program"></a>Методы класса Program

|Метод|ОПИСАНИЕ|
|-|-|
|`Main`|Анализирует командную строку, собирает входные данные пользователя и начинает обработку|
|`ProcessVideo`|Сжимает, отправляет, выполняет модерацию и создает проверки видео|
|`CreateVideoStreamingRequest`|Создает поток для отправки видео|
|`GetUserInputs`|Собирает входные данные пользователя; используется при отсутствии параметров командной строки|
|`Initialize`|Инициализирует объекты, необходимые для процесса модерации|

### <a name="the-main-method"></a>Метод Main

`Main()` используется в начале выполнения, поэтому с него нужно начинать изучать процесс модерации видео.

    static void Main(string[] args)
    {
        if (args.Length == 0)
        {
            string videoPath = string.Empty;
            GetUserInputs(out videoPath);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(Path.GetDirectoryName(videoPath), "log.txt");
            try
            {
                ProcessVideo(videoPath).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
        else
        {
            DirectoryInfo directoryInfo = new DirectoryInfo(args[0]);
            if (args.Length == 2)
                bool.TryParse(args[1], out generateVtt);
            Initialize();
            AmsConfigurations.logFilePath = Path.Combine(args[0], "log.txt");
            var files = directoryInfo.GetFiles("*.mp4", SearchOption.AllDirectories);
            foreach (var file in files)
            {
                try
                {
                    ProcessVideo(file.FullName).Wait();
                }
                catch (Exception ex)
                {
                    Console.WriteLine(ex.Message);
                }
            }
        }
    }

`Main()` обрабатывает следующие аргументы командной строки:

- Путь к каталогу, содержащему файлы видео MPEG-4 для отправки на модерацию. Все файлы `*.mp4` в этом каталоге и его подкаталогах отправляются на модерацию.
- При необходимости логический флаг (true/false), указывающий, следует ли создавать расшифровки текста для модерации аудио.

Если аргументов командной строки нет, `Main()` вызывает `GetUserInputs()`. Этот метод предлагает пользователю ввести путь к одному видеофайлу и указать, нужно ли создать расшифровку текста.

> [!NOTE]
> В консольном приложении используется [API индексатора мультимедийных данных Azure](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) для создания расшифровок из звуковой дорожки отправленного видео. Результаты предоставляются в формате WebVTT. Дополнительные сведения об этом формате см. в статье [Web Video Text Tracks Format (WebVTT)](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Формат Web Video Text Tracks (WebVTT)).

### <a name="initialize-and-processvideo-methods"></a>Инициализация и методы ProcessVideo

Независимо от того, поступили ли параметры программы из командной строки или от интерактивного пользователя, `Main()` вызывает `Initialize()` для создания следующих экземпляров:

|Класс|ОПИСАНИЕ|
|-|-|
|`AMSComponent`|Сжимает файлы видео перед их отправкой на модерацию|
|`AMSconfigurations`|Связывается с помощью интерфейса с данными конфигурации приложения в `App.config`|
|`VideoModerator`| Отправка, кодирование, шифрование и модерация с помощью пакета SDK AMS|
|`VideoReviewApi`|Управляет проверками видео в службе Content Moderator|

Эти классы (помимо `AMSConfigurations`, который является простым) рассматриваются более подробно в следующих разделах данного руководства.

Наконец, видеофайлы обрабатываются поочередно путем вызова `ProcessVideo()` для каждого из них.

    private static async Task ProcessVideo(string videoPath)
    {
        Stopwatch sw = new Stopwatch();
        sw.Start();
        Console.ForegroundColor = ConsoleColor.White;
        Console.WriteLine("\nVideo compression process started...");

        var compressedVideoPath = amsComponent.CompressVideo(videoPath);
        if (string.IsNullOrWhiteSpace(compressedVideoPath))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Compression failed.");
        }

        Console.WriteLine("\nVideo compression process completed...");

        UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
        UploadAssetResult uploadResult = new UploadAssetResult();

        if (generateVtt)
        {
            uploadResult.GenerateVTT = generateVtt;
        }
        Console.WriteLine("\nVideo moderation process started...");

        if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
        {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.WriteLine("Video Review process failed.");
        }

        Console.WriteLine("\nVideo moderation process completed...");
        Console.WriteLine("\nVideo review process started...");
        string reviewId = await videoReviewApi.CreateVideoReviewInContentModerator(uploadResult);
        Console.WriteLine("\nVideo review successfully completed...");
        sw.Stop();
        Console.WriteLine("\nTotal Elapsed Time: {0}", sw.Elapsed);
        using (var stw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            stw.WriteLine("Video File Name: " + Path.GetFileName(videoPath));
            stw.WriteLine($"ReviewId: {reviewId}");
            stw.WriteLine("Total Elapsed Time: {0}", sw.Elapsed);
        }
    }


Метод `ProcessVideo()` очень простой. Он выполняет следующие операции в таком порядке:

- сжимает видео;
- отправляет видео в ресурс Служб мультимедиа Azure;
- создает задание AMS для модерации видео;
- создает проверку видео в Content Moderator.

В следующих разделах более подробно рассматриваются некоторые отдельные процессы, вызываемые с помощью `ProcessVideo()`. 

## <a name="compressing-the-video"></a>Сжатие видео

Чтобы свести к минимуму трафик, приложение преобразовывает видеофайлы в формат H.264 (AVC MPEG-4) и масштабирует их до максимальной ширины в 640 пикселей. Кодек H.264 рекомендуется в связи с его высокой производительностью (степенью сжатия). Сжатие выполняется с помощью бесплатной программы командной строки `ffmpeg`, находящейся в папке `Lib` решения Visual Studio. Входные файлы могут быть любого формата, поддерживаемого `ffmpeg`, включая наиболее часто используемые форматы видеофайлов и кодеки.

> [!NOTE]
> При запуске программы с помощью параметров командной строки вы указываете каталог, содержащий видеофайлы, которые необходимо отправить на модерацию. Обрабатываются все файлы в этом каталоге с расширением имени файла `.mp4`. Чтобы обработать другие расширения имени файла, добавьте в метод `Main()` в `Program.cs` необходимые расширения.

Код, который сжимает один видеофайл, — это класс `AmsComponent` в `AMSComponent.cs`. Метод, который выполняет эту функцию, — `CompressVideo()`, показан ниже.

    public string CompressVideo(string videoPath)
    {
        string ffmpegBlobUrl;
        if (!ValidatePreRequisites())
        {
            Console.WriteLine("Configurations check failed. Please cross check the configurations!");
            throw new Exception();
        }

        if (File.Exists(_configObj.FfmpegExecutablePath))
        {
            ffmpegBlobUrl = this._configObj.FfmpegExecutablePath;
        }
        else
        {
            Console.WriteLine("ffmpeg.exe is missing. Please check the Lib folder");
            throw new Exception();
        }

        string videoFilePathCom = videoPath.Split('.')[0] + "_c.mp4";
        ProcessStartInfo processStartInfo = new ProcessStartInfo();
        processStartInfo.WindowStyle = ProcessWindowStyle.Hidden;
        processStartInfo.FileName = ffmpegBlobUrl;
        processStartInfo.Arguments = "-i \"" + videoPath + "\" -vcodec libx264 -n -crf 32 -preset veryfast -vf scale=640:-1 -c:a aac -aq 1 -ac 2 -threads 0 \"" + videoFilePathCom + "\"";
        var process = Process.Start(processStartInfo);
        process.WaitForExit();
        process.Close();
        return videoFilePathCom;
    }

Код выполняет следующие действия.

- Проверяет наличие в конфигурации `App.config` всех необходимых данных.
- Проверяет наличие двоичного объекта `ffmpeg`.
- Создает выходной файл путем добавления `_c.mp4` к базовому имени файла (например, `Example.mp4` -> `E>xample_c.mp4`).
- Создает строковое значение командной строки для выполнения преобразования.
- Запускает процесс `ffmpeg` с помощью командной строки.
- Ожидает обработку видео.

> [!NOTE]
> Если вы уверены, что ваши видео уже сжаты с использованием H.264 и у вас есть соответствующие измерения, вы можете переписать `CompressVideo()`, чтобы пропустить сжатие.

Метод возвращает имя сжатого выходного файла.

## <a name="uploading-and-moderating-the-video"></a>Отправка и модерация видео

Видео должно храниться в Службах мультимедиа Azure, прежде чем их можно будет обрабатывать с помощью службы модерации контента. В классе `Program` файла `Program.cs` есть краткий метод `CreateVideoStreamingRequest()`, который возвращает объект, представляющий собой запрос потоковой передачи, используемый для отправки видео.

    private static UploadVideoStreamRequest CreateVideoStreamingRequest(string compressedVideoFilePath)
    {
        return
            new UploadVideoStreamRequest
            {
                VideoStream = File.ReadAllBytes(compressedVideoFilePath),
                VideoName = Path.GetFileName(compressedVideoFilePath),
                EncodingRequest = new EncodingRequest()
                {
                    EncodingBitrate = AmsEncoding.AdaptiveStreaming
                },
                VideoFilePath = compressedVideoFilePath
            };
    }

Результирующий объект `UploadVideoStreamRequest` определен в `UploadVideoStreamRequest.cs` (и его родительский элемент `UploadVideoRequest` в `UploadVideoRequest.cs`). Эти классы не показаны здесь. Они краткие и предназначены только для хранения данных сжатого видео и сведений о нем. Еще один класс, предназначенный только для данных, `UploadAssetResult` (`UploadAssetResult.cs`), используется для хранения результатов процесса отправки. Теперь эти строки можно изучить в `ProcessVideo()`:

    UploadVideoStreamRequest uploadVideoStreamRequest = CreateVideoStreamingRequest(compressedVideoPath);
    UploadAssetResult uploadResult = new UploadAssetResult();

    if (generateVtt)
    {
        uploadResult.GenerateVTT = generateVtt;
    }
    Console.WriteLine("\nVideo moderation process started...");

    if (!videoModerator.CreateAzureMediaServicesJobToModerateVideo(uploadVideoStreamRequest, uploadResult))
    {
        Console.ForegroundColor = ConsoleColor.Red;
        Console.WriteLine("Video Review process failed.");
    }

Эти строки выполняют следующие задачи:

- Создают `UploadVideoStreamRequest` для передачи сжатого видео.
- Задают флаг `GenerateVTT` запроса, если пользователь запросил расшифровку текста.
- Вызывают `CreateAzureMediaServicesJobToModerateVideo()` для отправки и получения результата.

## <a name="deep-dive-into-video-moderation"></a>Подробный обзор модерации видео

Метод `CreateAzureMediaServicesJobToModerateVideo()` находится в `VideoModerator.cs`, содержащем основную часть кода, который взаимодействует со Службами мультимедиа Azure. В следующем фрагменте показан исходный код этого метода.

    public bool CreateAzureMediaServicesJobToModerateVideo(UploadVideoStreamRequest uploadVideoRequest, UploadAssetResult uploadResult)
    {
        asset = CreateAsset(uploadVideoRequest);
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        // Encoding the asset , Moderating the asset, Generating transcript in parallel
        IAsset encodedAsset = null;
        //Creates the job for the tasks.
        IJob job = this._mediaContext.Jobs.Create("AMS Review Job");

        //Adding encoding task to job.
        ConfigureEncodeAssetTask(uploadVideoRequest.EncodingRequest, job);

        ConfigureContentModerationTask(job);

        //adding transcript task to job.
        if (uploadResult.GenerateVTT)
        {
            ConfigureTranscriptTask(job);
        }

        Stopwatch timer = new Stopwatch();
        timer.Start();
        //submit and execute job.
        job.Submit();
        job.GetExecutionProgressTask(new CancellationTokenSource().Token).Wait();
        timer.Stop();
        using (var sw = new StreamWriter(AmsConfigurations.logFilePath, true))
        {
            sw.WriteLine("AMS Job Elapsed Time: {0}", timer.Elapsed);
        }

        if (job.State == JobState.Error)
        {
            throw new Exception("Video moderation has failed due to AMS Job error.");
        }

        UploadAssetResult result = uploadResult;
        encodedAsset = job.OutputMediaAssets[0];
        result.ModeratedJson = GetCmDetail(job.OutputMediaAssets[1]);
        // Check for valid Moderated JSON
        var jsonModerateObject = JsonConvert.DeserializeObject<VideoModerationResult>(result.ModeratedJson);

        if (jsonModerateObject == null)
        {
            return false;
        }
        if (uploadResult.GenerateVTT)
        {
            GenerateTranscript(job.OutputMediaAssets.Last());
        }

        uploadResult.StreamingUrlDetails = PublishAsset(encodedAsset);
        string downloadUrl = GenerateDownloadUrl(asset, uploadVideoRequest.VideoName);
        uploadResult.StreamingUrlDetails.DownloadUri = downloadUrl;
        uploadResult.VideoName = uploadVideoRequest.VideoName;
        uploadResult.VideoFilePath = uploadVideoRequest.VideoFilePath;
        return true;
    }

Этот код выполняет следующие задачи:

- Создает задание AMS для обработки, которую необходимо выполнить.
- Добавляет задачи для кодирования видеофайла, его модерации и создания расшифровки текста.
- Отправляет задание, передает файл и начинает обработку.
- Извлекает результаты модерации, расшифровку текста (если запрошено) и другие сведения.

## <a name="sample-video-moderation-response"></a>Пример ответа модерации видео

Результат задания модерации видео (см. [краткое руководство по модерации видео](video-moderation-api.md)) — это структура данных JSON, содержащая результаты модерации. Эти результаты содержат декомпозицию фрагментов (снимков) в видео, каждый из которых содержит события (клипы) с ключевыми кадрами, помеченными для проверки. Каждый кадр оценивается по вероятности нахождения в нем содержимого для взрослых или материалов непристойного характера. Ниже приведен пример ответа JSON.

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

Если флаг `GenerateVTT` установлен, также выполняется транскрибирование аудиофайла.

> [!NOTE]
> В консольном приложении используется [API индексатора мультимедийных данных Azure](https://docs.microsoft.com/azure/media-services/media-services-process-content-with-indexer2) для создания расшифровок из звуковой дорожки отправленного видео. Результаты предоставляются в формате WebVTT. Дополнительные сведения об этом формате см. в статье [Web Video Text Tracks Format (WebVTT)](https://developer.mozilla.org/en-US/docs/Web/API/WebVTT_API) (Формат Web Video Text Tracks (WebVTT)).


## <a name="creating-the-human-in-the-loop-review"></a>Создание проверки человеком в процессе

Процесс модерации возвращает список ключевых кадров из видео вместе с расшифровкой его звуковых дорожек. Следующим шагом является создание проверки в средстве проверки Content Moderator для модераторов. Возвращаясь к методу `ProcessVideo()` в `Program.cs`, вы видите вызов к методу `CreateVideoReviewInContentModerator()`. Этот метод находится в классе `videoReviewApi`, который находится в `VideoReviewAPI.cs` и приведен ниже.

    public async Task<string> CreateVideoReviewInContentModerator(UploadAssetResult uploadAssetResult)
    {
    
        string reviewId = string.Empty;
        List<ProcessedFrameDetails> frameEntityList = framegenerator.CreateVideoFrames(uploadAssetResult);
        string path = uploadAssetResult.GenerateVTT == true ? this._amsConfig.FfmpegFramesOutputPath + Path.GetFileNameWithoutExtension (uploadAssetResult.VideoName) + "_aud_SpReco.vtt" : "";
        TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult();
        
    if (File.Exists(path))
        {
            screenTextResult = await GenerateTextScreenProfanity(reviewId, path, frameEntityList);
            uploadAssetResult.Category1TextScore = screenTextResult.Category1Score;
            uploadAssetResult.Category2TextScore = screenTextResult.Category2Score;
            uploadAssetResult.Category3TextScore = screenTextResult.Category3Score;
            uploadAssetResult.Category1TextTag = screenTextResult.Category1Tag;
            uploadAssetResult.Category2TextTag = screenTextResult.Category2Tag;
            uploadAssetResult.Category3TextTag = screenTextResult.Category3Tag;
        }
        
        var reviewVideoRequestJson = CreateReviewRequestObject(uploadAssetResult, frameEntityList);
        if (string.IsNullOrWhiteSpace(reviewVideoRequestJson))
        {
            throw new Exception("Video review process failed in CreateVideoReviewInContentModerator");
        }
        
        reviewId = JsonConvert.DeserializeObject<List<string>>(ExecuteCreateReviewApi(reviewVideoRequestJson).Result).FirstOrDefault();
        frameEntityList = framegenerator.GenerateFrameImages(frameEntityList, uploadAssetResult, reviewId);
        await CreateAndPublishReviewInContentModerator(uploadAssetResult, frameEntityList, reviewId, path, screenTextResult);
        return reviewId;
    
    }

`CreateVideoReviewInContentModerator()` вызывает несколько методов для выполнения следующих задач:

> [!NOTE]
> В консольном приложении для создания эскизов используется библиотека [FFmpeg](https://ffmpeg.org/). Эти эскизы (изображения) соответствуют меткам времени кадра в [выходных данных модерации видео](#sample-video-moderation-response).

|Задача|Методы|Файл|
|-|-|-|
|Извлечение ключевых кадров из видео и создание изображений эскизов|`CreateVideoFrames()`<br>`GenerateFrameImages()`|`FrameGeneratorServices.cs`|
|Проверка расшифровки текста, если доступно, на наличие аудио содержимого для взрослых или материалов непристойного характера|`GenerateTextScreenProfanity()`| `VideoReviewAPI.cs`|
|Подготовка и отправка запроса на проверку видео человеком|`CreateReviewRequestObject()`<br> `ExecuteCreateReviewApi()`<br>`CreateAndPublishReviewInContentModerator()`|`VideoReviewAPI.cs`|

## <a name="video-review-default-view"></a>Представление по умолчанию проверки видео

На следующем снимке экрана показаны результаты выполнения предыдущих шагов.

![Представление по умолчанию проверки видео](images/video-tutorial-default-view.PNG)

## <a name="transcript-generation"></a>Создание расшифровки

До этого момента код, представленный в этом руководстве, ориентирован на визуальное содержимое. Проверка речевого содержимого — это отдельный и необязательный процесс, для которого, как упоминалось выше, используется расшифровка, созданная из аудио. Теперь рассмотрим создание расшифровки и использование ее в процессе проверки. Задача создания расшифровки отводится службе [индексатора мультимедийных данных Azure](https://docs.microsoft.com/azure/media-services/media-services-index-content).

Приложение выполняет такие задачи:

|Задача|Методы|Файл|
|-|-|-|
|Определить, нужно ли создать расшифровки текста|`Main()`<br>`GetUserInputs()`|`Program.cs`|
|Если да, отправить задание транскрибирования в рамках модерации|`ConfigureTranscriptTask()`|`VideoModerator.cs`|
|Получить локальную копию расшифровки|`GenerateTranscript()`|`VideoModerator.cs`|
|Пометить кадры видео, которые содержат аудио с неприемлемым содержимым|`GenerateTextScreenProfanity()`<br>`TextScreen()`|`VideoReviewAPI.cs`|
|Добавить результаты в проверку|`UploadScreenTextResult()`<br>`ExecuteAddTranscriptSupportFile()`|`VideoReviewAPI.cs`|

### <a name="task-configuration"></a>Конфигурация задачи

Перейдем к отправке задания транскрибирования. `CreateAzureMediaServicesJobToModerateVideo()` (уже описано) вызывает `ConfigureTranscriptTask()`.

    private void ConfigureTranscriptTask(IJob job)
    {
        string mediaProcessorName = _amsConfigurations.MediaIndexer2MediaProcessor;
        IMediaProcessor processor = _mediaContext.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

        string configuration = File.ReadAllText(_amsConfigurations.MediaIndexerConfigurationJson);
        ITask task = job.Tasks.AddNew("AudioIndexing Task", processor, configuration, TaskOptions.None);
        task.InputAssets.Add(asset);
        task.OutputAssets.AddNew("AudioIndexing Output Asset", AssetCreationOptions.None);
    }

Конфигурация для задачи расшифровки читается из файла `MediaIndexerConfig.json` в папке `Lib` решения. Ресурсы AMS создаются для файла конфигурации, а также для выходных данных процесса транскрибирования. При запуске задания AMS эта задача создает расшифровку текста из звуковой дорожки видеофайла.

> [!NOTE]
> Пример приложения распознает только английскую речь (США).

### <a name="transcript-generation"></a>Создание расшифровки

Расшифровка публикуется в качестве ресурса AMS. Для проверки расшифровки нежелательного содержимого приложение загружает ресурс из Служб мультимедиа Azure. `CreateAzureMediaServicesJobToModerateVideo()` вызывает `GenerateTranscript()`, показанный здесь, чтобы получить файл.

    public bool GenerateTranscript(IAsset asset)
    {
        try
        {
            var outputFolder = this._amsConfigurations.FfmpegFramesOutputPath;
            IAsset outputAsset = asset;
            IAccessPolicy policy = null;
            ILocator locator = null;
            policy = _mediaContext.AccessPolicies.Create("My 30 days readonly policy", TimeSpan.FromDays(360), AccessPermissions.Read);
            locator = _mediaContext.Locators.CreateLocator(LocatorType.Sas, outputAsset, policy, DateTime.UtcNow.AddMinutes(-5));
            DownloadAssetToLocal(outputAsset, outputFolder);
            locator.Delete();
            return true;
        }
        catch
        {   //TODO:  Logging
            Console.WriteLine("Exception occured while generating index for video.");
            throw;
        }
    }

После необходимой установки AMS фактическая загрузка выполняется путем вызова `DownloadAssetToLocal()`, универсальной функции, которая копирует ресурс AMS в локальный файл.

## <a name="transcript-moderation"></a>Модерация расшифровки

Эта расшифровка сканируется и используется в ходе проверки. Создание проверки — это область действия `CreateVideoReviewInContentModerator()`, который вызывает `GenerateTextScreenProfanity()` для выполнения этой работы. В свою очередь, этот метод вызывает `TextScreen()`, который содержит основные функциональные возможности. 

`TextScreen()` выполняет следующие задачи:

- Анализ расшифровки на наличие меток времени и субтитров.
- Отправка субтитров на модерацию текста.
- Пометка любого кадра, который может содержать данные с нежелательным речевым содержимым.

Давайте рассмотрим все эти задачи более подробно.

### <a name="initialize-the-code"></a>Инициализация кода

Сначала инициализируйте все переменные и коллекции.

    private async Task<TranscriptScreenTextResult> TextScreen(string filepath, List<ProcessedFrameDetails> frameEntityList)
    {
        List<TranscriptProfanity> profanityList = new List<TranscriptProfanity>();
        string responseContent = string.Empty;
        HttpResponseMessage response;
        bool category1Tag = false;
        bool category2Tag = false;
        bool category3Tag = false;
        double category1Score = 0;
        double category2Score = 0;
        double category3Score = 0;
        List<string> vttLines = File.ReadAllLines(filepath).Where(line => !line.Contains("NOTE Confidence:") && line.Length > 0).ToList();
        StringBuilder sb = new StringBuilder();
        List<CaptionScreentextResult> csrList = new List<CaptionScreentextResult>();
        CaptionScreentextResult captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };

        // Code from the next sections in the tutorial
    

### <a name="parse-the-transcript-for-captions"></a>Анализ расшифровки на наличие субтитров

Далее проанализируйте расшифровку в формате VTT на наличие субтитров и меток времени. Средство просмотра отображает эти субтитры на вкладке "Запись разговоров" на экране просмотра видео. Метки времени используются для синхронизации субтитров с соответствующими видеокадрами.

        // Code from the previous section(s) in the tutorial

        //
        // Parse the transcript
        //
        foreach (var line in vttLines.Skip(1))
        {
                if (line.Contains("-->"))
                {
                    if (sb.Length > 0)
                    {
                        captionScreentextResult.Captions.Add(sb.ToString());
                        sb.Clear();
                    }
                    if (captionScreentextResult.Captions.Count > 0)
                    {
                        csrList.Add(captionScreentextResult);
                        captionScreentextResult = new CaptionScreentextResult() { Captions = new List<string>() };
                    }
                    string[] times = line.Split(new string[] { "-->" }, StringSplitOptions.RemoveEmptyEntries);
                    string startTimeString = times[0].Trim();
                    string endTimeString = times[1].Trim();
                    int startTime = (int)TimeSpan.ParseExact(startTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    int endTime = (int)TimeSpan.ParseExact(endTimeString, @"hh\:mm\:ss\.fff", CultureInfo.InvariantCulture).TotalMilliseconds;
                    captionScreentextResult.StartTime = startTime;
                    captionScreentextResult.EndTime = endTime;
                }
                else
                {
                    sb.Append(line);
                }
                if (sb.Length + line.Length > 1024)
                {
                    captionScreentextResult.Captions.Add(sb.ToString());
                    sb.Clear();
                }
            }
            if (sb.Length > 0)
            {
                captionScreentextResult.Captions.Add(sb.ToString());
            }
            if (captionScreentextResult.Captions.Count > 0)
            {
                csrList.Add(captionScreentextResult);
            }

            // Code from the following section in the quickstart

### <a name="moderate-captions-with-the-text-moderation-service"></a>Модерация субтитров с помощью службы модерации текста

Далее мы сканируем субтитры проанализированного текста с помощью API для работы с текстами Content Moderator.

> [!NOTE]
> Ключ службы Content Moderator предусматривает ограничение частоты на количество запросов в секунду (RPS). Если превысить ограничение, пакет SDK порождает исключение с кодом ошибки 429. 
>
> Ключ бесплатного уровня предусматривает ограничение в один RPS.

    //
    // Moderate the captions or cues
    //
    int waitTime = 1000;
    foreach (var csr in csrList)
    {
                bool captionAdultTextTag = false;
                bool captionRacyTextTag = false;
                bool captionOffensiveTextTag = false;
                bool retry = true;

                foreach (var caption in csr.Captions)
                {
                    while (retry)
                    {
                        try
                        {
                            System.Threading.Thread.Sleep(waitTime);
                            var lang = await CMClient.TextModeration.DetectLanguageAsync("text/plain", caption);
                            var oRes = await CMClient.TextModeration.ScreenTextWithHttpMessagesAsync(lang.DetectedLanguageProperty, "text/plain", caption, null, null, null, true);
                            response = oRes.Response;
                            responseContent = await response.Content.ReadAsStringAsync();
                            retry = false;
                        }
                        catch (Exception e)
                        {
                            if (e.Message.Contains("429"))
                            {
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                                waitTime = (int)(waitTime * 1.5);
                                Console.WriteLine($"wait time: {waitTime}");
                            }
                            else
                            {
                                retry = false;
                                Console.WriteLine($"Moderation API call failed. Message: {e.Message}");
                            }
                        }
                    }
                    var jsonTextScreen = JsonConvert.DeserializeObject<TextScreen>(responseContent);
                    if (jsonTextScreen != null)
                    {
                        TranscriptProfanity transcriptProfanity = new TranscriptProfanity();
                        transcriptProfanity.TimeStamp = "";
                        List<Terms> transcriptTerm = new List<Terms>();
                        if (jsonTextScreen.Terms != null)
                        {
                            foreach (var term in jsonTextScreen.Terms)
                            {
                                var profanityobject = new Terms
                                {
                                    Term = term.Term,
                                    Index = term.Index
                                };
                                transcriptTerm.Add(profanityobject);
                            }
                            transcriptProfanity.Terms = transcriptTerm;
                            profanityList.Add(transcriptProfanity);
                        }
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) captionAdultTextTag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) captionRacyTextTag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) captionOffensiveTextTag = true;
                        if (jsonTextScreen.Classification.Category1.Score > _amsConfig.Category1TextThreshold) category1Tag = true;
                        if (jsonTextScreen.Classification.Category2.Score > _amsConfig.Category2TextThreshold) category2Tag = true;
                        if (jsonTextScreen.Classification.Category3.Score > _amsConfig.Category3TextThreshold) category3Tag = true;
                        category1Score = jsonTextScreen.Classification.Category1.Score > category1Score ? jsonTextScreen.Classification.Category1.Score : category1Score;
                        category2Score = jsonTextScreen.Classification.Category2.Score > category2Score ? jsonTextScreen.Classification.Category2.Score : category2Score;
                        category3Score = jsonTextScreen.Classification.Category3.Score > category3Score ? jsonTextScreen.Classification.Category3.Score : category3Score;
                    }
                    foreach (var frame in frameEntityList.Where(x => x.TimeStamp >= csr.StartTime && x.TimeStamp <= csr.EndTime))
                    {
                        frame.IsAdultTextContent = captionAdultTextTag;
                        frame.IsRacyTextContent = captionRacyTextTag;
                        frame.IsOffensiveTextContent = captionOffensiveTextTag;
                    }
                }
            }
            TranscriptScreenTextResult screenTextResult = new TranscriptScreenTextResult()
            {
                TranscriptProfanity = profanityList,
                Category1Tag = category1Tag,
                Category2Tag = category2Tag,
                Category3Tag = category3Tag,
                Category1Score = category1Score,
                Category2Score = category2Score,
                Category3Score = category3Score
            };
            return screenTextResult;
    }

### <a name="breaking-down-the-text-moderation-step"></a>Разбиение шага модерации текста

`TextScreen()` — это фундаментальный метод, поэтому его необходимо разбить.

1. Сначала этот метод считывает файл расшифровки по одной строке. Он пропускает пустые строки и строки, содержащие `NOTE` с оценкой надежности. Он извлекает метки времени и текстовые элементы из *подсказок* в файле. Подсказки представляют собой текст из звуковой дорожки и содержат сведения о времени начала и окончания. Подсказка начинается со строки метки времени со строкой `-->`. За ней следует одна или несколько строк текста.

1. Экземпляры `CaptionScreentextResult` (определенные в `TranscriptProfanity.cs`) используются для хранения сведений, извлеченных из каждой подсказки.  При обнаружении строки метки времени или достижении максимальной длины текста в 1024 символов в `csrList` добавляется новый `CaptionScreentextResult`. 

1. Далее метод отправляет каждую подсказку в API модерации текста. Он вызывает `ContentModeratorClient.TextModeration.DetectLanguageAsync()` и `ContentModeratorClient.TextModeration.ScreenTextWithHttpMessagesAsync()`, определенные в сборке `Microsoft.Azure.CognitiveServices.ContentModerator`. Чтобы избежать ограничений по скорости, метод приостанавливается на секунду перед отправкой каждой подсказки.

1. После получения результатов из службы модерации текста метод анализирует их, чтобы проверить, не превышают ли они пороговые значения надежности. Эти значения устанавливаются в `App.config` как `OffensiveTextThreshold`, `RacyTextThreshold` и `AdultTextThreshold`. Наконец, нежелательные термины также сохраняются. Все кадры в диапазоне времени подсказки отмечаются как содержащие текст с оскорбительным содержимым, материалами непристойного характера или для взрослых.

1. `TextScreen()` возвращает экземпляр `TranscriptScreenTextResult`, содержащий результат модерации текста из видео в целом. Этот объект содержит флаги и оценки для различных типов нежелательного содержимого вместе со списком всех нежелательных терминов. Вызывающий объект, `CreateVideoReviewInContentModerator()`, вызывает `UploadScreenTextResult()`, чтобы вложить эти сведения в проверку и сделать ее доступной для рецензентов.
 
## <a name="video-review-transcript-view"></a>Представление расшифровки проверки видео

На следующем снимке экрана показан результат создания расшифровки и шаги модерации.

![Представление расшифровки модерации видео](images/video-tutorial-transcript-view.PNG)

## <a name="program-output"></a>Выходные данные программы

В следующих выходных данных командной строки программы показаны различные задачи по мере их выполнения. Кроме того, результат модерации (в формате JSON) и расшифровка речи доступны в том же каталоге, что и исходные видеофайлы.

    Microsoft.ContentModerator.AMSComponentClient
    Enter the fully qualified local path for Uploading the video :
    "Your File Name.MP4"
    Generate Video Transcript? [y/n] : y
    
    Video compression process started...
    Video compression process completed...
    
    Video moderation process started...
    Video moderation process completed...
    
    Video review process started...
    Video Frames Creation inprogress...
    Frames(83) created successfully.
    Review Created Successfully and the review Id 201801va8ec2108d6e043229ba7a9e6373edec5
    Video review successfully completed...
    
    Total Elapsed Time: 00:05:56.8420355


## <a name="next-steps"></a>Дополнительная информация

[Загрузите решение Visual Studio](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp), примеры файлов и необходимые библиотеки для этого руководства и приступите к интеграции.
