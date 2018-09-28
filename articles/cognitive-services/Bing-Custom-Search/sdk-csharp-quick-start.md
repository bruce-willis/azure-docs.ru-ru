---
title: Краткое руководство по пакету SDK для пользовательского поиска для C# | Документация Майкрософт
titleSuffix: Cognitive Services
description: Установка компонентов консольного приложения пакета SDK для пользовательского поиска для C#.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6c9917e3a63515f36b386e444edcc53de07799fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949933"
---
# <a name="c-sdk-quickstart"></a>Краткое руководство по пакету SDK для C#

Пакет SDK для API пользовательского поиска Bing предоставляет более простую модель программирования, чем REST API пользовательского поиска Bing. Здесь описывается, как выполнить первые вызовы службы пользовательского поиска с помощью пакета SDK для C#.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

- Готовый экземпляр службы пользовательского поиска. Ознакомьтесь с разделом [Create your first Bing Custom Search instance](quick-start.md) (Создание первого экземпляра службы "Пользовательский поиск Bing").  
  
- ключ подписки; Ключ подписки можно получить при активации [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). Кроме того, вы можете использовать ключ платной подписки из панели мониторинга Azure (см. раздел об [учетной записи API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Установленная среда Visual Studio 2017. Вы можете скачать **бесплатную** [среду Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/), если у вас ее еще нет.  
  
- Установленный пакет [NuGet для пользовательского поиска](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0). В обозревателе решений в Visual Studio щелкните правой кнопкой мыши проект и выберите `Manage NuGet Packages` в меню. Установите пакет `Microsoft.Azure.CognitiveServices.Search.CustomSearch`.

При установке пакета NuGet для пользовательского поиска также будут установлены следующие сборки:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json.



## <a name="run-the-code"></a>Выполнение кода

Чтобы запустить этот пример, выполните следующее.

1. Откройте Visual Studio 2017.
  
2. Щелкните меню **Файл**, выберите **Создать**, **Проект**, а затем — шаблон **Visual C# Console Application** (Консольное приложение Visual C#).
  
3. Назовите решение CustomSearchSolution и перейдите в папку, в которую его нужно поместить.
  
4. Нажмите кнопку "ОК", чтобы создать решение.  
  
4. В обозревателе решений щелкните правой кнопкой мыши проект (его имя совпадает с именем решения) и выберите `Manage NuGet Packages`. В диспетчере пакетов NuGet щелкните **Обзор**. В поле поиска введите Microsoft.Azure.CognitiveServices.Search.CustomSearch и нажмите клавишу ВВОД. Выберите пакет и нажмите "Установить".  
  
4. Скопируйте следующий код в файл Program.cs. Замените **YOUR-SUBSCRIPTION-KEY** и **YOUR-CUSTOM-CONFIG-ID** своим ключом подписки и идентификатором конфигурации.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

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
                            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
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

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. Выполните сборку и запуск (отладку) решения. 




## <a name="breaking-it-down"></a>Выполнение запроса

После установки пакета NuGet для пользовательского поиска необходимо добавить для него директиву using.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Затем создайте экземпляр клиента пользовательского поиска, который используется для выполнения поисковых запросов. Обязательно замените `YOUR-SUBSCRIPTION-KEY` на ключ.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Теперь вы можете использовать клиент для отправки поискового запроса. Замените `YOUR-CUSTOM-CONFIG-ID` идентификатором конфигурации вашего экземпляра (идентификатор можно узнать на [портале пользовательского поиска](https://www.customsearch.ai/)). В этом примере выполняется поиск Xbox. Обновите его при необходимости.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

Метод `SearchAsync` возвращает объект `WebData`. Используйте `WebData` для выполнения итерации по любым найденным экземплярам `WebPages`. Этот код находит первую соответствующую веб-страницу и выводит ее `Name` и `URL`.

```csharp
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


## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами для пакета SDK. Каждый пример содержит файл сведений, в котором указаны предварительные требования и инструкции по установке и запуску этих примеров.

* Начало работы с [примерами для .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7). 
    * [Пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0).
    * Ознакомьтесь также с определениями и зависимостями в [библиотеках .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch).
* Начало работы с [примерами для NodeJS](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples). 
    * Ознакомьтесь также с определениями и зависимостями в [библиотеках NodeJS](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch).
* Начало работы с [примерами для Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples). 
    * Ознакомьтесь также с определениями и зависимостями в [библиотеках Java](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch).
* Начало работы с [примерами для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples). 
    * Ознакомьтесь также с определениями и зависимостями в [библиотеках Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch).

