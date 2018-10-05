---
title: 'Руководство: ImageInsightsToken — Визуальный поиск Bing'
titlesuffix: Azure Cognitive Services
description: Сведения об использовании пакета SDK для визуального поиска Bing с целью получения URL-адресов изображений, указанных токеном ImageInsightsToken.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: tutorial
ms.date: 06/21/2018
ms.author: rosh
ms.openlocfilehash: bda4bdeea019d8cf3ae677d5eaf81e631ca38d16
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222579"
---
# <a name="tutorial-bing-visual-search-sdk-imageinsightstoken-and-results"></a>Руководство по ImageInsightsToken в пакете SDK для визуального поиска Bing и результаты применения
В пакет SDK для визуального поиска входит возможность поиска изображений в Интернете из предыдущей операции поиска, возвращающей `ImageInsightsToken`.  В этом примере показано получение `ImageInsightsToken` и использование токена в последующих операциях поиска.  Код отправляет `ImageInsightsToken` в Bing и возвращает результаты, содержащие URL-адреса поиска Bing и URL-адреса схожих изображений, найденных в Интернете.

## <a name="prerequisites"></a>Предварительные требования
Visual Studio 2017. При необходимости можно скачать бесплатную версию сообщества. Ее можно найти по адресу https://www.visualstudio.com/vs/community/.
Для проверки подлинности вызовов пакета SDК требуется ключ API Cognitive Services. Зарегистрируйтесь для получения ключа бесплатной пробной версии. Ключ бесплатной пробной версии действует в течение семи дней с одним вызовом в секунду. Для производственной эксплуатации следует приобрести ключ доступа. Ознакомьтесь также со сведениями о ценах.
Поддерживается возможность запуска пакета SDK для .NET Core, приложений .NET Core 1.1. Загрузить .NET Core, Framework и среду выполнения можно по адресу https://www.microsoft.com/net/download/.

##<a name="application-dependencies"></a>Зависимости приложения
Чтобы настроить консольное приложение с помощью пакета SDK для поиска в Интернете Bing, в Visual Studio в обозревателе решений перейдите к параметру "Управление пакетами NuGet". Добавьте следующие пакеты:
* Microsoft.Azure.CognitiveServices.Search.VisualSearch;
* Microsoft.Azure.CognitiveServices.Search.ImageSearchpackage.

При установке пакета NuGet SDK для поиска в Интернете также устанавливаются зависимости, включая следующие:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json.

## <a name="get-the-imageinsightstoken-from-image-search"></a>Получение токена ImageInsightsToken из поиска изображений
В этом примере используется токен `ImageInsightsToken`, полученный с помощью следующего метода.  Дополнительные сведения об этом вызове см. в статье [Краткое руководство по пакету SDK для поиска изображений для C#](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

Код выполняет поиск результатов по запросу "Canadian Rockies" и получает токен ImageInsightsToken. Он выводит первый токен аналитики изображения, URL-адрес эскиза и URL-адрес содержимого изображения.  Метод возвращает `ImageInsightsToken` для использования в последующих запросах визуального поиска.

```
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }
```

## <a name="specify-the-imageinsightstoken-for-visual-search-request"></a>Указание токена ImageInsightsToken для запроса визуального поиска
В этом примере используется токен аналитики, возвращенный предыдущим методом. Следующий код создает объект `ImageInfo` из токена `ImageInsightsToken` и загружает объект ImageInfo в `VisualSearchRequest`. Укажите токен `ImageInsightsToken` в `ImageInfo` для `VisualSearchRequest`.

```
ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);
```
## <a name="use-visual-search-to-find-images-from-an-imageinsightstoken"></a>Использование визуального поиска для обнаружения изображений из токена ImageInsightsToken
`VisualSearchRequest` содержит сведения об изображении, поиск которого выполняется в объекте `ImageInfo`.  Метод `VisualSearchMethodAsync` получает результаты.
```
// An image binary is not necessary here, as the image is specified by insights token.
VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
Console.WriteLine("\r\nVisual search request with knowledgeRequest");

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Получение данных URL-адреса из ImageModuleAction
Результатами визуального поиска являются объекты `ImageTag`.  Каждый тег содержит список объектов `ImageAction`.  Каждый `ImageAction` содержит поле `Data`, которое представляет собой список значений, зависящих от типа действия:

С помощью следующего кода можно получить различные типы:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Готовое приложение возвращает:

* ActionType: MoreSizes -> WebSearchUrl:
* ActionType: VisualSearch -> WebSearchUrl:
* ActionType: ImageById -> WebSearchUrl:
* ActionType: RelatedSearches -> WebSearchUrl:
* ActionType: DocumentLevelSuggestions -> WebSearchUrl:
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=BcQifmzdKFyyBusjLxxgO42kzq1Geh7RucVVqvH-900&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fcanadian%2brocky&p=DevEx,5823.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=3E32CC6CA5934FBBA14ABC3B2E4651F9&CID=1BA795A21EAF6A63175699B71FC36B7C&rd=1&h=PV9GzMFOI0AHZp2gKeWJ8DcveSDRE3fP2jHDKMpJSU8&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3doutdoor&p=DevEx,5831.1

Как показано в предыдущем списке, типы `TopicResults` и `ImageResults` содержат запросы для связанных изображений. URL-адреса в списке ведут на результаты поиска Bing.


## <a name="pagesincluding-actiontype-urls-of-images-found-by-visual-search"></a>URL-адреса ActionType PagesIncluding изображений, обнаруженных визуальным поиском

Для получения URL-адресов фактических изображений требуется приведение к типу, который считывает `ActionType` как `ImageModuleAction`, содержащий элемент `Data` со списком значений.  Каждое значение является URL-адресом изображения.  Далее показано приведение типа действия `PagesIncluding` к `ImageModuleAction` и считывание значений.
```
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```
Дополнительные сведения об этих типах данных см. в статье [Images - Visual Search](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) (Изображения — визуальный поиск).
## <a name="complete-code"></a>Полный код

Далее приводится код для выполнения предыдущих примеров. Он отправляет `ImageInsightsToken` в запросе POST. Затем он выводит URL-адреса поиска Bing для каждого ActionType. Если ActionType имеет значение `PagesIncluding`, код получает элементы `ImageObject` в `Data`.  `Data` содержит список значений, которые являются URL-адресами изображений на веб-страницах.  Скопируйте и вставьте полученные URL-адреса визуального поиска в браузер, чтобы показать результаты. Скопируйте и вставьте элементы ContentUrl в браузер, чтобы показать изображения.

```
using System;
using System.IO;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch;
using Microsoft.Azure.CognitiveServices.Search.VisualSearch.Models;
using Microsoft.Azure.CognitiveServices.Search.ImageSearch;

namespace VisualSearchFeatures
{
    class Program
    {
        static void Main(string[] args)
        {
            String subscriptionKey = "YOUR-ACCESS-KEY";

            insightsToken = ImageResults(subscriptionKey);

            VisualSearchInsightsToken(subscriptionKey, insightsToken);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        // Searches for results on query (Canadian Rockies) and gets an ImageInsightsToken.
        // Also prints first image insights token, thumbnail url, and image content url.
        private static String ImageResults(String subKey)
        {
            String insightTok = "None";
            try
            {
                var client = new ImageSearchAPI(new Microsoft.Azure.CognitiveServices.Search.ImageSearch.ApiKeyServiceClientCredentials(subKey)); //
                var imageResults = client.Images.SearchAsync(query: "canadian rockies").Result;
                Console.WriteLine("Search images for query \"canadian rockies\"");

                if (imageResults == null)
                {
                    Console.WriteLine("No image result data.");
                }
                else
                {
                    // Image results
                    if (imageResults.Value.Count > 0)
                    {
                        var firstImageResult = imageResults.Value.First();

                        Console.WriteLine($"\r\nImage result count: {imageResults.Value.Count}");
                        insightTok = firstImageResult.ImageInsightsToken;
                        Console.WriteLine($"First image insights token: {firstImageResult.ImageInsightsToken}");  
                        Console.WriteLine($"First image thumbnail url: {firstImageResult.ThumbnailUrl}");
                        Console.WriteLine($"First image content url: {firstImageResult.ContentUrl}");
                    }
                    else
                    {
                        insightTok = "None found";
                        Console.WriteLine("Couldn't find image results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("\r\nEncountered exception. " + ex.Message);
            }

            return insightTok;
        }


        // This method will get Visual Search results from an imageInsightsToken obtained from the return value of the ImageResults method.
        // The method includes imageInsightsToken the in a knowledgeRequest parameter, along with a cropArea object. 
        // It prints out the imageInsightsToken uploaded in the request.
        // Finally the example prints URLs of images found using the imageInsightsToken.

        public static void VisualSearchInsightsToken(string subscriptionKey, string insightsTok)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                // The image can be specified via an insights token, in the ImageInfo object.
                ImageInfo ImageInfo = new ImageInfo(imageInsightsToken: insightsTok);

                // An image binary is not necessary here, as the image is specified by insights token.
                VisualSearchRequest VisualSearchRequest = new VisualSearchRequest(ImageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: VisualSearchRequest).Result;
                Console.WriteLine("\r\nVisual search request with imageInsightsToken and knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // Visual Search results
                    if (visualSearchResults.Image?.ImageInsightsToken != null)
                    {
                        Console.WriteLine($"Uploaded image insights token: {insightsTok}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find image insights token!");
                    }

                    if (visualSearchResults.Tags.Count > 0)
                    {
                        // List of tags
                        foreach (ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            {
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " WebSearchURL: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach (ImageObject o in (i as ImageModuleAction).Data.Value)
                                    {
                                        Console.WriteLine("ContentURL: " + o.ContentUrl);
                                    }
                                }
                            }
                        }
                    }

                    else
                    {
                        Console.WriteLine("Couldn't find image tags!");
                    }

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
## <a name="next-steps"></a>Дополнительная информация
[Ответ визуального поиска](https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/overview#the-response)
