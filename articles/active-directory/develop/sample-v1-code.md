---
title: Примеры кода Azure Active Directory | Документы Майкрософт
description: Предоставляет список примеров кода Azure Active Directory (конечная точка версии 1.0), сгруппированных по сценариям.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.assetid: a242a5ff-7300-40c2-ba83-fb6035707433
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: a8d1c94ac4357c7833f6db7cb9b4eaffa00c0396
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963804"
---
# <a name="azure-active-directory-code-samples-v10-endpoint"></a>Примеры кода Azure Active Directory (конечная точка версии 1.0)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Для добавления проверки подлинности и авторизации в веб-приложения и веб-интерфейсы API можно использовать Microsoft Azure Active Directory (Azure AD).

Этот раздел содержит ссылки на примеры, с помощью которых вы сможете узнать о конечной точке Azure AD версии 1.0. В этих примерах показано, как это можно сделать. Также приведены фрагменты кода, которые можно использовать в приложениях. На странице примеров кода приведены ссылки на статьи с подробными сведениями о требованиях, установке и настройке. Код указан с комментариями, чтобы можно было понять важные разделы.

> [!NOTE]
> Если вас интересуют примеры кода Azure AD версии 2, см. раздел [Примеры кода версии 2.0 для сценариев](sample-v2-code.md).

Сведения о базовых сценариях для каждого типа примера см. в разделе [Сценарии проверки подлинности в Azure AD](authentication-scenarios.md).

Вы также можете поделиться своими примерами на сайте GitHub. Чтобы узнать, как это сделать, обратитесь к разделу [Примеры и документация Microsoft Azure Active Directory](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Одностраничные приложения

В этом примере показано, как создать одностраничное приложение, защищенное с помощью Azure AD.

 платформа | Вызывает собственный API | Вызывает другой веб-API
 -------- |  --------------------- | ------------------ | ----------------
![JavaScript](media/sample-v2-code/logo_js.png) | [javascript-singlepageapp](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |
![Angular JS](media/sample-v2-code/logo_angular.png) | [angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) | [angularjs-singlepageapp-cors](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi)

## <a name="web-applications"></a>Веб-приложения

### <a name="web-applications-signing-in-users-calling-microsoft-graph-or-a-web-api-with-the-users-identity"></a>Веб-приложения, обрабатывающие вход пользователей и вызывающие Microsoft Graph или веб-API с удостоверением пользователя

В следующем примере показаны веб-приложения для входа пользователей. Некоторые из этих приложений также обращаются к Microsoft Graph или к веб-API от имени вошедшего в систему пользователя.

 платформа | Только вход пользователей | Вызывает Microsoft Graph или AAD Graph| Вызывает другой веб-API ASP.NET или ASP.NET Core 2.0
 -------- | ------------------- | --------------------- | -------------------------
![ASP.NET:](media/sample-v2-code/logo_NETcore.png)<p/>ASP.NET Core 2.0 | [dotnet-webapp-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore) | [webapp-webapi-multitenant-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-webapp-webapi-multitenant-openidconnect-aspnetcore/) <p/>(AAD Graph) | [dotnet-webapp-webapi-openidconnect-aspnetcore](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-openidconnect-aspnetcore)
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [webApp-openidconnect-dotnet](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-aspnetwebapp-v1) <p/> [webapp-WSFederation-dotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation) <p/> [dotnet-webapp-webapi-oauth2-useridentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-useridentity) | [dotnet-webapp-multitenant-openidconnect](https://github.com/Azure-Samples/active-directory-dotnet-webapp-multitenant-openidconnect)<p/> (AAD Graph) |
![Python](media/sample-v2-code/logo_python.png) | | [python-webapp-graphapi](https://github.com/Azure-Samples/active-directory-python-webapp-graphapi)  |
![Java](media/sample-v2-code/logo_java.png)  | | [java-webapp-openidconnect](https://github.com/azure-samples/active-directory-java-webapp-openidconnect)  |
![PHP](media/sample-v2-code/logo_php.png) | | [php-graphapi-web](https://github.com/Azure-Samples/active-directory-php-graphapi-web)  |

### <a name="web-applications-demonstrating-role-based-access-control-authorization"></a>Веб-приложения, демонстрирующие управление доступом на основе ролей (авторизация)

В примерах ниже показано, как реализовать управление доступом на основе ролей (RBAC). RBAC используется, чтобы ограничить разрешения определенных пользователей на доступ к некоторым функциям в веб-приложении. Эти пользователи проходят авторизацию в зависимости от того, принадлежат ли они **группе Azure AD** или имеют определенную **роль** приложения.

платформа | Образец
 -------- | -------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapp-groupclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims) <p/>  [dotnet-webapp-roleclaims](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) | Веб-приложение MVC .NET 4.5, использующее для проверки подлинности **роли** Azure AD

## <a name="desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api"></a>Классические и мобильные клиентские приложения, вызывающие Microsoft Graph или веб-API

В следующих примерах показаны общедоступные клиентские приложения (классические и мобильные приложения), которые обращаются к Microsoft Graph или веб-API от имени пользователя. В зависимости от устройств и платформ, пользователи могут входить в приложения различными способами (последовательности или предоставление): 

- интерактивно;
- автоматически (с использованием встроенной проверки подлинности Windows или имени пользователя и пароля); 
- делегируя интерактивный вход в систему на другое устройство (последовательность кода устройства, используемая на устройствах, которые не предоставляют веб-элементы управления).

Клиентское приложение | платформа | Поток или предоставление | Вызывает Microsoft Graph | Вызывает веб-API ASP.NET или ASP.NET Core 2.x
------------------ | -------- | ---------- | -------------------- | -------------------------
Классическое приложение (WPF)           | ![.NET и C#](media/sample-v2-code/logo_NET.png)  | Interactive | Часть [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) | [Dotnet-native-desktop](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop) </p> [dotnet-native-aspnetcore](https://azure.microsoft.com/resources/samples/active-directory-dotnet-native-aspnetcore/)</p> [dotnet-webapi-manual-jwt-validation](https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation)
Мобильное приложение (UWP)            | .![.NET, C#, UWP](media/sample-v2-code/logo_Windows.png)   | Interactive | [dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) </p> В этом примере используется [WAM](https://docs.microsoft.com/windows/uwp/security/web-account-manager), а не [ADAL.NET](https://aka.ms/adalnet)|  [dotnet-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-windows-store) (приложение UWP, использующее ADAL.NET для обращения к веб-API одного клиента) </p> [dotnet-webapi-multitenant-windows-store](https://github.com/Azure-Samples/active-directory-dotnet-webapi-multitenant-windows-store) (приложение UWP, использующее ADAL.NET для обращения к веб-API нескольких клиентов)|
Мобильное приложение (Android, iOS, UWP)   | ![.NET и C# (Xamarin)](media/sample-v2-code/logo_xamarin.png) | Interactive | [dotnet-native-multitarget](https://github.com/azure-samples/active-directory-dotnet-native-multitarget) |
Мобильное приложение (Android)           | ![Android и Java](media/sample-v2-code/logo_Android.png) | Interactive |   [android](https://github.com/Azure-Samples/active-directory-android) |
Мобильное приложение (iOS)           | ![iOS, Objective-C или swift](media/sample-v2-code/logo_iOS.png) | Interactive |   [nativeClient-iOS](https://github.com/azureadquickstarts/nativeclient-ios) |
Классическое приложение (консольное)          | ![.NET и C#](media/sample-v2-code/logo_NET.png) | Имя пользователя и пароль </p>  Встроенная проверка подлинности Windows | | [dotnet-native-headless](https://github.com/azure-samples/active-directory-dotnet-native-headless)
Классическое приложение (консольное)          | ![Консоль Java](media/sample-v2-code/logo_Java.png) | Имя пользователя и пароль | | [java-native-headless](https://github.com/Azure-Samples/active-directory-java-native-headless)
Классическое приложение (консольное)           | ![.NET Core и C#](media/sample-v2-code/logo_NETcore.png) | Последовательность кода устройства | | [dotnet-deviceprofile](https://github.com/Azure-Samples/active-directory-dotnet-deviceprofile)

## <a name="daemon-applications-accessing-web-apis-with-the-applications-identity"></a>Управляющая программа (которая обращается к веб-API с удостоверением приложения)

В следующих примерах показаны классические приложения и веб-приложения, которые обращаются к Microsoft Graph или веб-API без указания пользователя (с удостоверением приложения).

Клиентское приложение | платформа | Поток или предоставление | Вызывает веб-API ASP.NET или ASP.NET Core 2.0
------------------ | -------- | ---------- | -------------------- | -------------------------
Управляющая программа (консольная)          | ![.NET](media/sample-v2-code/logo_NETframework.png) | Учетные данные клиента с секретом приложения или сертификатом | [dotnet-daemon](https://github.com/azure-samples/active-directory-dotnet-daemon)</p> [dotnet-daemon-certificate-credential](https://github.com/azure-samples/active-directory-dotnet-daemon-certificate-credential)
Управляющая программа (консольная)         | ![.NET](media/sample-v2-code/logo_NETcore.png) | Учетные данные клиента с сертификатом| [dotnetcore-daemon-certificate-credential](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-certificate-credential)
Веб-приложение ASP.NET  | ![.NET](media/sample-v2-code/logo_NETframework.png) | Учетные данные клиента | [dotnet-webapp-webapi-oauth2-appidentity](https://github.com/Azure-Samples/active-directory-dotnet-webapp-webapi-oauth2-appidentity)

## <a name="web-apis"></a>Веб-API

### <a name="web-api-protected-by-azure-active-directory"></a>Веб-API, защищенный с помощью Azure Active Directory

В следующем примере показано, как защитить веб-API node.js с помощью Azure AD.

В предыдущих разделах этой статьи можно также найти другие примеры, в которых показано клиентское приложение, **обращающееся** к **веб-API** ASP.NET или ASP.NET Core. Эти примеры не упоминаются повторно в этом разделе, но их можно найти в последнем столбце таблиц выше или ниже.

платформа | Образец
 -------- | -------------------
![PHP](media/sample-v2-code/logo_nodejs.png)  | [node-webapi](https://github.com/Azure-Samples/active-directory-node-webapi)

### <a name="web-api-calling-microsoft-graph-or-another-web-api"></a>Веб-API, вызывающий Microsoft Graph или другой веб-API

В следующих примерах показан веб-API, который вызывает другой веб-API. Во втором примере показана организация условного доступа.

 платформа |  Вызывает Microsoft Graph | Вызывает другой веб-API ASP.NET или ASP.NET Core 2.0
 -------- |  --------------------- | -------------------------
![ASP.NET 4.5](media/sample-v2-code/logo_NETframework.png)<p/> ASP.NET 4.5 | [dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca) |[dotnet-webapi-onbehalfof](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof) <p/> [dotnet-webapi-onbehalfof-ca](https://github.com/azure-samples/active-directory-dotnet-webapi-onbehalfof-ca)

## <a name="other-microsoft-graph-samples"></a>Другие примеры Microsoft Graph

Дополнительные сведения о примерах и учебниках, в которых приводятся различные шаблоны использования API Microsoft Graph, включая аутентификацию в Azure AD, см. в статье [Примеры и учебники сообщества Microsoft Graph](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>См. также

[Руководство разработчика по Azure Active Directory](azure-ad-developers-guide.md)

[Библиотеки аутентификации Azure Active Directory](active-directory-authentication-libraries.md)

[Основные понятия и справочные материалы по Azure AD API Graph](https://msdn.microsoft.com/library/azure/hh974476.aspx)

[Библиотека вспомогательных методов для Azure AD Graph API](https://www.nuget.org/packages/Microsoft.Azure.ActiveDirectory.GraphClient)
