---
title: Краткое руководство по пакету SDK C# для API проверки орфографии — Azure Cognitive Services | Документация Майкрософт
description: Установка консольного приложения пакета SDK для API проверки орфографии
titleSuffix: Azure cognitive services setup Spell check search SDK C# console application
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.openlocfilehash: b64c1be49a26e1fadb504bed3ff8eb78d791539f
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "43771259"
---
# <a name="spell-check-sdk-c-quickstart"></a>Краткое руководство по пакету SDK C# для API проверки орфографии

Пакет SDK для API Bing для проверки орфографии содержит функции REST API для проверки орфографии.

## <a name="application-dependencies"></a>Зависимости приложения

Чтобы настроить консольное приложение с помощью пакета SDK для API Bing для проверки орфографии, в обозревателе решений Visual Studio перейдите к параметру `Manage NuGet Packages`. Добавьте пакет `Microsoft.Azure.CognitiveServices.SpellCheck`.

При установке [пакета SDK для API проверки орфографии](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.SpellCheck/1.2.0) также будут установлены следующие зависимости:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure;
* Newtonsoft.Json.

## <a name="spell-check-client"></a>Клиент проверки орфографии

Чтобы создать экземпляр клиента `SpellCheckAPI`, добавьте директиву using:

```cs
using Microsoft.Azure.CognitiveServices.SpellCheck;
```

Затем создайте экземпляр клиента:

```cs
var client = new SpellCheckAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
```

Используйте клиент для проверки орфографии:

```cs
var result = client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof", acceptLanguage: "en-US").Result;
Console.WriteLine("Correction for Query# \"bill gatas\"");
```

Проанализируйте результаты:

```cs
// SpellCheck Results
if (result?.Body.FlaggedTokens?.Count > 0)
{
    // find the first spellcheck result
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();

    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);

        var suggestions = firstspellCheckResult.Suggestions;
        if (suggestions?.Count > 0)
        {
            var firstSuggestion = suggestions.FirstOrDefault();
            Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
            Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
        }
        }
        else
        {
            Console.WriteLine("Couldn't get any Spell check results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any SpellCheck results..");
    }
```

## <a name="complete-console-application"></a>Готовое консольное приложение

Следующее консольное приложение выполняет предыдущий код:

```cs
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.SpellCheck;

namespace SpellCheckSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new SpellCheckAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var result = client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof", acceptLanguage: "en-US").Result;
                Console.WriteLine("Correction for Query# \"bill gatas\"");

                // SpellCheck Results
                if (result?.Body.FlaggedTokens?.Count > 0)
                {
                    // find the first spellcheck result
                    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();

                    if (firstspellCheckResult != null)
                    {
                        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
                        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
                        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
                        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);

                        var suggestions = firstspellCheckResult.Suggestions;
                        if (suggestions?.Count > 0)
                        {
                            var firstSuggestion = suggestions.FirstOrDefault();
                            Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                            Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
                        }
                    }
                    else
                    {
                        Console.WriteLine("Couldn't get any Spell check results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any SpellCheck results..");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

        // This will trigger an error response from the API.
        public static void SpellCheckError(string subscriptionKey)
        {
            var client = new SpellCheckAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                var result = client.SpellCheckerAsync(mode: "proof").Result;
                Console.WriteLine("Correction for Query# \"empty text field\"");
            }
            catch (Exception ex)
            {
                if (ex.GetBaseException().GetType() == typeof(Exception) )
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }
            }
        }
    }
}

```

## <a name="next-steps"></a>Дополнительная информация

[Примеры пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
