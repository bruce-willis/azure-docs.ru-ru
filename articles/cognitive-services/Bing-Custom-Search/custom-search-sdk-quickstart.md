---
title: Краткое руководство по пакету SDK для пользовательского поиска для C# | Документация Майкрософт
titleSuffix: Cognitive Services
description: Установка компонентов консольного приложения пакета SDK для пользовательского поиска для C#.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380040"
---
# <a name="custom-search-sdk-c-quickstart"></a>Краткое руководство по пакету SDK для пользовательского поиска для C#

Пакет SDK Bing для пользовательского поиска содержит функции REST API для поиска сущностей и анализа результатов.

## <a name="application-dependencies"></a>Установка зависимостей

Чтобы настроить консольное приложение с помощью пакета SDK Bing для пользовательского поиска, в обозревателе решений Visual Studio перейдите к параметру `Manage NuGet Packages`. Добавьте пакет `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

При установке [пакета NuGet для пользовательского поиска](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) также будут установлены зависимости, включая следующие сборки:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Клиент API для поиска сущностей

Чтобы создать экземпляр клиента CustomSearchAPI, добавьте директивы using:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Создайте экземпляр клиента для поиска сущностей: замените значения `YOUR-CUSTOM-SEARCH-KEY` и `YOUR-CUSTOM-CONFIG-ID` своим ключом доступа и своим идентификатором конфигурации конечной точки API, назначенными для [ваших экземпляров](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Используйте клиент для выполнения поиска с таким текстом запроса:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Анализ результатов

Метод `SearchAsync` возвращает объект `WebData`, содержащий `WebPages` (веб-страницы), если они найдены для данного запроса. Этот код находит первый результат и получает его значения `Name` и `URL`.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

 //WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
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

```
## <a name="complete-console-application"></a>Готовое консольное приложение

Следующий код выполняет поиск в Интернете по запросу "Xbox" и выводит значения `Name` и `URL` для первого результата.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

            try
            {
                // This will look up a single query (Xbox).
                var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                Console.WriteLine("Searched for Query# \" Xbox \"");

                //WebPages
                if (webData?.WebPages?.Value?.Count > 0)
                {
                    // find the first web page
                    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                    if (firstWebPagesResult != null)
                    {
                        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
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
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK для .NET в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
