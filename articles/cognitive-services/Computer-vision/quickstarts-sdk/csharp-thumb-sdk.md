---
title: Краткое руководство по API компьютерного зрения для C#. Создание эскиза | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: В этом кратком руководстве вы узнаете, как создать эскиз изображения с помощью клиентской библиотеки компьютерного зрения для C# в Windows.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: e26d2da8f068b3b23b8211dc88cd21ca4a049018
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772174"
---
# <a name="quickstart-generate-a-thumbnail---sdk-c35"></a>Краткое руководство по созданию эскиза (пакет SDK, C#)

В этом кратком руководстве вы узнаете, как создать эскиз изображения с помощью клиентской библиотеки компьютерного зрения.

## <a name="prerequisites"></a>Предварительные требования

* Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).
* Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/).
* Пакет NuGet клиентской библиотеки [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision). Скачивать пакет не нужно. Инструкции по установке приведены ниже.

## <a name="generatethumbnailasync-method"></a>Метод GenerateThumbnailAsync

Методы `GenerateThumbnailAsync` и `GenerateThumbnailInStreamAsync` создают программу-оболочку [API создания эскиза](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) для удаленных и локальных изображений соответственно.  Эти методы можно использовать для создания эскиза изображения. Вы можете указать нужную высоту и ширину. При этом пропорции могут отличаться от пропорций исходного изображения. API компьютерного зрения использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки для этой области.

Чтобы выполнить наш пример, сделайте следующее:

1. Создайте консольное приложение Visual C# в Visual Studio.
1. Установите пакет NuGet клиентской библиотеки компьютерного зрения.
    1. В меню щелкните **Средства**, выберите **Диспетчер пакетов NuGet**, а затем **Управление пакетами NuGet для решения**.
    1. Нажмите кнопку **Обзор** и в поле **Поиск** введите Microsoft.Azure.CognitiveServices.Vision.ComputerVision.
    1. Выберите **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**, установите флажок рядом с именем проекта и щелкните **Установить**.
1. Замените `Program.cs` следующим кодом.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. Вместо `computerVision.AzureRegion = AzureRegions.Westcentralus` укажите расположение, в котором вы получили ключи подписки, если нужно.
1. При необходимости вместо `<LocalImage>` укажите путь к файлу и имя локального файла изображения (этот параметр игнорируется, если значение не задано).
1. При необходимости задайте `remoteImageUrl` другого изображения.
1. При необходимости задайте для `writeThumbnailToDisk` значение `true`, чтобы сохранить эскиз его на диске.
1. Запустите программу.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

using System;
using System.IO;
using System.Threading.Tasks;

namespace ImageThumbnail
{
    class Program
    {
        private const bool writeThumbnailToDisk = false;

        // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // localImagePath = @"C:\Documents\LocalImage.jpg"
        private const string localImagePath = @"<LocalImage>";

        private const string remoteImageUrl =
            "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

        private const int thumbnailWidth = 100;
        private const int thumbnailHeight = 100;

        static void Main(string[] args)
        {
            ComputerVisionAPI computerVision = new ComputerVisionAPI(
                new ApiKeyServiceClientCredentials(subscriptionKey),
                new System.Net.Http.DelegatingHandler[] { });

            // You must use the same region as you used to get your subscription
            // keys. For example, if you got your subscription keys from westus,
            // replace "Westcentralus" with "Westus".
            //
            // Free trial subscription keys are generated in the westcentralus
            // region. If you use a free trial subscription key, you shouldn't
            // need to change the region.

            // Specify the Azure region
            computerVision.AzureRegion = AzureRegions.Westcentralus;

            Console.WriteLine("Images being analyzed ...\n");
            var t1 = GetRemoteThumbnailAsync(computerVision, remoteImageUrl);
            var t2 = GetLocalThumbnailAsnc(computerVision, localImagePath);

            Task.WhenAll(t1, t2).Wait(5000);
            Console.WriteLine("Press any key to exit");
            Console.ReadLine();
        }

        // Create a thumbnail from a remote image
        private static async Task GetRemoteThumbnailAsync(
            ComputerVisionAPI computerVision, string imageUrl)
        {
            if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
            {
                Console.WriteLine(
                    "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                return;
            }

            Stream thumbnail = await computerVision.GenerateThumbnailAsync(
                thumbnailWidth, thumbnailHeight, imageUrl, true);

            string path = Environment.CurrentDirectory;
            string imageName = imageUrl.Substring(imageUrl.LastIndexOf('/') + 1);
            string thumbnailFilePath =
                path + "\\" + imageName.Insert(imageName.Length - 4, "_thumb");

            // Save the thumbnail to the current working directory,
            // using the original name with the suffix "_thumb".
            SaveThumbnail(thumbnail, thumbnailFilePath);
        }

        // Create a thumbnail from a local image
        private static async Task GetLocalThumbnailAsnc(
            ComputerVisionAPI computerVision, string imagePath)
        {
            if (!File.Exists(imagePath))
            {
                Console.WriteLine(
                    "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                return;
            }

            using (Stream imageStream = File.OpenRead(imagePath))
            {
                Stream thumbnail = await computerVision.GenerateThumbnailInStreamAsync(
                    thumbnailWidth, thumbnailHeight, imageStream, true);

                string thumbnailFilePath =
                    localImagePath.Insert(localImagePath.Length - 4, "_thumb");

                // Save the thumbnail to the same folder as the original image,
                // using the original name with the suffix "_thumb".
                SaveThumbnail(thumbnail, thumbnailFilePath);
            }
        }

        // Save the thumbnail locally.
        // NOTE: This will overwrite an existing file of the same name.
        private static void SaveThumbnail(Stream thumbnail, string thumbnailFilePath)
        {
            if (writeThumbnailToDisk)
            {
                using (Stream file = File.Create(thumbnailFilePath))
                {
                    thumbnail.CopyTo(file);
                }
            }
            Console.WriteLine("Thumbnail {0} written to: {1}\n",
                writeThumbnailToDisk ? "" : "NOT", thumbnailFilePath);
        }
    }
}
```

## <a name="generatethumbnailasync-response"></a>Ответ от GenerateThumbnailAsync

В случае успешного выполнения для каждого изображения локально сохраняется эскиз и возвращается расположение этого эскиза, например так:

```cmd
Thumbnail written to: C:\Documents\LocalImage_thumb.jpg

Thumbnail written to: ...\bin\Debug\Bloodhound_Puppy_thumb.jpg
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами компьютерного зрения, которые позволяют анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст.

> [!div class="nextstepaction"]
> [Сведения об API-интерфейсах компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
