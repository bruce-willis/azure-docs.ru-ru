---
title: Краткое руководство. Использование пакета SDK Поиска в Интернете Bing для C#
description: Сведения об использовании пакета SDK Поиска в Интернете Bing для C#.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: ed958f4a691b878cfa7ff9766a0fb72857cce5db
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43186802"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Краткое руководство. Использование пакета SDK Поиска в Интернете Bing для C#

Пакет SDK Поиска в Интернете Bing позволяет интегрировать поиск Bing в любое приложение C#. Из этого краткого руководства вы узнаете, как создать экземпляр клиента, отправить запрос и вывести ответ.

Хотите увидеть код прямо сейчас? [Примеры использования пакета SDK Поиска в Интернете Bing для C#](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/) доступны в GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить это руководство, вам потребуется следующее:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) или
* [Visual Studio Code 2017](https://code.visualstudio.com/download);
  * [C# для Visual Studio Code](https://visualstudio.microsoft.com/downloads/);
  * [диспетчер пакетов NuGet](https://github.com/jmrog/vscode-nuget-package-manager);
* [пакет SDK для .NET Core](https://www.microsoft.com/net/download).

## <a name="create-a-project-and-install-dependencies"></a>Создание проекта и установка зависимостей

Первым шагом является создание проекта консольного приложения. Если вам требуется помощь по настройке проекта консольного приложения, см. [руководство по созданию программы Hello World на C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). Чтобы использовать в приложении SDK Поиска в Интернете Bing, необходимо установить `Microsoft.Azure.CognitiveServices.Search.WebSearch` с помощью диспетчера пакетов NuGet.

[Пакет SDK Поиска в Интернете](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) также устанавливает следующее:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json.

## <a name="declare-dependencies"></a>Объявление зависимостей

Откройте проект в Visual Studio или Visual Studio Code и импортируйте эти зависимости:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>Создание шаблонов для проекта

Для создаваемого проекта консольного приложения также нужно создать пространство имен и класс. Программа должна выглядеть так:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

В следующих разделах мы создадим в этом классе пример приложения.

## <a name="construct-a-request"></a>Создание запроса

Этот код создает поисковый запрос.

```csharp
public static void WebResults(WebSearchAPI client)
{
    try
    {
        var webData = client.Web.Search(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Обработка ответа

Теперь мы добавим код, который анализирует ответ и выводит результаты. Выводятся `name` и `url` для первых результатов в категориях веб-страниц, изображений, новостей и видео, если они присутствуют в объекте ответа.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Объявление метода main

В этом приложении метод main включает код, который создает экземпляр клиента, проверяет `subscriptionKey` и вызывает `WebResults`. Не забудьте ввести действующий ключ подписки Azure, прежде чем продолжить работу.

```csharp
static void Main(string[] args)
{
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Выполнение приложения

Теперь давайте запустим приложение.

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Определение функций и фильтрация результатов

Теперь, когда вы выполнили первый вызов к API Bing для Поиска в Интернете, давайте изучим несколько функций, которые демонстрируют возможности пакета SDK по уточнению запросов и фильтрации результатов. Каждую из этих функций можно добавить в приложение C#, которое вы создали в предыдущем разделе.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Ограничение числа результатов, возвращаемых Bing

В этом примере используются параметры `count` и `offset`, которые позволяют ограничить число результатов, возвращаемых поиском по запросу Best restaurants in Seattle (Лучшие рестораны в Сиэтле). Для первого результата возвращаются `name` и `URL`.

1. Добавьте следующий код в проект консольного приложения:
    ```csharp
    public static void WebResultsWithCountAndOffset(WebSearchAPI client)
    {
        try
        {
            var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
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
2. Добавьте `WebResultsWithCountAndOffset` в `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Запустите приложение.

### <a name="filter-for-news"></a>Фильтрация новостей

В этом примере используется параметр `response_filter` для фильтрации результатов поиска. В результатах поиска возвращаются только новости по запросу Microsoft. Для первого результата возвращаются `name` и `URL`.

1. Добавьте следующий код в проект консольного приложения:
    ```csharp
    public static void WebSearchWithResponseFilter(WebSearchAPI client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
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
2. Добавьте `WebResultsWithCountAndOffset` в `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Запустите приложение.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Использование безопасного поиска, счетчика ответов и фильтра повышения уровня

В этом примере используются параметры `answer_count`, `promote` и `safe_search` для фильтрации результатов поиска по запросу Music Videos (Музыкальные видео). Для первого результата возвращаются `name` и `URL`.

1. Добавьте следующий код в проект консольного приложения:
    ```csharp
    public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
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
    ```
2. Добавьте `WebResultsWithCountAndOffset` в `main`:
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Запустите приложение.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ключ подписки из кода приложения, когда завершите работу с этим проектом.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры использования пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
