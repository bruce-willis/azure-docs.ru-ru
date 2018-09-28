---
title: Краткое руководство по веб-серверу ASP.NET для Azure AD версии 2.0 | Документация Майкрософт
description: Узнайте, как реализовать единый вход Майкрософт в веб-приложении ASP.NET с помощью OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/24/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 7196cd6c18680583494a485c136081d90960464d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991289"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Краткое руководство: добавление возможности входа в веб-приложение ASP.NET с помощью учетной записи Майкрософт

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

В этом кратком руководстве вы узнаете, как веб-приложение ASP.NET позволяет войти в личные учетные записи (hotmail.com, outlook.com и т. д.), а также рабочие и учебные учетные записи из любого экземпляра Azure Active Directory (Azure AD).

![Как работает пример приложения, созданный в этом кратком руководстве](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.png)

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
> 1. В поле **URL-адреса перенаправления** введите значение `https://localhost:44368/`.
> 1. Перейдите вниз страницы и нажмите кнопку **Сохранить**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода в этом кратком руководстве необходимо добавить URL-адрес ответа `https://localhost:44368/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-aspnet-webapp/green-check.png) Ваше приложение настроено с использованием этого атрибута

#### <a name="step-2-download-your-project"></a>Шаг 2. Скачивание проекта

[Скачайте решение Visual Studio 2017](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip).

#### <a name="step-3-configure-your-visual-studio-project"></a>Шаг 3. Настройка проекта Visual Studio

1. Извлеките ZIP-файл в локальную папку (например, **C:\Azure-Samples**).
1. Откройте решение в Visual Studio (AppModelv2-WebApp-OpenIDConnect-DotNet.sln).
1. Измените файл **Web.config** и замените `Enter_the_Application_Id_here` на идентификатор зарегистрированного приложения:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    ```
    
> [!div class="sxs-lookup" renderon="portal"]
> [!IMPORTANT]
> Если ваше приложение *однотенантное* (предназначено для учетных записей только в этом каталоге), в файле **Web.config** найдите значение `Tenant` и замените `common` **идентификатором** или **именем клиента** (например, contoso.microsoft.com). Имя клиента можно получить на **странице "Обзор"**.

## <a name="more-information"></a>Дополнительные сведения

В этом разделе представлены общие сведения о коде, необходимом для выполнения входа пользователей. Это может быть полезно для понимания принципов работы кода, основных аргументов, а также того, нужно ли добавлять возможность входа в имеющееся приложение ASP.NET.

### <a name="owin-middleware-nuget-packages"></a>Пакеты NuGet для ПО промежуточного слоя OWIN

Вы можете настроить конвейер проверки подлинности, используя проверку подлинности на основе файлов cookie, с помощью OpenID Connect в ASP.NET и пакетов ПО промежуточного слоя OWIN. Эти пакеты можно установить, выполнив в **консоли диспетчера пакетов** Visual Studio следующие команды:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>Класс Startup OWIN

ПО промежуточного слоя OWIN использует *класс Startup*, выполняемый при инициализации ведущего процесса (в этом кратком руководстве это файл *startup.cs*, расположенный в корневой папке). В следующем коде показан параметр, используемый в этом кратком руководстве:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the ClientId, authority, RedirectUri as obtained from web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the id_token - which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.IdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Where  |  |
> |---------|---------|
> | `ClientId`     | Идентификатор приложения, зарегистрированного на портале Azure |
> | `Authority`    | Конечная точка STS для проверки подлинности пользователей. Обычно это https://login.microsoftonline.com/{tenant}/v2.0 для общедоступного облака, где {tenant} — имя вашего клиента, идентификатор клиента или *common* для ссылки на общую конечную точку (используется для мультитенантных приложений). |
> | `RedirectUri`  | URL-адрес, куда пользователи переходят после проверки подлинности на конечной точке Azure AD версии 2.0 |
> | `PostLogoutRedirectUri`     | URL-адрес, куда пользователи переходят после выхода |
> | `Scope`     | Список запрашиваемых областей, разделенных пробелами |
> | `ResponseType`     | Запрос, в котором указано, что ответ после проверки подлинности содержит маркер идентификации |
> | `TokenValidationParameters`     | Список параметров для проверки маркеров. В этом случае для `ValidateIssuer` задано значение `false`, чтобы указать, что приложение может принимать операции входа с любых типов личных, рабочих или учебных учетных записей |
> | `Notifications`     | Список делегатов, которые могут выполняться для разных сообщений *OpenIdConnect* |

### <a name="initiate-an-authentication-challenge"></a>Инициирование запроса проверки подлинности

Вы можете настроить принудительный вход пользователя, настроив запрос проверки подлинности в контроллере:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Запрос проверки подлинности описанным выше способом необязательный и обычно используется, если представление должно быть доступно для пользователей, прошедших и не прошедших проверку подлинности. Кроме того, можно защитить контроллеры, используя метод, описанный в следующем разделе.

### <a name="protect-a-controller-or-a-controllers-method"></a>Защита контроллера или метода контроллера

Контроллер или его действия можно защитить с помощью атрибута `[Authorize]`. Этот атрибут ограничивает доступ к контроллеру или действиям, разрешив доступ к действиям в контроллере только прошедшим проверку подлинности пользователям. Это означает, что запрос проверки подлинности будет выполняться автоматически, если пользователь, *не прошедший проверку подлинности*, попытается получить доступ к одному из действий или контроллеру с указанным атрибутом `[Authorize]`.

## <a name="next-steps"></a>Дополнительная информация

В руководстве по ASP.NET вы найдете пошаговые инструкции по созданию приложений и функций, а также полное описание того, о чем говорится в этом кратком руководстве.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Изучите инструкции, чтобы создать приложение, используемое в этом кратком руководстве.

> [!div class="nextstepaction"]
> [Добавление возможности входа в веб-приложение ASP.NET с помощью учетной записи Майкрософт](.\tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]