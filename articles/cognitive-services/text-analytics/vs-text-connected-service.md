---
title: Руководство по использованию службы "Анализ текста" для C# | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Подключение к службе "Анализ текста" из веб-приложения ASP.NET Core.
services: cognitive-services
author: ghogen
manager: douge
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: conceptual
ms.date: 06/01/2018
ms.author: ghogen
ms.openlocfilehash: eb9730f785b01a620e36a265216488c401eac63a
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068494"
---
# <a name="connect-to-the-text-analytics-service-by-using-connected-services-in-visual-studio"></a>Подключение к службе "Анализ текста" с помощью компонента "Подключенные службы" в Visual Studio

С помощью службы "Анализ текста" можно извлекать из изображений ценные сведения для классификации и обработки визуальных данных, а функция машинной модерации изображений обеспечит необходимый контроль за вашими службами.

В этой статье и других статьях этого цикла приводятся сведения об использовании компонента "Подключенная служба" Visual Studio для службы "Анализ текста". Эта возможность доступна как в Visual Studio 2017 15.7, так и в более поздней версии с установленным расширением Cognitive Services.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас нет подписки, вы можете зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2017 версии 15.7 с установленной рабочей нагрузкой "Веб-разработка". [Скачайте это приложение](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-the-text-analytics-service"></a>Добавление в проект поддержки службы "Анализ текста"

1. Создайте проект веб-приложения ASP.NET Core и назовите его TextAnalyticsDemo. Используйте шаблон проекта веб-приложения (Model-View-Controller) с параметрами по умолчанию. Важно присвоить проекту имя MyWebApplication, чтобы при копировании кода в проект использовалось соответствующее пространство имен.  В примере в этой статье используется MVC, но можно использовать подключенную службу "Анализ текста" с любым типом проекта ASP.NET.

1. В **обозревателе решений** дважды щелкните элемент **Подключенная служба**.
   Откроется страница "Подключенная служба" с перечнем служб, которые можно добавить в проект.

   ![Снимок экрана: страница "Подключенная служба" в обозревателе решений](../media/vs-common/Connected-Services-Solution-Explorer.PNG)

1. В меню доступных служб выберите **Evaluate Sentiment with Text Analytics** (Оценка тональности с помощью службы "Анализ текста").

   ![Снимок экрана: вкладка "Подключенные службы"](./media/vs-text-connected-service/Cog-Text-Connected-Service-0.PNG)

   Если вы выполнили вход в Visual Studio и с вашей учетной записью связана подписка Azure, отобразится страница с раскрывающимся списком ваших подписок.

   ![Снимок экрана подключенной службы "Анализ текста"](media/vs-text-connected-service/Cog-Text-Connected-Service-1.PNG)

1. Выберите подписку, которую необходимо использовать, придумайте имя для службы "Анализ текста" или выберите ссылку **Изменить**, чтобы изменить автоматически созданное имя, а затем выберите группу ресурсов и ценовую категорию.

   ![Снимок экрана: поля группы ресурсов и ценовой категории](media/vs-text-connected-service/Cog-Text-Connected-Service-2.PNG)

   Для получения дополнительных сведений о ценовых категориях перейдите по ссылке.

1. Щелкните **Добавить**, чтобы добавить поддержку подключенной службы.
   Visual Studio модифицирует проект, чтобы добавить пакеты NuGet, записи файла конфигурации и другие изменения для поддержки подключения к службе "Анализ текста". В **окне вывода** показан журнал того, что происходит в проекте. Вы должны увидеть нечто вроде этого:

   ```output
    [6/1/2018 3:04:02.347 PM] Adding Text Analytics to the project.
    [6/1/2018 3:04:02.906 PM] Creating new Text Analytics...
    [6/1/2018 3:04:06.314 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Language' version 1.0.0-preview...
    [6/1/2018 3:04:56.759 PM] Retrieving keys...
    [6/1/2018 3:04:57.822 PM] Updating appsettings.json setting: 'ServiceKey' = '<service key>'
    [6/1/2018 3:04:57.827 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://westus.api.cognitive.microsoft.com/text/analytics/v2.0'
    [6/1/2018 3:04:57.832 PM] Updating appsettings.json setting: 'Name' = 'TextAnalyticsDemo'
    [6/1/2018 3:05:01.840 PM] Successfully added Text Analytics to the project.
    ```
 
## <a name="use-the-text-analytics-service-to-detect-the-language-for-a-text-sample"></a>Используйте службу "Анализ текста" для определения языка в примере текста.

1. Добавьте следующие инструкции в Startup.cs.
 
   ```csharp
   using System.IO;
   using System.Net.Http;
   using System.Net.Http.Headers;
   using System.Text;
   using Microsoft.Extensions.Configuration;
   ```
 
1. Добавьте поле configuration, а также конструктор, который инициализирует поле configuration в классе Startup, чтобы включить Configuration в программе.

   ```csharp
      private IConfiguration configuration;

      public Startup(IConfiguration configuration)
      {
          this.configuration = configuration;
      }
   ```

1. Добавьте файл класса DemoTextAnalyzeController в папку "Контроллеры" и замените его содержимое следующим кодом.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Extensions.Configuration;
    using TextAnalyticsDemo.Models;
    
    namespace TextAnalyticsDemo.Controllers
    {
        public class DemoTextAnalyzeController : Controller
        {
            private IConfiguration configuration;
    
            public DemoTextAnalyzeController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }
    
            public IActionResult TextAnalyzeResult(TextAnalyzeModel model)
            {
    
                if (!string.IsNullOrWhiteSpace(model.TextStr))
                {
                    ITextAnalyticsAPI client = this.GetTextAnalyzeClient(new MyHandler());
                    model.AnalyzeResult = client.DetectLanguage(
                        new BatchInput(
                            new List<Input>()
                            {
                                new Input("id",model.TextStr)
                            }));
                }
                return View(model);
            }
    
            [HttpPost("Analyze")]
            public IActionResult Analyze(TextAnalyzeModel model)
            {
                return RedirectToAction("TextAnalyzeResult", model);
            }
    
            // Using the ServiceKey from the configuration file,
            // get an instance of the Text Analytics client.
            private ITextAnalyticsAPI GetTextAnalyzeClient(DelegatingHandler handler)
            {
                string key = configuration.GetSection("CognitiveServices")["TextAnalytics:ServiceKey"];
    
                ITextAnalyticsAPI client = new TextAnalyticsAPI( handlers: handler);
                client.SubscriptionKey = key;
                client.AzureRegion = AzureRegions.Westus;
    
                return client;
            }
        }
    }
    ```
    
    Код содержит GetTextAnalyzeClient для получения объекта client, который можно использовать для вызова API анализа текста, и обработчик запросов, который вызывает DetectLanguage для заданного текста.

1. Добавьте вспомогательный класс MyHandler, который используется в приведенном выше коде.

    ```csharp
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

1. В папку Models добавьте класс модели.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    
    namespace Demo.Models
    {
        public class TextAnalyzeModel
        {
            public string TextStr { get; set; }
    
            public LanguageBatchResult AnalyzeResult { get; set; }
    
            public SentimentBatchResult AnalyzeResult2 { get; set; }
        }
    }
    ```

1. Добавьте представление для отображения анализируемого текста, определенного языка и оценки, отражающей уровень достоверности при анализе. Для этого щелкните правой кнопкой мыши папку **Представления** и выберите **Добавить** > **Представление**. В появившемся диалоговом окне укажите имя _TextAnalyzeResult_ и примите значения по умолчанию, чтобы добавить новый файл _TextAnalyzeResult.cshtml_ в папку **Представления**, затем скопируйте в него следующее содержимое.
    
    ```cshtml
    @using System
    @model Demo.Models.TextAnalyzeModel
    
    @{
        ViewData["Title"] = "TextAnalyzeResult";
    }
    
    <h2>Text Language</h2>
    
    <div class="row">
        <section>
            <form asp-controller="DemoTextAnalyze" asp-action="Analyze" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width="90%">
                    <tr>
                        <td>
                            <input type="text" name="TextStr" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Analyze</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    
    <h2>Result</h2>
    <div>
        <dl class="dl-horizontal">
            <dt>
                Text :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.TextStr)
            </dd>
            <dt>
                Language Name :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Name)
            </dd>
            <dt>
                Score :
            </dt>
            <dd>
                @Html.DisplayFor(model => model.AnalyzeResult.Documents[0].DetectedLanguages[0].Score)
            </dd>
        </dl>
    </div>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    
    ```
 
1. Выполните сборку и запуск примера приложения в локальной среде. Введите любой текст и посмотрите, что обнаружит служба "Анализ текста".
   
## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите группу ресурсов, если она больше не нужна. При этом будут удалены ресурсы Сognitive Service и связанные ресурсы. Чтобы удалить группу ресурсов на портале, сделайте следующее:

1. В поле поиска в верхней части портала введите имя группы ресурсов. Если в результатах поиска отображается группа ресурсов, используемая в этом руководстве, выберите ее.
2. Выберите **Удалить группу ресурсов**.
3. В поле **Введите имя группы ресурсов:** введите имя группы ресурсов и выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о службе "Анализ текста" см. в [документации по службе "Анализ текста"](index.yml).
