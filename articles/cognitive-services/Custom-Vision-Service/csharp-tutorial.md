---
title: Использование Пользовательской службы визуального распознавания в приложении C# в Azure Cognitive Services | Документация Майкрософт
description: Изучите базовое приложение C#, в котором используется API Пользовательского визуального распознавания в службах Microsoft Cognitive Services. Создайте проект, добавьте теги, загрузите изображения, обучите проект и выполните прогнозирование с использованием конечной точки по умолчанию.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 80cb022808748ed2c60dff7c363d6020cb4043a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381004"
---
# <a name="use-the-custom-vision-service-from-a-c35-application"></a>Использование Пользовательской службы визуального распознавания в приложении C&#35;

Сведения о том, как использовать Пользовательскую службу визуального распознавания в приложении C#. Создав проект, вы можете добавить теги, загрузить изображения, обучить проект, получить URL-адрес конечной точки прогнозирования проекта по умолчанию и с помощью конечной точки программными средствами протестировать изображение. Этот пример с открытым исходным кодом можно применить как шаблон для построения собственного приложения для Windows на базе API Пользовательской службы визуального распознавания.

## <a name="prerequisites"></a>предварительным требованиям

* Любой выпуск Visual Studio 2015 или 2017 для Windows.

* Пакет [SDK для Пользовательской службы визуального распознавания](http://github.com/Microsoft/Cognitive-CustomVision-Windows/). В него входят примеры и изображения, используемые в этом документе.

## <a name="get-the-training-and-prediction-keys"></a>Получение ключей обучения и прогнозирования

Чтобы получить ключи, используемые в этом примере, посетите [веб-страницу Пользовательской службы визуального распознавания](https://customvision.ai) и выберите __значок шестеренки__ в правом верхнем углу. В разделе __Учетные записи__ скопируйте значения в полях __Ключ обучения__ и __Ключ прогнозирования__.

![Изображение пользовательского интерфейса с ключами](./media/csharp-tutorial/training-prediction-keys.png)

## <a name="understand-the-code"></a>Изучение кода

В Visual Studio откройте проект, расположенный в каталоге `Samples/CustomVision.Sample/` проекта SDK.

В этом приложении используется ключ обучения, полученный ранее при создании проекта __My New Project__. Затем приложение передает изображения для обучения и тестирует классификатор. Классификатор определяет вид дерева — __Hemlock__ или __Japanese Cherry__.

Следующие фрагменты кода реализуют основные функции для этого примера:

* __Создание проекта Пользовательской службы визуального распознавания__:

    ```csharp
     // Create a new project
    Console.WriteLine("Creating new project:");
    var project = trainingApi.CreateProject("My New Project");
    ```

* __Создание тегов в проекте__:

    ```csharp
    // Make two tags in the new project
    var hemlockTag = trainingApi.CreateTag(project.Id, "Hemlock");
    var japaneseCherryTag = trainingApi.CreateTag(project.Id, "Japanese Cherry");
    ```

* __Отправка и снабжение тегами изображений__:

    ```csharp
    // Add some images to the tags
    Console.WriteLine("\tUploading images");
    LoadImagesFromDisk();

    // Images can be uploaded one at a time
    foreach (var image in hemlockImages)
    {
        using (var stream = new MemoryStream(File.ReadAllBytes(image)))
        {
            trainingApi.CreateImagesFromData(project.Id, stream, new List<string>() { hemlockTag.Id.ToString() });
        }
    }

    // Or uploaded in a single batch 
    var imageFiles = japaneseCherryImages.Select(img => new ImageFileCreateEntry(Path.GetFileName(img), File.ReadAllBytes(img))).ToList();
    trainingApi.CreateImagesFromFiles(project.Id, new ImageFileCreateBatch(imageFiles, new List<Guid>() { japaneseCherryTag.Id }));
    ```

* __Обучение классификатора__:

    ```csharp
    // Now there are images with tags start training the project
    Console.WriteLine("\tTraining");
    var iteration = trainingApi.TrainProject(project.Id);

    // The returned iteration will be in progress, and can be queried periodically to see when it has completed
    while (iteration.Status == "Completed")
    {
        Thread.Sleep(1000);

        // Re-query the iteration to get it's updated status
        iteration = trainingApi.GetIteration(project.Id, iteration.Id);
    }
    ```

* __Задание итерацию по умолчанию для конечной точки прогнозирования__:

    ```csharp
    // The iteration is now trained. Make it the default project endpoint
    iteration.IsDefault = true;
    trainingApi.UpdateIteration(project.Id, iteration.Id, iteration);
    Console.WriteLine("Done!\n");
    ```

* __Создание конечной точки прогнозирования__:
 
    ```csharp
    // Create a prediction endpoint, passing in obtained prediction key
    PredictionEndpoint endpoint = new PredictionEndpoint() { ApiKey = predictionKey };
    ```
 
* __Отправка изображения в конечную точку прогнозирования__:

    ```csharp
    // Make a prediction against the new project
    Console.WriteLine("Making a prediction:");
    var result = endpoint.PredictImage(project.Id, testImage);

    // Loop over each prediction and write out the results
    foreach (var c in result.Predictions)
    {
        Console.WriteLine($"\t{c.TagName}: {c.Probability:P1}");
    }
    ```

## <a name="run-the-application"></a>Выполнение приложения

1. Внесите следующие изменения, чтобы добавить ключи обучения и прогнозирования в приложение:

    * Добавьте __ключ обучения__ в следующую строку:

        ```csharp
        string trainingKey = "<your key here>";
        ```

    * Добавьте __ключ прогнозирования__ в следующую строку:

        ```csharp
        string predictionKey = "<your key here>";
        ```

2. Запустите приложение. При запуске приложения в консоль записываются следующие выходные данные:

    ```
    Creating new project:
            Uploading images
            Training
    Done!

    Making a prediction:
            Hemlock: 95.0%
            Japanese Cherry: 0.0%
    ```

3. Нажмите любую клавишу для выхода из приложения.
