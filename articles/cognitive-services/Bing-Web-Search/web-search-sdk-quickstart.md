---
title: Краткое руководство по пакету SDK для API "Поиск в Интернете" для C# | Документация Майкрософт
description: Установка компонентов консольного приложения пакета SDK для поиска в Интернете на C#.
titleSuffix: Azure cognitive services Web search SDK C# quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 01/29/2018
ms.author: v-gedod
ms.openlocfilehash: 6d87b292475edff04e930ec4aa2f8e077a0fb82c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382625"
---
# <a name="web-search-sdk-c-quickstart"></a>Краткое руководство по пакету SDK для API "Поиск в Интернете" для C#

Пакет SDK для API "Поиск в Интернете Bing" содержит функции REST API для обработки веб-запросов и анализа результатов.

[Исходный код примеров для пакета SDK для API "Поиск в Интернете Bing" для C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/BingSearchv7/BingWebSearch/WebSearchSamples.cs) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Зависимости приложения

Чтобы настроить консольное приложение с помощью пакета SDK для API "Поиск в Интернете Bing", в обозревателе решений Visual Studio перейдите к параметру `Manage NuGet Packages`.  Добавьте пакет `Microsoft.Azure.CognitiveServices.Search.WebSearch`.

При установке [пакета SDK NuGet для API "Поиск в Интернете"](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) также будут установлены следующие зависимости:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json.

## <a name="web-search-client"></a>Клиент для API "Поиск в Интернете"
Чтобы создать экземпляр клиента `WebSearchAPI`, добавьте директивы using:
```
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;

```
Затем создайте экземпляр клиента:
```
var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Используйте клиент для выполнения поиска с таким текстом запроса:
```
// Search for "Yosemite National Park"
var webData = client.Web.Search(query: "Yosemite National Park");
Console.WriteLine("Searched for Query# \" Yosemite National Park \"");

```
Проанализируйте веб-страницы, возвращаемые в результатах предыдущего запроса:
```
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results #{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!)";
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```
## <a name="complete-console-application"></a>Готовое консольное приложение

Следующее консольное приложение выполняет ранее определенный запрос и анализирует веб-страницы, изображения, новости и видео, содержащиеся в результатах:
```
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;

namespace WebSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            WebResults(client);
            // Include the following calls to use queries defined under following headings.
            //WebResultsWithCountAndOffset(client);  
            //WebSearchWithResponseFilter(client);
            //WebSearchWithAnswerCountPromoteAndSafeSearch(client);

            Console.WriteLine("Any key to exit... ");
            Console.ReadKey();
        }

        public static void WebResults(WebSearchAPI client)
        {
            try
            {
                // Search for (Yosemite National Park"), print the number of results and print out name and url of first result.
                var webData = client.Web.Search(query: "Yosemite National Park");
                Console.WriteLine("Searched for Query# \" Yosemite National Park \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results #{0}", webData.WebPages.Value.Count);
                        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find web results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any Web data..");
                }

                //Images
                if (webData?.Images?.Value?.Count > 0)
                {
                    // find the first image result
                    var firstImageResult = webData.Images.Value.FirstOrDefault();

                    if (firstImageResult != null)
                    {
                        Console.WriteLine("Image Results #{0}", webData.Images.Value.Count);
                        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
                        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first image results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any image data..");
                }

                //News
                if (webData?.News?.Value?.Count > 0)
                {
                    // find the first news result
                    var firstNewsResult = webData.News.Value.FirstOrDefault();

                    if (firstNewsResult != null)
                    {
                        Console.WriteLine("\r\nNews Results #{0}", webData.News.Value.Count);
                        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find any News results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see first news data..");
                }

                //Videos
                if (webData?.Videos?.Value?.Count > 0)
                {
                    // find the first video result
                    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

                    if (firstVideoResult != null)
                    {
                        Console.WriteLine("\r\nVideo Results #{0}", webData.Videos.Value.Count);
                        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
                        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first video results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any video data..");
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

        }
    }
}

```

В примерах поиска Bing демонстрируются различные функции пакета SDK.  Добавьте следующие функции в ранее определенный класс `WebSrchSDK`.

## <a name="count-and-offset-parameters"></a>Параметры count и offset

Следующий код выполняет поиск по запросу "Best restaurants in Seattle" (Лучшие рестораны в Сиэтле), проверяет количество результатов и выводит имя и URL-адрес первого результата.

```
       public static void WebResultsWithCountAndOffset(WebSearchAPI client)
        {
            try
            {
                var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
                Console.WriteLine("\r\nSearched for Query# \" Best restaurants in Seattle \"");

                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first web result!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any Web data..");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="response-filter"></a>Фильтр ответов

Следующий запрос выполняет поиск по термину "Microsoft" с использованием фильтра ответа, для которого задано значение `news`, а затем выводит подробные сведения о результатах.
```
        public static void WebSearchWithResponseFilter(WebSearchAPI client)
        {
            //var client = new WebSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                IList<string> responseFilterstrings = new List<string>() { "news" };
                var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
                Console.WriteLine("\r\nSearched for Query# \" Microsoft \" with response filters \"news\"");

                //News
                if (webData?.News?.Value?.Count > 0)
                {
                    // find the first news result
                    var firstNewsResult = webData.News.Value.FirstOrDefault();

                    if (firstNewsResult != null)
                    {
                        Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find first News results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any News data..");
                }

            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="query-parameters---count-promotion-safe-search"></a>Параметры запроса count, promotion и safe search

Для этого запроса — "Lady Gaga" (Леди Гага) — выполняется поиск с использованием параметров `answerCount` и `promote`, а затем выводятся подробные сведения о результатах.

```
        public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
        {
            //var client = new WebSearchAPI(new ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
                var webData = client.Web.SearchAsync(query: "Lady Gaga", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
                Console.WriteLine("\r\nSearched for Query# \" Lady Gaga \"");

                if (webData?.Videos?.Value?.Count > 0)
                {
                    var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                    if (firstVideosResult != null)
                    {
                        Console.WriteLine("Video Results #{0}", webData.Videos.Value.Count);
                        Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                        Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find videos results!");
                    }
                }
                else
                {
                    Console.WriteLine("Didn't see any data..");
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }
    }
```

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).