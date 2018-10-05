---
title: Руководство. Обнаружение и выделение лиц на изображении с помощью API распознавания лиц и C#
titleSuffix: Azure Cognitive Services
description: С помощью этого руководства вы создадите простое приложение Windows, которое использует API распознавания лиц для обнаружения и выделения лиц на изображении.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: nolachar
ms.openlocfilehash: 657c471761c36de5095763623210909308f55c2a
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162617"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>Руководство. Создание приложения WPF для обнаружения и выделения лиц на изображении

В рамках работы с этим руководством вы создадите приложение на основе подсистемы Windows Presentation Framework (WPF), в котором используется служба распознавания лиц через клиентскую библиотеку .NET. Приложение обнаруживает лица на изображении, создает рамку вокруг каждого лица и отображает описание лица в строке состояния. Полный пример кода можно найти на странице GitHub [Detect and frame faces in an image on Windows](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) (Обнаружение и выделение лиц на изображении в Windows).

![Снимок экрана, на котором обнаруженные лица выделены прямоугольниками](../Images/getting-started-cs-detected.png)

В этом учебнике описаны следующие процедуры.

> [!div class="checklist"]
> - создание приложения WPF;
> - установка клиентской библиотеки службы распознавания лиц;
> - использование клиентской библиотеки для обнаружения лиц на изображении;
> - выделение рамкой каждого обнаруженного лица;
> - отображение описания лица в строке состояния.

## <a name="prerequisites"></a>Предварительные требования

- Чтобы выполнить пример, нужен ключ подписки. Вы можете получить ключи бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/). Для Visual Studio 2017 рабочая нагрузка по разработке классического приложения .NET является обязательной. В этом руководстве используется выпуск Visual Studio Community 2017.
- Пакет NuGet клиентской библиотеки [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Скачивать пакет не нужно. Инструкции по установке приведены ниже.

## <a name="create-the-visual-studio-solution"></a>Создание решения Visual Studio

Чтобы создать проект приложения WPF, сделайте следующее.

1. Откройте Visual Studio, выберите меню **Файл**, щелкните **Создать**, а затем — **Проект**.
   - В Visual Studio 2017 разверните раздел **Установленные**, а затем — **Другие языки**. Выберите **Visual C#**, а затем — **WPF App (.NET Framework)** (Приложение WPF (.NET Framework)).
   - В Visual Studio 2015 разверните раздел **Установленные**, а затем — **Шаблоны**. Выберите **Visual C#**, а затем — **Приложение WPF**.
1. Присвойте приложению имя **FaceTutorial** и нажмите кнопку **ОК**.

## <a name="install-the-face-service-client-library"></a>установка клиентской библиотеки службы распознавания лиц;

Чтобы установить клиентскую библиотеку, выполните следующие инструкции.

1. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**.
1. В **консоли диспетчера пакетов** вставьте следующую команду, а затем нажмите клавишу **ВВОД**.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.2.0-preview`

## <a name="add-the-initial-code"></a>Добавление исходного кода

### <a name="mainwindowxaml"></a>MainWindow.xaml:

Откройте файл *MainWindow.xaml* (совет: поменяйте области местами с помощью **значков со стрелками вверх и вниз**) и замените его содержимое приведенным ниже кодом. Этот код XAML используется для создания окна пользовательского интерфейса. Обратите внимание на обработчики событий `FacePhoto_MouseMove` и `BrowseButton_Click`.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Разверните файл *MainWindow.xaml*, затем откройте файл *MainWindow.xaml.cs* и замените его содержимое приведенным ниже кодом. Не обращайте внимания на красные волнистые линии подчеркивания — они исчезнут после первой сборки.

Первые две строки используются для импорта пространств имен клиентской библиотеки. Затем создается элемент `FaceClient`, с помощью которого передается ключ подписки во время настройки региона Azure в конструкторе `MainWindow`. Два метода — `BrowseButton_Click` и `FacePhoto_MouseMove` — соответствуют обработчикам событий, объявленным в файле *MainWindow.xaml*.

`BrowseButton_Click` создает `OpenFileDialog`, что позволяет пользователю выбрать изображение в формате JPG. Изображение считывается и отображается в главном окне. Оставшийся код для `BrowseButton_Click` и код для `FacePhoto_MouseMove` вставляются на последующих этапах.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(faceEndpoint, UriKind.Absolute))
            {
                faceClient.Endpoint = faceEndpoint;
            }
            else
            {
                MessageBox.Show(faceEndpoint,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>Добавление ключа подписки и подтверждение или изменение региона

- Найдите следующую строку в файле *MainWindow.xaml.cs* и замените значение `<Subscription Key>` ключом подписки API распознавания лиц:

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- Найдите следующую строку в файле *MainWindow.xaml.cs* и измените или подтвердите регион Azure, связанный с ключом подписки:

    ```csharp
    private const string Endpoint =
        "https://westcentralus.api.cognitive.microsoft.com";
    ```

    Убедитесь, что расположение соответствует региону, в котором вы получили ключи подписки. Например, если вы получили ключи подписки в регионе **westus**, вместо `Westcentralus` укажите `Westus`.

    Если вы получили свои ключи подписки с помощью бесплатной пробной версии, регион для ваших ключей — **westcentralus**, поэтому никаких изменений не требуется.

### <a name="test-the-app"></a>Тестирование приложения

В меню выберите команду **Начать**, чтобы протестировать приложение. В открывшемся окне в левом нижнем углу нажмите кнопку **Обзор**. Откроется диалоговое окно **Открытие файла**, где можно просмотреть и выбрать фотографию, которая затем отобразится в окне.

![Снимок экрана с фотографией лиц без изменений](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>Отправка изображения для обнаружения лиц

Самый простой способ обнаружения лиц — вызвать метод `FaceClient.Face.DetectWithStreamAsync`, который служит оболочкой метода API [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) (Обнаружение) для отправки локального изображения.

Вставьте следующий метод в класс `MainWindow` под методом `FacePhoto_MouseMove`.

Создается список атрибутов лица для анализа, и отправленный файл изображения считывается в `Stream`. Оба компонента передаются в вызов `DetectWithStreamAsync`.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>Создание прямоугольника вокруг каждого лица

Добавьте код, чтобы добавить прямоугольник вокруг каждого обнаруженного лица на изображении.

В файле *MainWindow.xaml.cs* добавьте модификатор `async` в метод `BrowseButton_Click`.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Вставьте приведенный ниже код в конец метода `BrowseButton_Click`, после строки `FacePhoto.Source = bitmapSource`.

Список обнаруженных лиц заполняется с помощью вызова метода `UploadAndDetectFaces`. Затем вокруг каждого лица создается прямоугольник, и измененное изображение отображается в главном окне.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>Описание лиц на изображении

Добавьте следующий метод в класс `MainWindow` перед методом `UploadAndDetectFaces`.

Этот метод преобразует атрибуты лица в строку описания лица. Эта строка отображается при наведении указателя мыши на прямоугольник с лицом.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>Отображение описания лица

Замените метод `FacePhoto_MouseMove` приведенным ниже кодом.

Этот обработчик событий отображает строку описания лица при наведении указателя мыши на лицо в прямоугольнике.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>Запуск приложения

Запустите приложение и с помощью обзора найдите изображение, содержащее лицо. Подождите несколько секунд, чтобы служба распознавания лиц смогла ответить. После этого лица на изображении будут выделены красными прямоугольниками. При наведении указателя мыши на прямоугольник вокруг лица в строке состояния появится описание лица.

![Снимок экрана, на котором обнаруженные лица выделены прямоугольниками](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>Сводка

С помощью этого руководства вы изучили базовый процесс использования клиентской библиотеки службы распознавания лиц и создали приложение, в котором лица на изображениях отображаются и выделяются рамкой.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об обнаружении лиц и использовании ориентиров для их обнаружения.

> [!div class="nextstepaction"]
> [Как обнаруживать лица на изображении](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
