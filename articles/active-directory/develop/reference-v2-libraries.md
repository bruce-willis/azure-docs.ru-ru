---
title: Библиотеки аутентификации Azure Active Directory версии 2.0 | Документация Майкрософт
description: Сведения о совместимых клиентских библиотеках и серверных библиотеках ПО промежуточного слоя, а также ссылки на связанные библиотеки, исходный код и примеры для конечной точки Azure Active Directory версии 2.0.
services: active-directory
documentationcenter: ''
author: negoe
manager: mtillman
editor: ''
ms.assetid: 19cec615-e51f-4141-9f8c-aaf38ff9f746
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 220627e6903abb643cde63cbc9b85402360ce3f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999772"
---
# <a name="azure-active-directory-v20-authentication-libraries"></a>Библиотеки проверки подлинности Azure Active Directory версии 2.0

[Конечная точка Azure Active Directory (Azure AD) версии 2.0](active-directory-v2-compare.md) поддерживает стандартные отраслевые протоколы OAuth 2.0 и OpenID Connect 1.0. Библиотека аутентификации Майкрософт (MSAL) предназначена для работы с конечными точками Azure AD версии 2.0. Можно также использовать библиотеки с открытым кодом, которые поддерживают OAuth 2.0 и OpenID Connect 1.0.

Рекомендуется использовать библиотеки, написанные специалистами в области протоколов, которые придерживаются методов жизненного цикла разработки защищенных приложений (SDL), [таких, каким следуют в корпорации Майкрософт][Microsoft-SDL]. Если вы решили написать код для протоколов, следуйте методам SDL (например тем, которых придерживаются в корпорации Майкрософт) и внимательно прочитайте рекомендации по обеспечению безопасности в спецификациях стандартов для каждого из протоколов.

> [!NOTE]
> Ищете библиотеку Azure AD версии 1.0 (ADAL)? Ознакомьтесь с [руководством по библиотекам ADAL](active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Типы библиотек

Конечная точка Azure AD версии 2.0 поддерживает два типа библиотек.

* **Клиентские библиотеки**. Собственные клиенты и серверы используют клиентские библиотеки, чтобы получить маркер доступа для вызова ресурса, например Microsoft Graph.
* **Серверные библиотеки ПО промежуточного слоя**. Веб-приложения используют серверные библиотеки ПО промежуточного слоя для выполнения входа пользователей. Веб-API используют серверные библиотеки ПО промежуточного слоя для проверки маркеров, отправляемых собственными клиентами или другими серверами.

## <a name="library-support"></a>Поддержка библиотек

Для библиотек предусмотрено две категории поддержки:

* **Библиотеки, поддерживаемые корпорацией Майкрософт**. Корпорация Майкрософт предоставляет исправления для этих библиотек, к тому же она провела для них комплексную экспертизу жизненного цикла разработки защищенных приложений.
* **Совместимые библиотеки**. Корпорация Майкрософт протестировала такие библиотеки в основных сценариях и подтвердила их совместимость с конечной точкой версии 2.0. Корпорация Майкрософт не предоставляет исправления для этих библиотек и не выполняла их проверку. Проблемы и запросы возможностей следует отправлять в проекты с открытым кодом библиотеки.

Список библиотек, совместимых с конечной точкой версии 2.0, см. в следующих разделах этой статьи.

## <a name="microsoft-supported-client-libraries"></a>Клиентские библиотеки, поддерживаемые корпорацией Майкрософт

Клиентские библиотеки аутентификации используются, чтобы получить маркер для вызова защищенного веб-API

| платформа | Библиотека | Загрузка | Исходный код | Образец | Справочные материалы | Основная документация | Стратегия |
| --- | --- | --- | --- | --- | --- | --- | ---| ---|
| ![JavaScript](media/sample-v2-code/logo_js.png) | MSAL.js (предварительная версия) | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  [Одностраничное приложение](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) |  | [Вики-сайт](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki)|
|![Angular JS](media/sample-v2-code/logo_angular.png) | MSAL для Angular JS | [NPM](https://www.npmjs.com/package/@azure/msal-angularjs) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md) |  |  | |
![Angular](media/sample-v2-code/logo_angular.png) | MSAL для Angular (предварительная версия) | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | | | |
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL для .NET (предварительная версия) |[NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Классическое приложение](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL для .NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Вики-сайт](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#conceptual-documentation) | [План выхода продуктов](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![iOS, Objective-C или swift](media/sample-v2-code/logo_iOS.png) | MSAL для obj_c (предварительная версия) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [Приложение iOS](https://github.com/Azure-Samples/active-directory-msal-ios-swift) |  |
|![Android и Java](media/sample-v2-code/logo_Android.png) | MSAL (предварительная версия) | [Центральный репозиторий](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Приложение Android](quickstart-v2-android.md) | [Javadocs](http://javadoc.io/doc/com.microsoft.identity.client/msal) | | |

## <a name="microsoft-supported-server-middleware-libraries"></a>Серверные библиотеки ПО промежуточного слоя, поддерживаемые корпорацией Майкрософт

Библиотеки ПО промежуточного слоя используются для защиты веб-приложений и веб-API. Для веб-приложений или веб-API, написанных с помощью ASP.NET или ASP.NET Core, эти библиотеки используются ASP.NET и ASP.NET Core.

| платформа | Библиотека | Загрузка | Исходный код | Образец | Справочные материалы
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | Безопасность ASP.NET |[NuGet](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[Безопасность ASP.NET (GitHub)](https://github.com/aspnet/Security) |[Приложение MVC](quickstart-v2-aspnet-webapp.md) |[Справочник по API для ASP.NET](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| Расширения модели идентификации для .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [Приложение MVC](quickstart-v2-aspnet-webapp.md) |[Справочные материалы](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD Passport |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Веб-приложение](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="compatible-client-libraries"></a>Совместимые клиентские библиотеки

| платформа | Имя библиотеки | Проверенные версии | Исходный код | Образец |
|:---:|:---:|:---:|:---:|:---:|
|![JavaScript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) |1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Безопасная проверка пароля](https://github.com/Azure-Samples/active-directory-javascript-graphapi-web-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Scribe Java](https://github.com/scribejava/scribejava) | [Версия 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [The PHP League (oauth2-client)](https://github.com/thephpleague/oauth2-client) | [Версия 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth](https://github.com/omniauth/omniauth/wiki) |omniauth:1.3.1</br>omniauth-oauth2:1.4.0 |[OmniAuth](https://github.com/omniauth/omniauth)</br>[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
![iOS](media/sample-v2-code/logo_iOS.png) |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |1.2.8 |[NXOAuth2Client](https://github.com/nxtbgthng/OAuth2Client) |[Пример встроенного приложения](active-directory-v2-devquickstarts-ios.md) |

При использовании конечной точки версии 2.0 можно выбрать любую библиотеку, соответствующую стандартам. При этом важно знать, куда обратиться за поддержкой.

* Проблемы и запросы на новые возможности в коде библиотеки отправляются владельцу библиотеки.
* Проблемы и запросы на новые возможности в реализации протокола на стороне службы отправляются в корпорацию Майкрософт.
* [Зарегистрируйте запрос функции](https://feedback.azure.com/forums/169401-azure-active-directory) для дополнительных компонентов, которые вы хотите добавить в протокол.
* [Создайте запрос в службу поддержки](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) при обнаружении проблемы несовместимости конечной точки версии 2.0 Azure AD с OAuth 2.0 и OpenID Connect 1.0.

## <a name="related-content"></a>Связанная информация

Дополнительные сведения о конечной точке Azure AD версии 2.0 см. в статье [Вход для пользователей учетных записей Майкрософт и Azure AD в одном приложении][AAD-App-Model-V2-Overview].

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: http://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: http://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: http://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
