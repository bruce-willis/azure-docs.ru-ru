---
title: Краткое руководство по пакету SDK для поиска новостей для C# | Документация Майкрософт
description: Установка консольного приложения пакета SDK для поиска новостей.
titleSuffix: Azure cognitive services News search SDK C# quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 01/30/2018
ms.author: v-gedod
ms.openlocfilehash: e803fd579c6b71b8b1754546446715795a12087a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382608"
---
# <a name="news-search-sdk-c-quickstart"></a>Краткое руководство по пакету SDK для поиска новостей для C#

Пакет SDK Bing для поиска новостей содержит функции REST API для обработки запросов новостей и анализа результатов. 

[Исходный код примеров для пакета SDK Bing для поиска новостей для C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Зависимости приложения

Чтобы настроить консольное приложение с помощью пакета SDK Bing для поиска новостей, в обозревателе решений Visual Studio перейдите к параметру `Manage NuGet Packages`.  Добавьте пакет `Microsoft.Azure.CognitiveServices.Search.NewsSearch`.

При установке [пакета NuGet SDK для поиска новостей](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0) также устанавливаются зависимости, включая следующие:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="news-search-client"></a>Клиент для поиска новостей
Чтобы создать экземпляр клиента `NewsSearchAPI`, добавьте директиву using:
```
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

```
Затем создайте экземпляр клиента:
```
var client = new NewsSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Используйте клиент для выполнения поиска с таким текстом запроса:
```
var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

```
Проанализируйте новости, возвращаемые в результатах предыдущего запроса:
```
if (newsResults.Value.Count > 0)
{
    var firstNewsResult = newsResults.Value.First();

    Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
    Console.WriteLine($"News result count: {newsResults.Value.Count}");
    Console.WriteLine($"First news name: {firstNewsResult.Name}");
    Console.WriteLine($"First news url: {firstNewsResult.Url}");
    Console.WriteLine($"First news description: {firstNewsResult.Description}");
    Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
    Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
}

else
{
    Console.WriteLine("Couldn't find news results!");
}

```
## <a name="complete-console-application"></a>Готовое консольное приложение

Следующее консольное приложение выполняет ранее определенный запрос и выполняет поиск новостей для поискового запроса "Quantum Computing" (Квантовые вычисления). Запрос включает в себя параметры `market` и `count`. Код проверяет число результатов и выводит значения параметров `totalEstimatedMatches`, `name`, `url`, `description`, `published time` и имя (`name`) поставщика (`provider`) первого результата из списка новостей.

```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

namespace NewsSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new NewsSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
                Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these methods to run queries explained under headings following.
            // NewsSearchWithFilters(client);
            // NewsCategory(client);
            // TrendingTopics(client);

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}

```
## <a name="recent-news-freshness-and-sortby-parameters"></a>Последние новости, параметры freshness и sortBy
Следующий код выполняет поиск последних новостей для поискового запроса "Artificial Intelligence" (Искусственный интеллект) с параметрами `freshness` и `sortBy`. Он проверяет число результатов и выводит значения параметров `totalEstimatedMatches`, `name`, `url`, `description`, `published time` и имя (`name`) поставщика первого результата из списка новостей.
```
        public static void NewsSearchWithFilters(NewsSearchAPI client)
        {
            try
            {
                var newsResults = client.News.SearchAsync(query: "Artificial Intelligence", market: "en-us", freshness: "Week", sortBy: "Date").Result;
                Console.WriteLine("Search most recent news for query \"Artificial Intelligence\" with freshness and sortBy");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nTotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="category-news-safe-search"></a>Категории новостей, безопасный поиск
Следующий код выполняет поиск по категориям новостей для фильмов и телепрограмм, используя безопасный поиск.  Он проверяет число результатов и выводит значения параметров `category`, `name`, `url`, `description`, `published time` и имя (`name`) поставщика первого результата из списка новостей.
```
        public static void NewsCategory(NewsSearchAPI client)
        {
            try
            {
                var newsResults = client.News.CategoryAsync(category: "Entertainment_MovieAndTV", market: "en-us", safeSearch: "strict").Result;
                Console.WriteLine("Search category news for movie and TV entertainment with safe search");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nNews result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news category: {firstNewsResult.Category}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="trending-topics"></a>Темы, набирающие популярность
Следующий код выполняет поиск по новостным темам, набирающим популярность в Bing. Он проверяет число результатов и выводит значения параметров `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` и `image.Url` первого результата из списка новостей.
```
        public static void TrendingTopics(NewsSearchAPI client)
        {
            try
            {
                var trendingTopics = client.News.TrendingAsync(market: "en-us").Result;
                Console.WriteLine("Search news trending topics in Bing");

                if (trendingTopics == null)
                {
                    Console.WriteLine("Didn't see any news trending topics..");
                }
                else
                {
                    if (trendingTopics.Value.Count > 0)
                    {
                        var firstTopic = trendingTopics.Value.First();

                        Console.WriteLine($"\r\nTrending topics count: {trendingTopics.Value.Count}");
                        Console.WriteLine($"First topic name: {firstTopic.Name}");
                        Console.WriteLine($"First topic query: {firstTopic.Query.Text}");
                        Console.WriteLine($"First topic image url: {firstTopic.Image.Url}");
                        Console.WriteLine($"First topic webSearchUrl: {firstTopic.WebSearchUrl}");
                        Console.WriteLine($"First topic newsSearchUrl: {firstTopic.NewsSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news trending topics!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)