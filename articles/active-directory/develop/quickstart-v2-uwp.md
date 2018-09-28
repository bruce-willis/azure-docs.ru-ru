---
title: Краткое руководство. Универсальная платформа Windows для Azure Active Directory версии 2 | Документация Майкрософт
description: Здесь описывается, как приложение универсальной платформы Windows (XAML) может получить маркер доступа и вызвать API, защищенный конечной точкой Azure Active Directory версии 2.0.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 5241df87297fb2e293e2cc828821e66d6f2837b0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970833"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Вызов API Microsoft Graph из приложения для универсальной платформы Windows (UWP)

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

В этом кратком руководстве содержится пример кода, который демонстрирует, как приложение универсальной платформы Windows (UWP) может входить от имени пользователей в личные, рабочие, а также школьные учетные записи, получать маркер доступа и вызывать Microsoft Graph API.

![Как работает пример приложения, созданный в этом кратком руководстве](media/quickstart-v2-uwp/uwp-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Регистрация и загрузка
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Регистрация и настройка примера приложения и кода
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение, сделайте следующее:
> 1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
> 1. В поле **Имя приложения** введите имя приложения.
> 1. Обязательно снимите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.
> 1. Выберите **Добавление платформы**, **Собственное приложение**, а затем нажмите кнопку **Сохранить**.

> [!div renderon="portal" class="sxs-lookup alert alert-info"]
> #### <a name="step-1-configure-your-application"></a>Шаг 1. Настройка приложения
> Для работы примера кода в этом кратком руководстве необходимо добавить URL-адрес перенаправления в виде **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-uwp/green-check.png). Ваше приложение настроено с помощью этих атрибутов

#### <a name="step-2-download-your-visual-studio-project"></a>Шаг 2. Загрузка проекта Visual Studio

 - [Загрузить проект Visual Studio 2017](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Шаг 3. Настройка проекта Visual Studio

1. Извлеките ZIP-файл в локальную папку (например, **C:\Azure-Samples**).
1. Откройте проект в Visual Studio
1. Внесите изменения в **App.Xaml.cs** и замените строку, начинающуюся с `private static string ClientId`, на следующее.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="more-information"></a>Дополнительные сведения

Ниже приведен обзор этого краткого руководства.

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) — это библиотека, используемая для входа пользователей и запросов маркеров, используемых для доступа к API, защищенному Microsoft Azure Active Directory. Ее можно установить, выполнив в *консоли диспетчера пакетов* Visual Studio следующие команды.

```powershell
Install-Package Microsoft.Identity.Client -Pre
```

### <a name="msal-initialization"></a>Инициализация MSAL

Вы можете добавить ссылку на MSAL, добавив строку, приведенную ниже.

```csharp
using Microsoft.Identity.Client;
```

Затем выполните инициализацию MSAL с помощью строки, приведенной ниже.

```csharp
public static PublicClientApplication PublicClientApp = new PublicClientApplication(ClientId);
```

> |Описание ||
> |---------|---------|
> |ClientId | Идентификатор приложения, зарегистрированного на веб-сайте *portal.microsoft.com* |

### <a name="requesting-tokens"></a>Запрос маркеров

MSAL имеет два метода получения маркеров: `AcquireTokenAsync` и `AcquireTokenSilentAsync`.

#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

 В некоторых ситуациях необходимо принудительное взаимодействие пользователя с конечной точкой Azure Active Directory версии 2 через всплывающее окно, чтобы подтвердить их учетные данные или предоставить согласие. Некоторые примеры включают следующее.

- Первый вход пользователей в приложение.
- Пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля
- Ваше приложение запрашивает доступ к ресурсу, на обращение к которому пользователь должен дать согласие
- Требуется двухфакторная проверка подлинности

```csharp
authResult = await App.PublicClientApp.AcquireTokenAsync(scopes);
```

> |Описание||
> |---------|---------|
> |Области | Содержит запрашиваемые области (то есть `{ "user.read" }` для Microsoft Graph или `{ "api://<Application ID>/access_as_user" }` для пользовательских веб-API) |

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Вы не хотите заставлять пользователя подтверждать учетные данные каждый раз, когда ему нужно получить доступ к ресурсу (в большинстве случаев требуется получение и обновление маркеров без вмешательства пользователя). `AcquireTokenSilentAsync` — это метод, который обычно используется, чтобы получить маркеры для доступа к защищенным ресурсам после начального `AcquireTokenAsync`.

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
authResult = await App.PublicClientApp.AcquireTokenSilentAsync(scopes, accounts.FirstOrDefault());
```

> |Описание ||
> |---------|---------|
> |Области | Содержит запрашиваемые области (то есть `{ "user.read" }` для Microsoft Graph или `{ "api://<Application ID>/access_as_user" }` для пользовательских веб-API) |
> |accounts.FirstOrDefault() | Первый пользователь в кэше (MSAL поддерживает нескольких пользователей в одном приложении) |

## <a name="next-steps"></a>Дополнительная информация

В руководстве по Windows Desktop вы найдете пошаговые инструкции по созданию приложений и функций, в том числе полное описание того, о чем говорится в этом кратком руководстве.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Изучите этапы создания приложения, используемые в этом кратком руководстве

> [!div class="nextstepaction"]
> [Вызов API Microsoft Graph из приложения для универсальной платформы Windows (XAML)](tutorial-v2-windows-uwp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]