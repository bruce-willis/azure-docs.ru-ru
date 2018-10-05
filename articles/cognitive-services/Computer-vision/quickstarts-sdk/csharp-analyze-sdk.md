---
title: Краткое руководство по анализу изображения с использованием пакета SDK API компьютерного зрения для C#
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как анализировать изображения с помощью клиентской библиотеки API компьютерного зрения для C# в Windows.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 09/14/2018
ms.author: nolachar
ms.openlocfilehash: 0315b1c90eeae27d30a237aea76e66465818fba4
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056090"
---
# <a name="quickstart-analyze-an-image-using-the-computer-vision-sdk-and-c"></a>Краткое руководство. Анализ изображения с использованием пакета SDK API компьютерного зрения для C#

Из этого краткого руководства вы узнаете, как с помощью клиентской библиотеки компьютерного зрения для Windows анализировать локальные и удаленные изображения, чтобы извлекать визуальные признаки.

Исходный код этого примера доступен на [GitHub](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision).

## <a name="prerequisites"></a>Предварительные требования

* Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).
* Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).
* Пакет NuGet клиентской библиотеки [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Скачивать пакет не нужно. Инструкции по установке приведены ниже.

## <a name="analyzeimageasync-method"></a>Метод AnalyzeImageAsync

Методы `AnalyzeImageAsync` и `AnalyzeImageInStreamAsync` создают программу-оболочку [API анализа изображений](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) для удаленных и локальных изображений соответственно. С помощью этих методов можно извлекать визуальные признаки на основе содержимого изображения и выбирать, какие из них следуя вернуть, в частности:

* подробный список тегов, связанных с содержимым изображения;
* описание содержимого изображения в виде полного предложения;
* координаты, пол и возраст любого лица, показанного на изображении;
* значение ImageType (картинки или линейный рисунок);
* преобладающий цвет, контрастный цвет, наличие или отсутствие цветности;
* категорию, определенную в этой [таксономии](../Category-Taxonomy.md);
* наличие на изображении содержимого для взрослых или содержимого сексуального характера.

Чтобы выполнить наш пример, сделайте следующее:

1. Создайте консольное приложение Visual C# в Visual Studio.
1. Установите пакет NuGet клиентской библиотеки компьютерного зрения.
    1. В меню щелкните **Средства**, выберите **Диспетчер пакетов NuGet**, а затем **Управление пакетами NuGet для решения**.
    1. Нажмите кнопку **Обзор** и в поле **Поиск** введите Microsoft.Azure.CognitiveServices.Vision.ComputerVision.
    1. Выберите **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**, установите флажок рядом с именем проекта и щелкните **Установить**.
1. Замените `Program.cs` следующим кодом.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. При необходимости замените `computerVision.Endpoint` регионом Azure, связанным с ключами подписки.
1. Замените `<LocalImage>` на путь и имя файла локального изображения.
1. При необходимости задайте `remoteImageUrl` другого изображения.
1. Запустите программу.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace ImageAnalyze
{
    class Program
    {
        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg";

        // Specify the features to return
        private static readonly List<VisualFeatureTypes> features =
            new List<VisualFeatureTypes>()
        {
            VisualFeatureTypes.Categories, VisualFeatureTypes.Description,
            VisualFeatureTypes.Faces, VisualFeatureTypes.ImageType,
            VisualFeatureTypes.Tags
        };

        static void Main(string[] args)
        {
            ComputerVisionClient computerVision = new ComputerVisionClient(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "westcentralus" with "westus".
            //
            // Free trial subscription keys are generated in the westcentralus
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

            Console.WriteLine("Images being analyzed ...");
            var t1 = AnalyzeRemoteAsync(computerVision, remoteImageUrl);
            var t2 = AnalyzeLocalAsync(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press ENTER to exit");
            Console.ReadLine();
        }

        // Analyze a remote image
        private static async Task AnalyzeRemoteAsync(
            ComputerVisionClient computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            ImageAnalysis analysis =
                await computerVision.AnalyzeImageAsync(imageUrl, features);
            DisplayResults(analysis, imageUrl);
        }

        // Analyze a local image
        private static async Task AnalyzeLocalAsync(
            ComputerVisionClient computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                ImageAnalysis analysis = await computerVision.AnalyzeImageInStreamAsync(
                    imageStream, features);
                DisplayResults(analysis, imagePath);
            }
        }

        // Display the most relevant caption for the image
        private static void DisplayResults(ImageAnalysis analysis, string imageUri)
        {
            Console.WriteLine(imageUri);
            Console.WriteLine(analysis.Description.Captions[0].Text + "\n");
        }
    }
}
```

## <a name="analyzeimageasync-response"></a>Результат AnalyzeImageAsync

При успешном выполнении в ответе будет отображаться наиболее релевантная подпись для каждого изображения.

Пример необработанных данных в формате JSON см. в статье [API Quickstarts: Analyze a local image with C#](../QuickStarts/CSharp-analyze.md#examine-the-response) (Краткие руководства по API: анализ локального изображения с помощью C#).

```
http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg
a large waterfall over a rocky cliff
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами компьютерного зрения, которые позволяют анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст.

> [!div class="nextstepaction"]
> [Сведения об API-интерфейсах компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
