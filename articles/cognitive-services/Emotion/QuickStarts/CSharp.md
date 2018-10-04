---
title: Начало работы с API распознавания эмоций для C# | Документация Майкрософт
description: Информация и примеры кода, которые помогут быстро приступить к работе с API распознавания эмоций для C# в Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 11/02/2017
ms.author: anroth
ms.openlocfilehash: 89735ae54395447e3cb421f45db3d6b99001ecd6
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016571"
---
# <a name="emotion-api-c-quick-start"></a>Быстрое начало работы с AP распознавания эмоций для C#

> [!IMPORTANT]
> Срок действия предварительной версии API для поиска видео истек 30 октября 2017 г. Чтобы легко извлекать сведения из видео, попробуйте применить новый [API Индексатора видео (предварительная версия)](https://azure.microsoft.com/services/cognitive-services/video-indexer/). Также вы можете с его помощью дополнить процессы поиска содержимого, например обнаруживая в результатах поиска произнесенные слова, лица, персонажей и эмоции. Дополнительные сведения см. [в обзоре Индексатора видео (предварительная версия)](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

В этой статье представлены сведения и примеры кода, которые помогут быстро приступить к работе с [методом Recognize из API распознавания эмоций](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) для C#. Он позволяет распознавать эмоции одного или нескольких человек на изображении. 

## <a name="prerequisites"></a>Предварительные требования
* Получите [пакет Windows SDK для API распознавания эмоций](https://www.nuget.org/packages/Microsoft.ProjectOxford.Emotion/) из Cognitive Services.
* Получите бесплатный [ключ подписки](https://azure.microsoft.com/try/cognitive-services/).

## <a name="emotion-recognition-c-example-request"></a>Пример запроса для распознавания эмоций на C#

Создайте консольное решение в Visual Studio и замените текст файла Program.cs следующим фрагментом кода. Вместо `string uri` укажите регион, в котором вы получили ключи подписки. Замените строковое значение **Ocp-Apim-Subscription-Key** действующим ключом подписки. Ключ подписки можно получить на портале Azure. На панели навигации слева в разделе **Ключи** перейдите к ресурсу "API распознавания эмоций". Также вы можете получить правильный URI для подключения на панели **Обзор** для ресурса, указанного в разделе **Конечная точка**.

![Ключи для ресурса API-интерфейса](../../media/emotion-api/keys.png)

Чтобы обработать ответ на запрос, примените `Newtonsoft.Json` или другую аналогичную библиотеку. Это позволит обрабатывать строку JSON как серию управляемых объектов, называемых маркерами. Чтобы добавить в пакет эту библиотеку, щелкните правой кнопкой мыши проект в обозревателе решений и выберите **Управление пакетами NuGet**. Выполните поиск по строке **Newtonsoft**. Файл **Newtonsoft.json** должен оказаться первым в списке результатов. Щелкните **Установить**. Теперь эту библиотеку можно использовать в приложении.

![Установка Newtonsoft.Json](../../media/emotion-api/newtonsoft-nuget.png)

```csharp
using System;
using System.IO;
using System.Net.Http.Headers;
using System.Net.Http;
using Newtonsoft.Json.Linq;

namespace CSHttpClientSample
{
    static class Program
    {
        static void Main()
        {
            Console.Write("Enter the path to a JPEG image file:");
            string imageFilePath = Console.ReadLine();

            MakeRequest(imageFilePath);

            Console.WriteLine("\n\n\nWait for the result below, then hit ENTER to exit...\n\n\n");
            Console.ReadLine(); // wait for ENTER to exit program
        }

        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }

        static async void MakeRequest(string imageFilePath)
        {
            var client = new HttpClient();

            // Request headers - replace this example key with your valid key.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<your-subscription-key>"); // 

            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URI below with "westcentralus".
            string uri = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize?";
            HttpResponseMessage response;
            string responseContent;

            // Request body. Try this sample with a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (var content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                response = await client.PostAsync(uri, content);
                responseContent = response.Content.ReadAsStringAsync().Result;
            }

            // A peek at the raw JSON response.
            Console.WriteLine(responseContent);

            // Processing the JSON into manageable objects.
            JToken rootToken = JArray.Parse(responseContent).First;

            // First token is always the faceRectangle identified by the API.
            JToken faceRectangleToken = rootToken.First;

            // Second token is all emotion scores.
            JToken scoresToken = rootToken.Last;

            // Show all face rectangle dimensions
            JEnumerable<JToken> faceRectangleSizeList = faceRectangleToken.First.Children();
            foreach (var size in faceRectangleSizeList) {
                Console.WriteLine(size);
            }

            // Show all scores
            JEnumerable<JToken> scoreList = scoresToken.First.Children();
            foreach (var score in scoreList) {
                Console.WriteLine(score);
            }
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Пример ответа операции распознавания эмоций
При успешном вызове возвращается массив записей лиц и связанные с ними оценки эмоций. Они сортируются по убыванию в зависимости от размера прямоугольника с лицом. Пустой ответ указывает на то, что лица не обнаружены. Запись об эмоции содержит следующие поля:

* faceRectangle — расположение прямоугольника с лицом на изображении;
* scores — результаты оценки эмоций для каждого лица на изображении. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
