---
title: Использование конечной точки прогнозирования Пользовательской службы визуального распознавания в Azure Cognitive Services | Документация Майкрософт
description: Сведения о том, как использовать API для программного тестирования изображений с помощью классификатора Пользовательской службы визуального распознавания.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: d7f9b90db06811e16cd0cd6ad2b32a27912cfee5
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43341799"
---
# <a name="use-the-prediction-endpoint-to-test-images-programmatically-with-a-custom-vision-service-classifier"></a>Использование конечной точки прогноза для программного тестирования изображений с помощью классификатора Пользовательской службы визуального распознавания

После обучения модели можно протестировать изображения программными средствами, отправив их в API прогнозирования. 

> [!NOTE]
> В этом документе описано, как использовать C# для отправки изображения в API прогнозирования. См. дополнительные сведения и примеры использования [API прогнозирования](https://go.microsoft.com/fwlink/?linkid=865445).

## <a name="get-the-url-and-prediction-key"></a>Получение URL-адреса и ключа прогнозирования

На [веб-странице Пользовательской службы визуального распознавания](https://customvision.ai) выберите проект и перейдите на вкладку __Производительность__. Чтобы отобразить сведения об использовании API прогнозирования, которое включает в себя __ключ прогнозирования__, выберите __URL-адрес прогнозирования__. Для проектов, присоединенных к ресурсу Azure, ваш __ключ прогнозирования__ также можно найти на странице [портала Azure](https://portal.azure.com) для связанного ресурса Azure в разделе __Ключи__. Скопируйте следующие данные для использования в приложении:

* __URL-адрес__ для использования __файла изображения__;
* значение __Prediction-key__ (ключ прогнозирования).

> [!TIP]
> При наличии нескольких итераций можно настроить, какая из них будет использоваться по умолчанию. Выберите итерацию из раздела __Итерации__ и щелкните __Установить по умолчанию__ в верхней части страницы.

![Вкладка "Производительность" показана с красным прямоугольником вокруг URL-адреса прогнозирования.](./media/use-prediction-api/prediction-url.png)

## <a name="create-the-application"></a>Создание приложения

1. В Visual Studio создайте новое консольное приложение C#.

2. Используйте следующий код в файле __Program.cs__:

    > [!IMPORTANT]
    > Измените следующие сведения:
    >
    > * задайте в качестве __namespace__ имя проекта;
    > * задайте полученное ранее значение __Prediction-Key__ в строке, которая начинается с `client.DefaultRequestHeaders.Add("Prediction-Key",`;
    > * задайте полученное ранее значение __URL-адреса__ в строке, которая начинается с `string url =`.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CSPredictionSample
    {
        static class Program
        {
            static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }

            static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid subscription key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

                // Prediction URL - replace this example URL with your valid prediction URL.
                string url = "http://southcentralus.api.cognitive.microsoft.com/customvision/v1.0/prediction/d16e136c-5b0b-4b84-9341-6a3fff8fa7fe/image?iterationId=f4e573f6-9843-46db-8018-b01d034fd0f2";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }
        }
    }
    ```

## <a name="use-the-application"></a>Использование приложения

При запуске приложения следует ввести путь к файлу изображения. Изображение передается в API, а результаты возвращаются в виде документа JSON. Ниже приведен привет ответа в формате JSON:

```json
{
    "Id":"3f76364c-b8ae-4818-a2b2-2794cfbe377a",
    "Project":"2277aca4-7aff-4742-8afb-3682e251c913",
    "Iteration":"84105bfe-73b5-4fcc-addb-756c0de17df2",
    "Created":"2018-05-03T14:15:22.5659829Z",
    "Predictions":[
        {"TagId":"35ac2ad0-e3ef-4e60-b81f-052a1057a1ca","Tag":"dog","Probability":0.102716163},
        {"TagId":"28e1a872-3776-434c-8cf0-b612dd1a953c","Tag":"cat","Probability":0.02037274}
    ]
}
```

## <a name="next-steps"></a>Дополнительная информация

[Экспорт модели для использования на мобильных устройствах](export-your-model.md)
