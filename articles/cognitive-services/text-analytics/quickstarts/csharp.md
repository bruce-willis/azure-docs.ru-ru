---
title: Краткое руководство по Azure Cognitive Services и API анализа текста для C# | Документы Майкрософт
description: Получайте информацию и примеры кода, которые помогут вам приступить к работе с API анализа текста в Cognitive Services в Azure.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: d9c61a83450844461f621ff16354881a029f7ad6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266300"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Краткое руководство по работе с API анализа текста для C# 
<a name="HOLTop"></a>

В этой статье содержатся сведения об обнаружении языка, анализе тональности и извлечении ключевых фраз с использованием [API анализа текста ](//go.microsoft.com/fwlink/?LinkID=759711) для C#. Код был написан для работы в приложении .NET Core с минимальными ссылками на внешние библиотеки, поэтому его также можно выполнять в Linux или MacOS.

Техническую документацию по API-интерфейсам см. в разделе [Определения API](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>предварительным требованиям

Необходима [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API анализа текста**. Для выполнения действий в этом кратком руководстве можно использовать **уровень "Бесплатный" на 5000 транзакций в месяц**.

Также требуются [конечная точка и ключ доступа](../How-tos/text-analytics-how-to-access-key.md), созданный автоматически во время регистрации. 


## <a name="install-the-nuget-sdk-package"></a>Установка пакета SDK для пакетов NuGet
1. В Visual Studio создайте консольное приложение.
1. Щелкните решение правой кнопкой мыши и выберите пункт **Управление пакетами NuGet для решения**.
1. Установите флажок **Включить предварительные выпуски**.
1. Откройте вкладку **Обзор** и найдите **Microsoft.Azure.CognitiveServices.Language**.
1. Выберите пакет NuGet и установите его.

> [!Tip]
>  Несмотря на то, что [конечные точки HTTP](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) можно вызывать напрямую из C#, пакет SDK Microsoft.Azure.CognitiveServices.Language значительно упрощает процесс вызова службы без сериализации и десериализации JSON.
>
> Несколько полезных ссылок
> - [Страница пакета SDK для NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [Код пакета SDK](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Вызов API анализа текста с помощью пакета SDK
1. Замените файл Program.cs на код, указанный ниже. Эта программа показывает возможности API анализа текста в трех разделах (извлечение языка, извлечение ключевой фразы и анализ тональности).
1. Замените значение заголовка `Ocp-Apim-Subscription-Key` с ключом доступа, допустимым для вашей подписки.
1. Замените расположение в `client.AzureRegion` (в настоящее время `AzureRegions.Westus`) на регион вашей регистрации.
1. Запустите программу.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsAPI client = new TextAnalyticsAPI(new ApiKeyServiceClientCredentials());
            client.AzureRegion = AzureRegions.Westus;

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }

            // Extracting sentiment
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
        }
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Text Analytics with Power BI](../tutorials/tutorial-power-bi-key-phrases.md) (Анализ текста с использованием Power BI)

## <a name="see-also"></a>См. также 

 [Text Analytics overview](../overview.md) (Общие сведения об анализе текста)  
 [Часто задаваемые вопросы](../text-analytics-resource-faq.md)

