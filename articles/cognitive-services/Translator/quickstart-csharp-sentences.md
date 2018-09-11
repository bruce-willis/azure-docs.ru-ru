---
title: Получение длины предложения с помощью API перевода текстов и C# | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: В этом кратком руководстве описано, как получать длину предложений в тексте с помощью API перевода текстов и C# в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: b3e7f1099b1a7584435646fe3fae237cd458967f
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "43770622"
---
# <a name="quickstart-get-sentence-lengths-with-c35"></a>Краткое руководство. Получение длины предложения с помощью C#

В этом кратком руководстве объясняется, как определять длину предложений в тексте с помощью API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода на компьютерах под управлением Windows потребуется [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Будет работать бесплатный выпуск Community Edition.)

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>Запрос метода BreakSentence

Приведенный ниже код позволяет разбить исходный текст на предложения с помощью метода [BreakSentence](./reference/v3-0-break-sentence.md).

1. Создайте новый проект C# в избранной IDE.
2. Добавьте указанный ниже код.
3. Замените значение `key` ключом доступа, допустимым для подписки.
4. Запустите программу.

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/breaksentence?api-version=3.0";

        static string uri = host + path;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string text = "How are you? I am fine. What did you do today?";

        async static void Break()
        {
            System.Object[] body = new System.Object[] { new { Text = text } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                var result = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(responseBody), Formatting.Indented);

                Console.OutputEncoding = UnicodeEncoding.UTF8;
                Console.WriteLine(result);
            }
        }

        static void Main(string[] args)
        {
            Break();
            Console.ReadLine();
        }
    }
}
```

## <a name="breaksentence-response"></a>Ответ метода BreakSentence

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, включая перевод и транслитерацию, а также с другими примерами проектов перевода текстов в GitHub.

> [!div class="nextstepaction"]
> [Ознакомиться с примерами на C# на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=c%23)
