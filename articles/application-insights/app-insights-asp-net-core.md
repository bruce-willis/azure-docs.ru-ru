---
title: Azure Application Insights для ASP.NET Core | Документация Майкрософт
description: Отслеживайте доступность, производительность и использование веб-приложений.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: a0e0d2c3604ea2357f52c3d464933c622ba1810d
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523246"
---
# <a name="application-insights-for-aspnet-core"></a>Application Insights для ASP.NET Core

Azure Application Insights обеспечивает детализированный мониторинг веб-приложения на уровне кода. Вы можете легко отслеживать доступность, производительность и использование своего веб-приложения. Вы также можете быстро идентифицировать и диагностировать ошибки в приложении, не дожидаясь, пока пользователь сообщит о них.

Эта статья содержит сведения о создании примера приложения [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) ASP.NET Core в Visual Studio и о том, как начать выполнять мониторинг с помощью Application Insights.

## <a name="prerequisites"></a>Предварительные требования

- Пакет SDK для .NET Core 2.0.0 или более поздней версии.
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) версии 15.7.3 или выше с ASP.NET и рабочей нагрузкой веб-разработки.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Создание проекта ASP.NET Core в Visual Studio

1. Щелкните правой кнопкой мыши **Visual Studio 2017**, а затем выберите **Запуск от имени администратора**.
2. Выберите **Файл** > **Создать** > **Проект** (CTRL+SHIFT+N).

   ![Снимок экрана меню выбора нового проекта в Visual Studio](./media/app-insights-asp-net-core/001-new-project.png)

3. Разверните **Visual C#**. Выберите **.NET Core** > **Веб-приложение ASP.NET Core**. Введите имя проекта и имя решения, а затем выберите **Создать новый репозиторий Git**.

   ![Снимок экрана мастера создания проекта в Visual Studio](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Выберите **.NET Core** > **ASP.NET Core 2.0** и **Веб-приложение** > **ОК**.

    ![Снимок экрана выбора шаблона нового проекта в Visual Studio](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Поиск по Application Insights

В версии Visual Studio 2015 с обновлением 2 или более поздней в проекте на основе ASP.NET Core 2+ вы сможете использовать [поиск по Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) еще до того, как добавите Application Insights в свой проект.

Для проверки функции сделайте следующее:

1. Запустите приложение. Чтобы запустить приложение, выберите значок **IIS Express** (![Снимок экрана со значком IIS Express в Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png)).

2. Выберите **Просмотр** > **Другие окна** > **Поиск по Application Insights**.

   ![Снимок экрана выбора инструментов диагностики в Visual Studio](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. Телеметрия сеанса отладки в настоящее время доступна только для локального анализа. Чтобы полностью включить Application Insights, выберите **Готовность телеметрии** в правом верхнем углу или следуйте инструкциям ниже.

   ![Снимок экрана "Поиск по Application Insights" в Visual Studio](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> Для получения дополнительных сведений о том, как в Visual Studio локально используются такие компоненты, как [поиск по Application Insights](app-insights-visual-studio.md) и [CodeLens](app-insights-visual-studio-codelens.md), перед добавлением Application Insights в свой проект ASP.NET Core ознакомьтесь с разделом [Больше сведений о поиске по Application Insights](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>Добавление телеметрии Application Insights

1. Выберите **Проект** > **Добавить телеметрию Application Insights**. (Или щелкните правой кнопкой мыши **Подключенные службы** и выберите **Добавить подключенную службу**.)

    ![Снимок экрана меню выбора "Добавить телеметрию Application Insights" в Visual Studio](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Выберите **Начать**. (В зависимости от используемой версии Visual Studio текст может несколько отличаться. У некоторых предыдущих версий есть кнопка **Начать бесплатно**.)

    ![Снимок экрана кнопки "Начать бесплатно" в Application Insights](./media/app-insights-asp-net-core/008-get-started.png)

3. Выберите свою подписку, а затем выберите **Ресурс** > **Зарегистрировать**.

## <a name="changes-made-to-your-project"></a>Изменения, внесенные в проект

Application Insights имеет низкие издержки. Чтобы просмотреть изменения, внесенные в ваш проект путем добавления телеметрии Application Insights, сделайте следующее:

Выберите **Вид** > **Командный обозреватель** (CTRL+\, CTRL+M) > **Проект** > **Изменения**.

- Появятся четыре общих изменения:

  ![Снимок экрана файлов, измененных путем добавления Application Insights](./media/app-insights-asp-net-core/009-changes.png)

- Создан один файл:

  -  _ConnectedService.json_

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "8.12.10405.1",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```

- Изменены три файла (чтобы выделить изменения, добавлены дополнительные комментарии):

  - _appsettings.json_:

    ```json
    {
      "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
          "Default": "Warning"
        }
      },
    // Changes to file post adding Application Insights Telemetry:
      "ApplicationInsights": {
        "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
      }
    }
    //
    ```

  - _ContosoDotNetCore.csproj_:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
      <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
        <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
    <!---->
      </PropertyGroup>
      <ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
    <!---->
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
      </ItemGroup>
      <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
      </ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    <!---->
    </Project>
    ```

  -  _Program.cs_:

      ```csharp
      using System;
      using System.Collections.Generic;
      using System.IO;
      using System.Linq;
      using System.Threading.Tasks;
      using Microsoft.AspNetCore;
      using Microsoft.AspNetCore.Hosting;
      using Microsoft.Extensions.Configuration;
      using Microsoft.Extensions.Logging;

      namespace DotNetCore
      {
          public class Program
          {
              public static void Main(string[] args)
              {
                  BuildWebHost(args).Run();
              }

              public static IWebHost BuildWebHost(string[] args) =>
                  WebHost.CreateDefaultBuilder(args)
      // Change to file post adding Application Insights Telemetry:
                      .UseApplicationInsights()
      //
                      .UseStartup<Startup>()
                      .Build();
          }
      }
      ```

## <a name="synthetic-transactions-with-powershell"></a>Создание искусственных транзакций с помощью PowerShell

Чтобы автоматизировать запросы к своему приложению, используйте искусственные транзакции:

1. Чтобы запустить приложение, щелкните значок ![Снимок экрана: значок IIS Express в Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png) .

2. Скопируйте URL-адрес из адресной строки браузера. Он имеет следующий формат: `http://localhost:<port number>`.

   ![Снимок экрана URL-адреса в адресной строке браузера](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Запустите указанный ниже цикл PowerShell, чтобы создать 100 искусственных транзакций с помощью приложения для тестирования. Измените номер порта, указанный после `localhost:`, в соответствии с URL-адресом, скопированным на предыдущем шаге. Например: 

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Открытие портала Application Insights

После выполнения команд PowerShell в предыдущем разделе откройте Application Insights, чтобы просмотреть транзакции и подтвердить сбор данных. 

В меню Visual Studio выберите **Проект** > **Application Insights** > **Открыть портал Application Insights**.

   ![Снимок экрана: обзор Application Insights](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> На снимке экрана с примером выше не выполняется сбор данных для параметров **Live Stream**, **Время загрузки страницы** и **Невыполненные запросы**. В следующем разделе описаны шаги по добавлению данных для этих параметров. Если вы уже собрали данные для **Live Stream** и **Время загрузки страницы**, выполните действия только для параметра **Невыполненные запросы**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Сбор данных для параметров "Невыполненные запросы", "Live Stream" и "Время загрузки страницы"

### <a name="failed-requests"></a>Невыполненные запросы

С технической точки зрения данные невыполненных запросов собираются, но еще не отображаются. Чтобы ускорить процесс, можно добавить пользовательское исключение к имеющемуся проекту для имитации реального исключения. Если приложение все еще запущено в Visual Studio, прежде чем продолжить, выберите **Остановить отладку** (SHIFT+F5).

1. В **обозревателе решений** разверните **Страницы** > **About.cshtml** и откройте *About.cshtml.cs*.

   ![Снимок экрана: обозреватель решений Visual Studio](./media/app-insights-asp-net-core/011-about.png)

2. Добавьте исключение в разделе ``Message=`` и сохраните изменения в файле.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Live Stream

Чтобы получить доступ к функции Live Stream в Application Insights с помощью ASP.NET Core, обновите пакеты NuGet до версии Microsoft.ApplicationInsights.AspNetCore 2.2.0.

В Visual Studio выберите **Проект** > **Управление пакетами NuGet** > **Microsoft.ApplicationInsights.AspNetCore**, в поле "Версия" введите **2.2.0** >  нажмите кнопку **Обновить**.

  ![Снимок экрана: диспетчер пакетов NuGet](./media/app-insights-asp-net-core/012-nuget-update.png)

Появятся несколько запросов на подтверждение. Прочитайте и примите условия, если вы согласны с изменениями.

### <a name="page-view-load-time"></a>Время загрузки страницы

1. В Visual Studio выберите **Обозреватель решений** > **Страницы**. Вам необходимо изменить два файла: *Layout.cshtml* и *ViewImports.cshtml*.

2. В файле *ViewImports.cshtml* добавьте код ниже:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. В файле *Layout.cshtml* добавьте код ниже перед тегом ``</head>``, а также перед любыми другими сценариями:

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Тестирование сбора данных для параметров "Невыполненные запросы", "Время загрузки страницы" и "Live Stream"

Чтобы проверить и убедиться, что все работает, сделайте следующее:

1. Запустите приложение. Чтобы запустить приложение, щелкните значок ![Снимок экрана: значок IIS Express в Visual Studio](./media/app-insights-asp-net-core/004-iis-express.png) .

2. Перейдите на страницу **О программе**, чтобы активировать исключения теста. (Если вы выполняете запуск в режиме отладки, в Visual Studio щелкните **Продолжить** для того, чтобы просмотреть исключение в Application Insights.)

3. Перезапустите смоделированный сценарий транзакции PowerShell, который вы использовали ранее. (Может потребоваться изменить номер порта в сценарии.)

4. Если страница **обзора** Applications Insights еще не открыта, в меню Visual Studio выберите **Проект** > **Application Insights** > **Открыть портал Application Insights**. 

   > [!TIP]
   > Если вы не видите новый трафик, проверьте значение для **диапазона времени**, а затем выберите **Обновить**.

   ![Снимок экрана окна обзора](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Выберите **Live Stream**.

   ![Снимок экрана: Live Metrics Stream](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Если по-прежнему выполняется сценарий PowerShell, вы должны увидеть динамические метрики.) (Если сценарий PowerShell остановлен, запустите его снова с открытой службой Live Stream.)

## <a name="application-insights-sdk-comparison"></a>Сравнение пакетов SDK для Application Insights

Группа по продукту Application Insights работала над тем, чтобы согласовать функциональные возможности [полного пакета SDK для .NET Framework](https://github.com/Microsoft/ApplicationInsights-dotnet) и пакета SDK для .NET Core. В [пакете SDK для ASP.NET Core версии 2.2.0](https://github.com/Microsoft/ApplicationInsights-aspnetcore) для Application Insights максимально минимизировано различие между функциями.

В следующей таблице описывается большая часть различий и компромиссов между [.NET и .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server):

   | Сравнение пакетов SDK | ASP.NET:        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Динамические метрики**      | **+** |**-** | **+** |
   | **Канал телеметрии сервера** | **+** |**-** | **+**|
   |**Адаптивная выборка**| **+** | **-** | **+**|
   | **Вызов зависимостей SQL**     | **+** |**-** | **+**|
   | **Счетчики производительности*** | **+** | **-**| **-**|

Счетчики производительности в этом контексте относятся к [счетчикам производительности на стороне сервера](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters), например использование процессора, памяти и диска.

## <a name="open-source-sdk"></a>Пакет SDK с открытым исходным кодом
[Чтение кода и дополнительные наработки](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Больше сведений о поиске по Application Insights

Этот раздел поможет вам лучше понять, как в Visual Studio работает поиск по Application Insights для проекта ASP.NET Core 2. Он работает таким образом, даже если вы еще явно не установили пакеты NuGet для Application Insights. Поиск может быть полезен для просмотра выходных данных отладки.

Если выполнить поиск выходных данных для слова _аналитические сведения_, он выделит результаты, аналогичные показанным ниже.

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

В выходных данных CoreCLR загружает две сборки: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_,
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

_Ненастроенная_ ссылка в каждом экземпляре телеметрии Application Insights указывает, что это приложение не связано с ключом ikey. Данные, созданные во время работы вашего приложения, не отправляются в Azure. Они доступны только для локального поиска и анализа.

Частично это возможно, потому что пакет NuGet _Microsoft.AspNetCore.All_ принимает в качестве зависимости [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1).

![Снимок экрана схемы зависимостей NuGet для Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

За пределами Visual Studio, если вы редактировали проект ASP.NET Core с помощью VS Code или другого редактора, эти сборки не загружаются автоматически во время отладки, если вы еще явно не добавили в проект Application Insights.

Однако в Visual Studio эти локальные возможности Application Insights из внешних сборок реализуются с помощью [интерфейса IHostingStartup](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1), который динамически добавляет Application Insights во время отладки.

Дополнительные сведения о расширении приложения из [внешней сборки в ASP.NET Core с IHostingStartup](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Отключение Application Insights в проектах .NET Core в Visual Studio

Хотя автоматическое использование функций поиска по Application Insights в некоторых случаях может быть полезно, просмотр сгенерированной телеметрии отладки может сбить с толку неподготовленного пользователя.

Если достаточно просто отключить создание телеметрии, можно добавить этот блок кода в метод **configure** файла _Startup.cs_:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR будет по-прежнему загружать файлы _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ и _Microsoft.ApplicationInsights.AspNetCore.dll_, но они не будут выполнять никаких действий.

Если вы хотите полностью отключить Application Insights в проекте .NET Core в Visual Studio, лучше выбрать **Инструменты** > **Параметры** > **Проекты и Решения** > **Веб-проекты** и установить флажок **Отключить локальные данные Application Insights для веб-проектов ASP.NET Core**. Эта функция была добавлена в Visual Studio 15.6.

![Снимок экрана окна параметров веб-проектов Visual Studio](./media/app-insights-asp-net-core/014-disable.png)

Если запущена более ранняя версия Visual Studio и вы хотите полностью удалить все сборки, загруженные через *IHostingStartup*, есть два варианта:

* добавить `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` в файл _Program.cs_:

  ```csharp
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Logging;

  namespace DotNetCore
  {
      public class Program
      {
          public static void Main(string[] args)
          {
              BuildWebHost(args).Run();
          }

          public static IWebHost BuildWebHost(string[] args) =>
              WebHost.CreateDefaultBuilder(args)
                  .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                  .UseStartup<Startup>()
                  .Build();
      }
  }
  ```

* добавить ``"ASPNETCORE_preventHostingStartup": "True"`` в переменные среды файла _launchSettings.json_.

Проблема с использованием любого из этих методов заключается в том, что они отключают не только Application Insights. Эти методы также отключают какой-либо компонент в Visual Studio, который использует функциональные возможности *IHostingStartup*.

## <a name="video"></a>Видео

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>Дополнительная информация
* [Изучите маршруты пользователей](app-insights-usage-flows.md), чтобы понять, как пользователи перемещаются через приложение.
* [Настройте коллекцию моментальных снимков](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) для отображения состояния исходного кода и переменных в момент создания исключения.
* [Используйте API](app-insights-api-custom-events-metrics.md) для отправки собственных событий и метрик для более четкого представления о производительности и использовании приложения.
* Используйте [тесты доступности](app-insights-monitor-web-app-availability.md), чтобы постоянно проверять работу приложения из всех точек мира.
