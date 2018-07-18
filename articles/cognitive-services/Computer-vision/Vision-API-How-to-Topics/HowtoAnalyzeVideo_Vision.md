---
title: Анализ видео в реальном времени с помощью API компьютерного зрения | Документация Майкрософт
description: Узнайте, как выполнить анализ практически в реальном времени по кадрам, взятым из видеотрансляции, с помощью API компьютерного зрения в Cognitive Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 01/20/2017
ms.author: kefre
ms.openlocfilehash: d75b1a887e5e4557d5464d8062e1bde628e7adab
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380728"
---
# <a name="how-to-analyze-videos-in-real-time"></a>Анализ видео в реальном времени
В этом руководстве показано, как выполнить анализ практически в реальном времени по кадрам, взятым из видеотрансляции. Базовые операции этой системы:
- получение кадров из источника видео;
- выбор кадров для анализа;
- отправка этих кадров в API;
- обработка каждого результата анализа, возвращенного в результате вызова API.

Эти примеры написаны на C#. Код можно найти на GitHub: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

## <a name="the-approach"></a>Подход
Есть несколько способов выполнить анализ видеопотоков практически в реальном времени. Начнем с описания трех подходов (по уровню сложности).

### <a name="a-simple-approach"></a>Простой подход
Простейшая система анализа практически в реальном времени представляет собой бесконечный цикл, где в каждой итерации мы захватываем кадр, анализируем его и затем используем результат:
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
Если при анализе применялся упрощенный алгоритм на стороне клиента, такого подхода будет достаточно. Но когда анализ выполняется в облаке, возникающая задержка означает, что вызов API может занимать несколько секунд, в течение которых мы не захватываем изображения, и наш поток фактически не выполняет никаких действий. Максимальная частота кадров ограничивается задержкой вызовов API.

### <a name="parallelizing-api-calls"></a>Организация параллельных вызовов API
Хотя простой однопоточный цикл имеет смысл использовать с упрощенным алгоритмом на стороне клиента, он плохо сочетается с задержкой при вызовах API в облаке. Решение этой проблемы состоит в том, чтобы разрешить выполнять длительные вызовы API одновременно с перехватом кадров. В C# это достигается с помощью параллелизма на основе задач, например:
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
При этом каждый анализ запускается в рамках отдельной задачи, которая может выполняться в фоновом режиме, пока продолжается захват новых кадров. Это позволяет избежать блокировки основного потока во время ожидания возврата вызова API. Но мы при этом теряем некоторые гарантированные функции простой версии, следовательно, несколько вызовов API могут выполняться параллельно, и результаты могут возвращаться в неправильном порядке. Также при этом несколько потоков могут одновременно использовать в функцию ConsumeResult(), что может представлять опасность, если эта функция не является потокобезопасной. Наконец, этот простой код не позволяет отслеживать создаваемые задачи, поэтому исключения будут исчезать без уведомления. Таким образом, последняя составляющая — добавить "поток-получатель", который будет отслеживать задачи анализа, вызывать исключения, завершать долго выполняющиеся задачи и обеспечивать использование результатов в правильном порядке, по одному за раз.

### <a name="a-producer-consumer-design"></a>Конструкция "производитель-получатель"
В нашей итоговой системе "производитель-получатель" есть поток-производитель, который очень похож на описанный ранее бесконечный цикл. Но вместо того, чтобы использовать результаты анализа сразу же по мере их доступности, производитель просто помещает их в очередь для отслеживания.
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
У нас также есть поток-получатель, который принимает задачи из очереди, дожидается их завершения и либо отображает результат, либо вызывает созданное исключение. С помощью очереди мы можем гарантировать, что результаты будут использоваться последовательно в правильном порядке и без ограничения максимальной частоты кадров системы.
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
Чтобы как можно быстрее запустить приложение, мы реализовали описанную выше систему, спроектировав ее достаточно гибкой для реализации множества сценариев и одновременно для простоты использования. Получить код можно отсюда: [https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis).

Библиотека содержит класс FrameGrabber, который реализует описанную выше систему "производитель-получатель" для обработки видеокадров с веб-камеры. Пользователь может указать точную форму вызова API, а класс с помощью событий позволяет вызывающему коду определять, когда поступает новый кадр или становится доступным новый результат анализа.

Для иллюстрации некоторых из возможностей есть два примера приложений, использующих библиотеку. Первый — это простое консольное приложение, упрощенная версия которого воспроизведена ниже. Приложение захватывает кадры с установленной по умолчанию веб-камеры и отправляет их в API распознавания лиц для обработки.
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
            FaceServiceClient faceClient = new FaceServiceClient("<subscription key>");

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
Второй пример приложения более интересный. Он позволяет выбрать, какой API вызывать для видеокадров. Слева в приложении отображается видео в реальном времени для предварительного просмотра, справа — последний результат API, наложенный на соответствующий кадр.

В большинстве режимов будет наблюдаться видимая задержка между текущим видео с левой стороны и визуализированным анализом с правой. Эта задержка равна времени, которое занимает выполнение вызова API. Исключением является режим EmotionsWithClientFaceDetect, который выполняет обнаружение лиц локально на клиентском компьютере с помощью OpenCV перед отправкой изображений в Cognitive Services. Таким образом мы можем сразу же визуализировать обнаруженное лицо, а затем обновить эмоции после возвращения вызова API. Этот пример демонстрирует возможность гибридного подхода, когда некоторая простая обработка может выполняться на клиенте, а затем с помощью API-интерфейсов Cognitive Services ее можно при необходимости дополнить более сложным анализом.

![HowToAnalyzeVideo](../../Video/Images/FramebyFrame.jpg)

### <a name="integrating-into-your-codebase"></a>Интеграция в базу кода
Чтобы приступить к работе с этим примером, сделайте следующее:

1. Получите ключи API-интерфейсов зрения в разделе [Подписки](https://azure.microsoft.com/try/cognitive-services/). Для анализа кадров видео можно использовать следующие API-интерфейсы:
    - [API компьютерного зрения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
    - [API распознавания эмоций](https://docs.microsoft.com/azure/cognitive-services/emotion/home)
    - [API распознавания лиц](https://docs.microsoft.com/azure/cognitive-services/face/overview)
2. Клонируйте репозиторий GitHub [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).

3. Откройте пример в Visual Studio 2015, создайте и запустите примеры приложений:
    - Для примера BasicConsoleSample ключ API распознавания лиц содержится непосредственно в коде [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs).
    - Для LiveCameraSample ключи следует ввести в области "Параметры" приложения. Они будут сохраняться во всех сеансах как пользовательские данные.
        

Когда все будет готово к интеграции, **просто создайте ссылку на библиотеку VideoFrameAnalyzer из собственных проектов**. 



## <a name="developer-code-of-conduct"></a>Правила поведения разработчиков
Как и для всех служб Cognitive Services, разработчики, работающие с нашими API-интерфейсами и примерами, должны следовать [правилам поведения разработчиков служб Microsoft Cognitive Services](https://azure.microsoft.com/support/legal/developer-code-of-conduct/). 


В функциях библиотеки VideoFrameAnalyzer для распознавания изображений, речи, видео или текста используются службы Microsoft Cognitive Services. Корпорация Майкрософт будет получать изображения, аудио, видео и другие данные, отправленные вами (с помощью этого приложения), чтобы использовать их для улучшения служб. Мы просим вас внимательно относиться к данным людей, которые ваше приложение отправляет в службы Microsoft Cognitive Services. 


## <a name="summary"></a>Сводка
В этом руководстве описано, как запустить анализ потокового видео практически в реальном времени с использованием API распознавания лиц и эмоций, а также API компьютерного зрения, и приступить к работе с использованием наших примеров кода. Вы можете создать свое приложения, используя бесплатные ключи API на [странице регистрации Microsoft Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). 

Отправляйте отзывы и предложения в [репозиторий GitHub](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/), а также развернутые идеи об API на наш [сайт UserVoice](https://cognitive.uservoice.com/).

