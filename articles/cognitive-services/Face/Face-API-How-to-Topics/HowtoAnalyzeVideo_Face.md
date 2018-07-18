---
title: Анализ видео в режиме реального времени с помощью API распознавания лиц | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Выполнение анализа почти в режиме реального времени по кадрам, взятым из видеопотока реального времени, с помощью API распознавания лиц в службах Cognitive Services.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 8675f992ddffe2eedfeac294a6c57560434802c2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380741"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Анализ видео в режиме реального времени
В этом руководстве будет показано, как выполнить анализ почти в режиме реального времени по кадрам, взятым из видеопотока реального времени. Основные компоненты такой системы:
- получение кадров из источника видео;
- выбор кадров для анализа;
- отправка этих кадров в API;
- использование каждого результата анализа, возвращенного из вызова API.

Эти примеры написаны на C#, а код можно найти на GitHub здесь: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Подход
Существует несколько способов решить проблему выполнения анализа видеопотоков почти в режиме реального времени. Начнем с изложения трех подходов в порядке повышения уровня сложности.

### <a name="a-simple-approach"></a>Простой подход
Простейшая конструкция системы анализа в режиме почти реального времени представляет собой бесконечный цикл, где в каждой итерации мы захватываем кадр, анализируем его и затем используем результат:
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```
Если при анализе применялся упрощенный алгоритм на стороне клиента, такого подхода будет достаточно. Однако когда анализ производится в облаке, возникающая задержка означает, что вызов API может занимать несколько секунд, в течение которых мы не захватываем изображения, и наш поток фактически не выполняет никаких действий. Максимальная частота кадров ограничивается задержкой вызовов API.

### <a name="parallelizing-api-calls"></a>Организация параллельных вызовов API
В то время как простой однопоточный цикл имеет смысл для упрощенного алгоритма на стороне клиента, он плохо сочетается с задержкой при вызовах API в облаке. Решение этой проблемы состоит в том, чтобы разрешить исполнять длительные вызовы API параллельно с перехватом кадров. В C# это достигается с помощью параллелизма на основе задач, например:
```CSharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () => 
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```
При этом каждый анализ запускается в отдельной задаче, которая может выполняться в фоновом режиме, пока продолжается перехват новых кадров. Это позволяет избежать блокировки основного потока во время ожидания возврата вызова API. Однако мы при этом теряем некоторые гарантии простой версии — несколько вызовов API могут выполняться параллельно, и результаты могут возвращаться в неправильном порядке. Кроме того, при этом несколько потоков могут одновременно войти в функцию ConsumeResult(), что может представлять опасность, если эта функция не потокобезопасна. Наконец, такой простой код не позволяет отслеживать создаваемые задачи, поэтому исключения исчезают без уведомления. Таким образом, последняя составляющая — добавить поток-"получатель", который будет отслеживать задачи анализа, вызывать исключения, завершать долго выполняющиеся задачи и обеспечивать использование результатов в правильном порядке, по одному за раз.

### <a name="a-producer-consumer-design"></a>Конструкция "производитель-получатель"
В нашей конечной системе "производитель-получатель" имеется поток-"производитель", который очень похож на описанный ранее бесконечный цикл. Однако вместо того чтобы использовать результаты анализа сразу же по мере их доступности, производитель просто помещает их в очередь для отслеживания.
```CSharp
// Queue that will contain the API call tasks. 
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();
     
// Producer thread. 
while (true)
{
    // Grab a frame. 
    Frame f = GrabFrame();
 
    // Decide whether to analyze the frame. 
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread. 
        var analysisTask = Task.Run(async () => 
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }
        
        // Push the task onto the queue. 
        taskQueue.Add(analysisTask);
    }
}
```
У нас также есть поток-"получатель", который принимает задачи из очереди, дожидается их завершения и либо отображает результат, либо вызывает созданное исключение. С помощью очереди мы можем гарантировать, что результаты будут использоваться по одному за раз в правильном порядке без ограничения максимальной частоты кадров системы.
```CSharp
// Consumer thread. 
while (true)
{
    // Get the oldest task. 
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Await until the task is completed. 
    var output = await analysisTask;
     
    // Consume the exception or result. 
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implementing-the-solution"></a>Реализация решения
### <a name="getting-started"></a>Приступая к работе
Чтобы как можно быстрее запустить приложение, мы реализовали описанную выше систему, спроектировав ее с достаточной гибкостью для реализации множества сценариев и одновременно для простоты использования. Для доступа к коду перейдите к странице [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Библиотека содержит класс FrameGrabber, который реализует описанную выше систему "производитель-получатель" для обработки видеокадров с веб-камеры. Пользователь может указать точную форму вызова API, а класс с помощью событий позволяет вызывающему коду знать, когда поступает новый кадр или доступен новый результат анализа.

Для иллюстрации некоторых возможностей есть два примера приложений, использующих библиотеку. Первый представляет собой простое консольное приложение, упрощенная версия которого воспроизведена ниже. Оно захватывает кадры с установленной по умолчанию веб-камеры и отправляет их в API распознавания лиц.
```CSharp
using System;
using VideoFrameAnalyzer;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;
     
namespace VideoFrameConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            // Create grabber, with analysis type Face[]. 
            FrameGrabber<Face[]> grabber = new FrameGrabber<Face[]>();
            
            // Create Face API Client. Insert your Face API key here.
            FaceServiceClient faceClient = new FaceServiceClient("<Subscription Key>");

            // Set up our Face API call.
            grabber.AnalysisFunction = async frame => return await faceClient.DetectAsync(frame.Image.ToMemoryStream(".jpg"));

            // Set up a listener for when we receive a new result from an API call. 
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.Analysis != null)
                    Console.WriteLine("New result received for frame acquired at {0}. {1} faces detected", e.Frame.Metadata.Timestamp, e.Analysis.Length);
            };
            
            // Tell grabber to call the Face API every 3 seconds.
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running.
            grabber.StartProcessingCameraAsync().Wait();

            // Wait for keypress to stop
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();
            
            // Stop, blocking until done.
            grabber.StopProcessingAsync().Wait();
        }
    }
}
```
Второй пример приложения интереснее, он позволяет выбрать, какой API вызывать для видеокадров. Слева в приложении отображается видео в реальном времени для предварительного просмотра, справа — последний результат API, наложенный на соответствующий кадр.

В большинстве режимов будет наблюдаться видимая задержка между текущим видео с левой стороны и визуализированным анализом с правой. Эта задержка равна времени, которое занимает выполнение вызова API. Исключением является режим EmotionsWithClientFaceDetect, который выполняет обнаружение лиц локально на клиентском компьютере с помощью OpenCV перед отправкой каких-либо изображений в службы Cognitive Services. Таким образом, мы можем сразу же визуализировать обнаруженное лицо, а впоследствии обновить эмоции после возвращения вызова API. Этот пример демонстрирует возможность "гибридного" подхода, когда некоторая простая обработка может выполняться на клиенте, а затем с помощью API-интерфейсов Cognitive Services ее можно при необходимости дополнить более сложным анализом.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Интеграция в базу кода
Чтобы приступить к работе с этим примером, выполните следующие действия.

1. Получите ключи API-интерфейсов зрения в разделе [Подписки](https://azure.microsoft.com/try/cognitive-services/). Для анализа кадров видео применимы следующие API-интерфейсы:
    - [API компьютерного зрения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API распознавания эмоций](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [API распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Клонируйте репозиторий GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/)

3. Откройте пример в Visual Studio 2015, выполните сборку и запустите примеры приложений:
    - Для примера BasicConsoleSample ключ API распознавания лиц содержится непосредственно в коде [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Для LiveCameraSample ключи следует ввести в области Settings (Параметры) приложения. Они будут сохраняться во всех сеансах как пользовательские данные.
        

Когда все будет готово к интеграции, **просто обратитесь к библиотеке VideoFrameAnalyzer из собственных проектов.** 



## <a name="developer-code-of-conduct"></a>Правила поведения разработчиков
Как и для всех служб Cognitive Services, разработчики, работающие с нашими API-интерфейсами и примерами, обязаны следовать [Правилам поведения разработчиков для служб Microsoft Cognitive Services](https://azure.microsoft.com/support/legal/developer-code-of-conduct/). 


В функциях библиотеки VideoFrameAnalyzer для распознавания изображений, речи, видео или текста используются службы Microsoft Cognitive Services. Корпорация Майкрософт получит изображения, аудио, видео и другие данные, отправленные вами (с помощью этого приложения), и может использовать их в целях улучшения служб. Мы просим вашей помощи в защите тех, чьи данные ваше приложение отправляет в службы Microsoft Cognitive Services. 


## <a name="summary"></a>Сводка
Из этого руководства вы узнали, как запустить анализ потокового видео почти в режиме реального времени с использованием API-интерфейсов распознавания лиц, компьютерного зрения и распознавания эмоций, а также как можно приступить к работе с использованием наших примеров кода.  Вы можете приступить к созданию своего приложения с бесплатными ключами API на [странице регистрации Microsoft Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

Отправляйте отзывы и предложения в [репозиторий GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), а более подробные отзывы об API — на нашем [сайте UserVoice](https://cognitive.uservoice.com/).



## <a name="related"></a> Связанные разделы
- [Как идентифицировать лица на изображениях](HowtoIdentifyFacesinImage.md)
- [How to Detect Faces in Image](HowtoDetectFacesinImage.md) (Практическое руководство по обнаружению лиц на изображении)
