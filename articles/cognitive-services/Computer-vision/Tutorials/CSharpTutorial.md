---
title: Руководство по API компьютерного зрения для Python | Документация Майкрософт
description: Изучите базовое приложение Windows, в котором используется API компьютерного зрения в Microsoft Cognitive Services. Узнайте, как выполнять распознавание текста, создавать эскизы и работать с визуальными компонентами в изображении.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381020"
---
# <a name="computer-vision-api-c35-tutorial"></a>Руководство по API компьютерного зрения для C&#35;

Исследуйте приложение Windows, которое использует API компьютерного зрения для оптического распознавания символов (OCR) и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных компонентов изображения, включая лица. Приведенный ниже пример позволяет отправить URL-адрес изображения или локально хранящийся файл. Вы можете использовать этот пример с открытым кодом в качестве шаблона для создания собственного приложения для Windows с использованием API компьютерного зрения и WPF (Windows Presentation Foundation), входящего в состав .NET Framework.

### <a name="prerequisites"></a>предварительным требованиям

#### <a name="platform-requirements"></a>Требования платформы

Пример был разработан для платформы .NET Framework с помощью [Visual Studio 2015 Community Edition](https://www.visualstudio.com/downloads/).

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Получение подписки API компьютерного зрения и ключа подписки 

Перед созданием примера необходимо получить подписку API компьютерного зрения, который является частью Microsoft Cognitive Services (прежнее название — Project Oxford). Сведения о подписках и управлении ключами см. в разделе [Подписки](https://azure.microsoft.com/try/cognitive-services/). В этом руководстве можно использовать как первичный, так и вторичный ключ. 

> [!NOTE]
> Руководство предназначено для использования ключей подписки в регионе **westcentralus**. В ключах подписки, создаваемых бесплатной ознакомительной версией API компьютерного зрения, используется регион **westcentralus**, поэтому они работают правильно. Если вы создали ключи подписки с помощью учетной записи Azure посредством [https://azure.microsoft.com/](https://azure.microsoft.com/), необходимо указать регион **westcentralus**. Ключи, созданные за пределами региона **westcentralus**, не будут работать.

#### <a name="get-the-client-library-and-example"></a>Получение клиентской библиотеки и примера

Вы можете клонировать клиентскую библиотеку API компьютерного зрения и пример приложения на свой компьютер с помощью [пакета SDK](https://www.github.com/microsoft/cognitive-vision-windows). Не следует скачивать их как ZIP-файл.

### <a name="Step1">Шаг 1. Установка примера</a>

В GitHub Desktop откройте файл Sample-WPF\VisionAPI-WPF-Samples.sln.

### <a name="Step2">Шаг 2. Компиляция примера</a>

* Нажмите клавиши CTRL+SHIFT+B или выберите действие "Build" (Сборка) в меню ленты, а затем выберите "Build Solution" (Выполнить сборку решения).

### <a name="Step3">Шаг 3. Запуск примера</a>

1. После завершения сборки нажмите клавишу **F5** или щелкните **Start** (Запустить) в меню ленты, чтобы выполнить созданный пример.
2. Найдите окно пользовательского интерфейса API компьютерного зрения с текстовым полем, в котором написано "Paste your subscription key here to start" (Вставьте сюда ключ подписки для запуска).
Вы можете сохранить ключ подписки на своем компьютере или ноутбуке, нажав кнопку "Save Key" (Сохранить ключ). Если вы хотите удалить ключ подписки из системы, нажмите кнопку "Delete Key" (Удалить ключ). Таким образом вы удаляете его с ноутбука или компьютера.

    ![Ключ подписки API компьютерного зрения](../Images/Vision_UI_Subscription.PNG)

3. В разделе "Select Scenario" (Выбор сценария) выберите один из шести сценариев, а затем следуйте инструкциям на экране. Корпорация Майкрософт получает переданные изображения и может использовать их для улучшения API компьютерного зрения и связанных с ним служб. Отправляя изображения, вы подтверждаете, что были выполнены наши [правила поведения для разработчиков](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

    ![Интерфейс анализа изображений](../Images/Analyze_Image_Example.PNG)

4. Доступны примеры изображений, которые можно использовать в этом примере приложения. Эти изображения можно найти в репозитории API распознавания лиц для Windows на сайте GitHub в папке [Data](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data). Обратите внимание на то, что использование этих изображений лицензируется в рамках соглашения [LICENSE-IMAGE](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md).

### <a name="Review">Просмотр и изучение</a>

Теперь у вас есть работающее приложение, и на его примере мы рассмотрим интеграцию этого примера приложения с технологией Cognitive Services. Благодаря этому вам будет проще продолжать развитие этого приложения или разработать собственное приложение, которое использует API компьютерного зрения Майкрософт.

В этом примере приложение использует клиентскую библиотеку API компьютерного зрения и тонкую клиентскую программу-оболочку C# для API компьютерного зрения Майкрософт. При сборке примера приложения, как описано выше, вы получаете клиентскую библиотеку из пакета NuGet. Вы можете просмотреть исходный код клиентской библиотеки в папке **Client Library** в разделе **Vision**, **Windows**, **Client Library**, который является частью скачанного репозитория файлов, упомянутого выше в разделе предварительных требований.

Вы можете также узнать, как использовать код клиентской библиотеки в обозревателе решений. В папке **VisionAPI-WPF_Samples** разверните элемент **AnalyzePage.xaml**, чтобы найти файл **AnalyzePage.xaml.cs**, который используется для отправки изображения на конечную точку анализа изображений. Дважды щелкните XAML.CS-файлы, чтобы они отобразились в новых окнах Visual Studio.

Чтобы узнать, как клиентская библиотека API компьютерного зрения используется в нашем примере приложении, давайте рассмотрим два фрагмента кода из **AnalyzePage.xaml.cs**. Этот файл содержит комментарии к коду, в которых есть фразы "KEY SAMPLE CODE STARTS HERE" (Здесь начинается основной пример кода) и "KEY SAMPLE CODE ENDS HERE" (Здесь завершается основной пример кода), которые помогут найти приведенные ниже фрагменты кода.

Конечная точка анализа может работать с URL-адресом изображения или двоичными данными изображения (в виде потока октетов) в качестве входных данных. Сначала следует найти директиву using, позволяющую использовать клиентскую библиотеку API компьютерного зрения.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(...)** В этом фрагменте кода показано, как использовать клиентскую библиотеку для отправки ключа подписки и хранящегося локально изображения на конечную точку анализа службы API компьютерного зрения.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(...)** В этом фрагменте кода показано, как использовать клиентскую библиотеку для отправки ключа подписки и URL-адреса фотографии на конечную точку анализа службы API компьютерного зрения.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**Остальные страницы и конечные точки**. Чтобы изучить способы взаимодействия с другими конечными точками, предоставляемыми службой API компьютерного зрения, можно просмотреть другие страницы в примере. Например, конечная точка OCR показана в коде, содержащемся в файле OCRPage.xaml.cs. 

### <a name="Related">Связанные разделы</a>
 * [Приступая к работе с API распознавания лиц](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


