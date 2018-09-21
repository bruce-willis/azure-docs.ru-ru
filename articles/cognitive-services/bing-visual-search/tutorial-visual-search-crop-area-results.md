---
title: Руководство по выбору области обрезки в пакете SDK для визуального поиска Bing и результаты применения | Документы Майкрософт
description: Сведения об использовании пакета SDK для визуального поиска Bing с целью получения URL-адресов изображений, аналогичных области обрезки отправленного изображения.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: article
ms.date: 06/20/2018
ms.author: rosh
ms.openlocfilehash: dd51ed7c710cc51a9fe0e63e55aa0d2c4ea24bee
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574495"
---
# <a name="tutorial-bing-visual-search-sdk-image-crop-area-and-results"></a>Руководство по выбору области обрезки для изображения в пакете SDK для визуального поиска Bing и результаты применения
В пакет SDK для визуального поиска входит возможность выбора области изображения и поиска изображений в Интернете, аналогичных области обрезки более крупного изображения.  В этом примере указывается область обрезки, на которой показан один человек с изображения, содержащего несколько человек.  Код отправляет область обрезки и URL-адрес более крупного изображения и возвращает результаты, содержащие URL-адреса поиска Bing и URL-адреса схожих изображений, найденных в Интернете.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода на компьютерах под управлением Windows потребуется [Visual Studio 2017](https://www.visualstudio.com/downloads/). (Будет работать бесплатный выпуск Community Edition.)

Необходима [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с API-интерфейсами поиска Bing. Для данного краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Требуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure.

## <a name="application-dependencies"></a>Зависимости приложения
Чтобы настроить консольное приложение с помощью пакета SDK для поиска в Интернете Bing, в Visual Studio в обозревателе решений перейдите к параметру "Управление пакетами NuGet". Добавьте пакет Microsoft.Azure.CognitiveServices.Search.VisualSearch.

При установке пакета NuGet SDK для поиска в Интернете также устанавливаются зависимости, включая следующие:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json.

## <a name="image-and-crop-area"></a>Изображение и область обрезки
На следующем изображении показана группа представителей высшего руководства корпорации Майкрософт.  Используя пакет SDK для визуального поиска, мы отправляем область обрезки изображения и находим другие изображения и веб-страницы, содержащие сущность в выделенной области более крупного изображения.  В этом случае сущностью является человек.

![Группа представителей высшего руководства корпорации Майкрософт](./media/MS_SrLeaders.jpg)

## <a name="specify-the-crop-area-as-imageinfo-in-visualsearchrequest"></a>Указание области обрезки как ImageInfo в VisualSearchRequest
В этом примере используется область обрезки предыдущего изображения, которая указывает верхнюю левую и нижнюю правую координаты в процентах от всего изображения.  Следующий код создает объект `ImageInfo` из области обрезки и загружает объект `ImageInfo` в `VisualSearchRequest`.  Объект `ImageInfo` также содержит URL-адрес изображения в Интернете.

```
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg;
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```
## <a name="search-for-images-similar-to-crop-area"></a>Поиск изображений, аналогичных области обрезки
`VisualSearchRequest` содержит сведения области обрезки об изображении и его URL-адрес.  Метод `VisualSearchMethodAsync` получает результаты.
```
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Получение данных URL-адреса из ImageModuleAction
Результатами визуального поиска являются объекты `ImageTag`.  Каждый тег содержит список объектов `ImageAction`.  Каждый `ImageAction` содержит поле `Data`, которое представляет собой список значений, зависящих от типа действия:

С помощью следующего кода можно получить различные типы:
```
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

```
Готовое приложение возвращает:

* ActionType: PagesIncluding WebSearchURL:
* ActionType: MoreSizes WebSearchURL:
* ActionType: VisualSearch WebSearchURL:
* ActionType: ImageById WebSearchURL: 
* ActionType: RelatedSearches  WebSearchURL:
* ActionType: Entity -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v=1&r=https%3a%2f%2fwww.bing.com%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5380.1
* ActionType: TopicResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v=1&r=https%3a%2f%2fwww.bing.com%2fdiscover%2fnadella%2bsatya&p=DevEx,5382.1
* ActionType: ImageResults -> WebSearchUrl: https://www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID=03DCF882D7386A442137F49BD6596BEF&rd=1&h=l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v=1&r=https%3a%2f%2fwww.bing.com%2fimages%2fsearch%3fq%3dSatya%2bNadella&p=DevEx,5384.1

Как показано в предыдущем списке `Entity` `ActionType` содержит запрос поиска Bing, который возвращает сведения о распознаваемом человеке, месте или предмете.  Типы `TopicResults` и `ImageResults` содержат запросы для связанных изображений. URL-адреса в списке ведут на результаты поиска Bing.


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

## <a name="complete-code"></a>Полный код

Далее приводится код для выполнения предыдущих примеров. Он отправляет двоичный файл изображения в тексте запроса POST вместе с объектом cropArea и выводит URL-адреса Bing для каждого типа действия. Если тип действия — PagesIncluding, код получает элементы ImageObject в ImageObject Data.  Data содержит список значений, которые являются URL-адресами изображений на веб-страницах.  Скопируйте и вставьте полученные URL-адреса визуального поиска в браузер, чтобы показать результаты. Скопируйте и вставьте элементы ContentUrl в браузер, чтобы показать изображения.

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

            VisualSearchImageBinaryWithCropArea(subscriptionKey);

            Console.WriteLine("Any key to quit...");
            Console.ReadKey();

        }

        public static void VisualSearchImageBinaryWithCropArea(string subscriptionKey)
        {
            var client = new VisualSearchAPI(new Microsoft.Azure.CognitiveServices.Search.VisualSearch.ApiKeyServiceClientCredentials(subscriptionKey));

            try
            {
                CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
                
                // The ImageInfo struct specifies the crop area in the image and the URL of the larger image. 
                string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
                ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);
                
                VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);

                var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result; 

                Console.WriteLine("\r\nVisual search request with image from URL and crop area combined in knowledgeRequest");

                if (visualSearchResults == null)
                {
                    Console.WriteLine("No visual search result data.");
                }
                else
                {
                    // List of tags
                    if (visualSearchResults.Tags.Count > 0)
                    {

                        foreach(ImageTag t in visualSearchResults.Tags)
                        {
                            foreach (ImageAction i in t.Actions)
                            { 
                                Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);

                                if (i.ActionType == "PagesIncluding")
                                {
                                    foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
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