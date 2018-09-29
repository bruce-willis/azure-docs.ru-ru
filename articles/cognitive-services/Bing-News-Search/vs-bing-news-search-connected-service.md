---
title: Руководство по службе "Поиск новостей Bing" | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Подключение к службе "Поиск новостей Bing" в Cognitive Services из веб-приложения ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: ghogen
ms.openlocfilehash: 5cfa82067d28b05f32bd87e0e83d55a03da8d508
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095435"
---
# <a name="connect-to-bing-news-search-api-by-using-connected-services-in-visual-studio"></a>Подключение к API Bing для поиска новостей с помощью компонента "Подключенные службы" в Visual Studio

С помощью службы "Поиск новостей Bing" можно позволить приложениям и службам использовать мощные возможности поисковой системы без рекламы, разработанной для Интернета. Служба "Поиск новостей Bing" является одной из служб поиска, предоставляемых Cognitive Services.

В этой статье описывается, как использовать компонент "Подключенная служба" в Visual Studio для работы со службой "Поиск новостей Bing". Эта возможность доступна в Visual Studio 2017 версии 15.7 и более поздних версиях с установленным расширением Cognitive Services.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас нет подписки, вы можете зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 версии 15.7 с установленной рабочей нагрузкой "Веб-разработка". [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Добавление в проект поддержки API Bing для поиска новостей

1. Создайте проект веб-приложения ASP.NET Core и назовите его MyWebApplication. Используйте шаблон проекта **Веб-приложение (модель-представление-контроллер)** с параметрами по умолчанию. Важно присвоить проекту имя MyWebApplication, чтобы при копировании кода в проект использовалось соответствующее пространство имен. 

1. В **обозревателе решений** выберите **Добавить** > **Подключенная служба**.
   Откроется страница "Подключенная служба" с перечнем служб, которые можно добавить в проект.

   ![Снимок экрана: пункт меню "Добавить подключенную службу"](../media/vs-common/Connected-Service-Menu.PNG)

1. В меню доступных служб выберите **Bring Intelligent Search To Your Apps** (Добавить в приложения возможности интеллектуального поиска).

   ![Снимок экрана: список подключенных служб](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Если вы выполнили вход в Visual Studio и с вашей учетной записью связана подписка Azure, отобразится страница с раскрывающимся списком ваших подписок. Выберите подписку, которую вы хотите использовать, затем выберите имя API Bing для поиска новостей. Вы также щелкнуть **Изменить**, чтобы изменить автоматически созданное имя.

   ![Снимок экрана: поля подписки и имени](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Выберите группу ресурсов и ценовую категорию.

   ![Снимок экрана: поля группы ресурсов и ценовой категории](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Чтоб получить дополнительные сведения о ценовых категориях, щелкните **Review pricing** (Просмотреть цены).

1. Щелкните **Добавить**, чтобы добавить поддержку подключенной службы.
   Visual Studio модифицирует проект, чтобы добавить пакеты NuGet, записи файла конфигурации и другие изменения для поддержки подключения к API Bing для поиска новостей. В окне вывода показан журнал того, что происходит в проекте. Вы должны увидеть нечто вроде этого:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Теперь файл appsettings.json содержит следующие новые параметры.

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Использование API Bing для поиска новостей для добавления возможностей поиска на веб-страницу

Теперь, когда вы добавили поддержку API Bing для поиска новостей в проект, вы можете использовать этот API, чтобы добавить возможности интеллектуального поиска на веб-страницу, как показано ниже.

1.  В файле *Startup.cs* в метод `ConfigureServices` добавьте вызов `IServiceCollection.AddSingleton`. Будет создан объект configuration с основными параметрами, доступными для кода в проекте.
 
   ```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            services.AddMvc();
            services.AddSingleton<IConfiguration>(Configuration);
        }
   ```


1. Добавьте новый файл класса в папку **Модели** и назовите его *BingNewsModel.cs*. Если вы присвоили проекту другое имя, используйте пространство имен своего проекта вместо MyWebApplication. Замените содержимое этого файла кодом, приведенным ниже.
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Эта модель используется для хранения результатов вызова к службе "Поиск новостей Bing".
 
1. Добавьте в папку **Контроллеры** новый файл класса *IntelligentSearchController.cs*. Замените содержимое этого файла кодом, приведенным ниже.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   В этом коде конструктор задает объект configuration, содержащий ключи. Метод для маршрута `Search` — это просто перенаправление к функции `BingSearchResult`. Она вызывает метод `GetNewsSearchClient` для получения объекта client `NewsSearchAPI`.  Объект client `NewsSearchAPI` содержит метод `SearchAsync`, который фактически вызывает службу и возвращает результаты в модель `SearchResult`, которую вы только что создали. 

1. Добавьте класс `MyHandler`, который указан в приведенном выше коде. Он делегирует асинхронный вызов к службе поиска базовому классу `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Чтобы добавить поддержку отправки поисковых запросов и просмотра результатов, в папке **Представления** создайте папку **IntelligentSearch**. В эту папку добавьте представление *BingSearchResult.cshtml*. Скопируйте приведенный ниже код.

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Запустите веб-приложение локально, введите URL-адрес страницы, которую вы только что создали (/IntelligentSearch/BingSearchResult), и отправьте поисковый запроса с помощью кнопки "Search" (Найти).

   ![Снимок экрана: результаты службы "Поиск новостей Bing"](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Очистка ресурсов

Когда группа ресурсов перестанет быть нужна, ее можно удалить. При этом будут удалены ресурсы Сognitive Service и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Выберите группу ресурсов, которую нужно удалить.
2. Выберите **Удалить группу ресурсов**.
3. В поле **Type the Resource Group Name** (Введите имя группы ресурсов) введите имя группы ресурсов и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения об API Bing для поиска новостей см. в [документации по службе "Поиск новостей Bing"](index.yml).
