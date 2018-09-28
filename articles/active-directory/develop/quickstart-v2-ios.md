---
title: Краткое руководство. Azure Active Directory версии 2 для iOS | Документация Майкрософт
description: Узнайте, как осуществлять вход пользователям и запрашивать Microsoft Graph в собственном приложении iOS.
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
ms.date: 09/23/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 49ced3277a659ddacef239c7a1394cbe5ce06ac9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973615"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-native-app"></a>Краткое руководство. Вход пользователей и вызов Microsoft Graph API из собственного приложения iOS

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

В этом кратком руководстве содержится пример кода, который демонстрирует, как собственное приложение iOS может входить в личные, рабочие и школьные учетные записи, получать маркер доступа и вызывать Microsoft Graph API.

![Как работает пример приложения, созданный в этом кратком руководстве](media/quickstart-v2-ios/ios-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>Регистрация и загрузка
> ### <a name="register-and-configure-your-application-and-code-sample"></a>Регистрация и настройка примера приложения и кода
> #### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
> Чтобы зарегистрировать приложение и добавить сведения о его регистрации в решение, сделайте следующее:
> 1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
> 1. В поле **Имя приложения** введите имя приложения.
> 1. Обязательно снимите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.
> 1. Выберите **Добавление платформы**, **Собственное приложение**, а затем нажмите кнопку **Сохранить**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Шаг 1. Настройка приложения
> Для работы примера кода в этом кратком руководстве необходимо добавить URL-адрес ответа `msal<AppId>://auth` (где msal<AppId> является идентификатором этого приложения).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-ios/green-check.png). Ваше приложение настроено с помощью этого атрибута

#### <a name="step-2-download-your-web-server-or-project"></a>Шаг 2. Загрузка веб-сервера или проекта

- [Загрузка проекта XCode](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Шаг 3. Настройка проекта

1. Извлеките ZIP-файл и откройте проект в XCode.
1. Измените **ViewController.swift** и замените строку, начинающуюся с "let kClientID", следующим фрагментом кода.

    > [!div renderon="portal" class="sxs-lookup"]
    > ```swift
    > let kClientID = "Enter_the_Application_Id_here"
    > ```

    > [!div renderon="docs"]
    > ```swift
    > let kClientID = "<ENTER_THE_APPLICATION_ID_HERE>"
    > ```   
1. Нажмите элемент управления и щелкните **Info.plist** для вызова контекстного меню, а затем выберите **Открыть как** > **Исходный код**.
1. Добавьте следующий код в корневой узел словаря.

    > [!div renderon="portal" class="sxs-lookup"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msalEnter_the_Application_Id_here</string>
    >         </array>
    >     </dict>
    > </array>
    > ```

    > [!div renderon="docs"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msal<ENTER_THE_APPLICATION_ID_HERE></string>
    >         </array>
    >     </dict>
    > </array>
    > ```
    
> [!div renderon="docs"]
> <span>5.</span> Замените `<ENTER_THE_APPLICATION_ID_HERE>` *идентификатором приложения* для вашего приложения. Если вам нужно найти *идентификатор приложения*, перейдите на страницу *Обзор*.

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения о кратком руководстве содержатся в этих разделах.

### <a name="msal"></a>MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) — это библиотека, используемая для входа пользователей и запросов маркеров безопасности для доступа к API, защищенному Microsoft Azure Active Directory. MSAL можно добавить в приложение, используя следующее действие.

```
$ vi Podfile
```
Добавьте в файл Podfile следующий код:

```
 target 'QuickStart' do
   use_frameworks!
 pod 'MSAL'
 end
```

### <a name="msal-initialization"></a>Инициализация MSAL

Добавив следующий код, вы можете добавить ссылку на MSAL.

```swift
import MSAL
```

Затем выполните инициализацию MSAL с помощью следующего кода.

```swift
let authority = MSALAuthority(url: URL(string: kAuthority)!)
self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)
```

> |Описание ||
> |---------|---------|
> | `clientId` | Идентификатор зарегистрированного приложения на *portal.azure.com* |
> | `authority` | Конечная точка Azure AD версии 2.0. В большинстве случаев это будет *https<span/>://login.microsoftonline.com/common* |

### <a name="requesting-tokens"></a>Запрос маркеров

MSAL имеет два метода получения маркеров безопасности: `acquireToken` и `acquireTokenSilent`.

#### <a name="getting-an-access-token-interactively"></a>Интерактивное получение маркера доступа

Некоторые ситуации требуют принуждения пользователей взаимодействовать с конечной точкой Azure Active Directory (Azure AD) версии 2.0, что приведет к переключению контекста в системный браузер для проверки учетных данных пользователей или для получения согласия. Некоторые примеры:

* Первый вход пользователей в приложение.
* Пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля.
* Ваше приложение запрашивает доступ к ресурсу, на обращение к которому пользователь должен дать согласие.
* Требуется двухфакторная проверка подлинности.

```swift
applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in /* Add your handling logic */}
```

> |Описание||
> |---------|---------|
> | `forScopes` | Содержит запрашиваемую область (то есть ["user.read"]` for Microsoft Graph or `["<Application ID URL>/область"]` for custom Web APIs (i.e. `api://<Application ID>/access_as_user")) |

#### <a name="getting-an-access-token-silently"></a>Автоматическое получение маркера доступа

Вы не хотите требовать от пользователя проверки своих учетных данных каждый раз, когда ему необходимо получить доступ к ресурсу. Большую часть времени вы хотите приобретать и обновлять маркеры без какого-либо взаимодействия с пользователем. Можно использовать метод `acquireTokenSilent`, чтобы получить маркеры безопасности для доступа к защищенным ресурсам после первоначального метода `acquireToken`.

```swift
applicationContext.acquireTokenSilent(forScopes: self.kScopes, account: applicationContext.allAccounts().first) { (result, error) in /* Add your handling logic */}
```

> |Описание ||
> |---------|---------|
> | `forScopes` | Содержит запрашиваемые области (то есть `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`)) |
> | `account` | Учетная запись, запрашивающая маркер безопасности (MSAL поддерживает несколько учетных записей в одном приложении). В случае с этим кратким руководством значение указывает на первую учетную запись в кэше (`applicationContext.allAccounts().first`). |

## <a name="next-steps"></a>Дополнительная информация

В руководстве по iOS вы найдете пошаговые инструкции по созданию приложений и функций, а также полное описание того, о чем говорится в этом кратком руководстве.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Изучите этапы создания приложения, используемые в этом кратком руководстве

> [!div class="nextstepaction"]
> [Вызов API Microsoft Graph из приложения iOS](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
