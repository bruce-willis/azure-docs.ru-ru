---
title: Краткое руководство по веб-приложению ASP.NET Core для Azure AD версии 2.0 | Документация Майкрософт
description: Узнайте, как реализовать единый вход Майкрософт в веб-приложении ASP.NET Core с помощью OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: f57dc1707a9e25e4ea308142e804cdbc80d4308a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984948"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Добавление возможности входа в веб-приложение ASP.NET Core с помощью учетной записи Майкрософт

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

В этом кратком руководстве содержится пример кода, который демонстрирует, как в веб-приложение ASP.NET Core можно войти с использованием личных (hotmail.com, live.com и т. д.), а также рабочих и учебных учетных записей из любого экземпляра Azure Active Directory.

![Как работает пример приложения, созданный в этом кратком руководстве](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.png)


> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в кратком руководстве
>
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Регистрация и настройка приложения и примера кода
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> 
> 1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app).
> 1. Введите имя своего приложения, снимите флажок **Пошаговая установка** и нажмите кнопку **Создать**.
> 1. Щелкните `Add Platform`, а затем выберите `Web`.
> 1. *Установите флажок* **Разрешить неявный поток**.
> 1. В поле **URL-адреса перенаправления** введите значение `https://localhost:3110/`.
> 1. Перейдите вниз страницы и нажмите кнопку **Сохранить**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода в этом кратком руководстве необходимо добавить URL-адрес ответа `http://localhost:3110/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-aspnet-core-webapp/green-check.png) Ваше приложение настроено с использованием этого атрибута

#### <a name="step-2-download-your-aspnet-core-project"></a>Шаг 2. Скачивание проекта ASP.NET Core

- [Скачивание проекта ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Шаг 3. Настройка проекта

1. Извлеките ZIP-файл в локальную папку (например, **C:\Azure-Samples**).
1. Если вы используете Visual Studio 2017, откройте проект в Visual Studio (необязательно).
1. Измените файл **appsettings.json** и замените `ClientId` на идентификатор зарегистрированного приложения:

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "common"
    ```
1. Если ваше приложение является *однотенантным* (предназначено для учетных записей только в текущем каталоге), в файле **appsettings.json** найдите значение `TenantId` и замените `common` **идентификатором** или **именем клиента** (например, contoso.microsoft.com). Имя клиента можно получить на **странице "Обзор"**.

## <a name="more-information"></a>Дополнительные сведения

В этом разделе представлены общие сведения о коде, необходимом для выполнения входа пользователей. Они помогут вам понять принципы работы кода, основные аргументы, а также решить, нужно ли добавлять возможность входа в имеющееся приложение ASP.NET Core.

### <a name="startup-class"></a>Класс Startup

ПО промежуточного слоя *Microsoft.AspNetCore.Authentication* использует класс Startup, который выполняется при инициализации хост-процесса:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddAzureAd(options => Configuration.Bind("AzureAd", options))
    .AddCookie();

    services.AddMvc();
}
```

Метод `AddAuthentication` добавляет в службу аутентификацию на основе файлов cookie, используемую в сценариях браузера, а также настраивает запрос OpenIdConnect. 

Строка, содержащая `.AddAzureAd`, добавляет в ваше приложение аутентификацию Azure Active Directory.

Кроме того, файл **AzureAdAuthenticationBuilderExtensions.cs** добавляет метод расширения в конвейер аутентификации Azure AD. Этот метод расширения настраивает атрибуты, необходимые для аутентификации Azure AD. Метод `Configure` в интерфейсе `IConfigureNamedOptions` содержит следующее:

```csharp
public void Configure(string name, OpenIdConnectOptions options)
{
    options.ClientId = _azureOptions.ClientId;
    options.Authority = $"{_azureOptions.Instance}common/v2.0";   // V2 specific
    options.UseTokenLifetime = true;
    options.RequireHttpsMetadata = false;
    options.TokenValidationParameters.ValidateIssuer = false;     // accept several tenants (here simplified)
}
```
> |Where  |  |
> |---------|---------|
> |ClientId     |Идентификатор приложения, зарегистрированного на портале Azure.|
> |Authority | Конечная точка STS для проверки подлинности пользователей. Обычно это https://login.microsoftonline.com/{tenant}/v2.0 для общедоступного облака, где {tenant} — имя вашего клиента, идентификатор клиента или *common* для ссылки на общую конечную точку (используется для мультитенантных приложений).|
> |UseTokenLifetime |Указывает, что файл cookie проверки подлинности должен совпадать с маркером проверки подлинности.|
> |RequireHttpsMetadata     |Требование HTTPS для адреса метаданных или центра сертификации. Рекомендуется изменить это значение на `True`.|
> |TokenValidationParameters     | Список параметров для проверки маркеров. В этом случае для `ValidateIssuer` задано значение `false`, чтобы указать, что приложение может принимать операции входа с любых типов личных, рабочих или учебных учетных записей.|

### <a name="initiate-an-authentication-challenge"></a>Инициирование запроса проверки подлинности

Вы можете настроить принудительный вход пользователя, задав запрос проверки подлинности в контроллере так же, как в **AccountController.cs**:

```csharp
public IActionResult SignIn()
{
    var redirectUrl = Url.Action(nameof(HomeController.Index), "Home");
    return Challenge(
        new AuthenticationProperties { RedirectUri = redirectUrl },
        OpenIdConnectDefaults.AuthenticationScheme);
}
```

> [!TIP]
> Запрос проверки подлинности с использованием описанного выше метода необязательный и обычно используется, если представление должно быть доступно как для прошедших, так и для непрошедших проверку подлинности пользователей. Вы можете защитить контроллеры, используя метод, описанный в следующем разделе.

### <a name="protect-a-controller-or-a-controllers-method"></a>Защита контроллера или метода контроллера

Контроллер или его методы можно защитить с помощью атрибута `[Authorize]`. Этот атрибут ограничивает доступ к контроллеру и методам, позволяя выполнять действия только пользователям, прошедшим проверку подлинности. Это означает, что когда к контроллеру получает доступ неаутентифицированный пользователь, выполняется запрос проверки подлинности.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения, включая инструкции по добавлению аутентификации в новое веб-приложение ASP. NET Core, см. в этом кратком руководстве в репозитории GitHub:

> [!div class="nextstepaction"]
> [Пример кода веб-приложения ASP.NET Core](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]