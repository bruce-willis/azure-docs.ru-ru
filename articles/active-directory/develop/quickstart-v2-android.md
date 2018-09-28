---
title: Краткое руководство. Azure Active Directory версии 2 для Android | Документация Майкрософт
description: В этой статье описано, как приложения Android могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2.0.
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
ms.openlocfilehash: 6eb06a2a4e83c9c293474f1692c2d33d4d0dfb36
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995743"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Краткое руководство. Вход пользователей и вызов Microsoft Graph API из приложения Android

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

В этом кратком руководстве содержится пример кода, который демонстрирует, как приложение Android может входить в личные или рабочие и учебные учетные записи, получать маркер доступа и вызывать API Microsoft Graph.

![Как работает пример приложения, созданный в этом кратком руководстве](media/quickstart-v2-android/android-intro.png)

> [!NOTE]
> **Предварительные требования**
> * Android Studio 3 или более поздней версии
> * Необходим пакет SDK для Android версии 21 или более поздней (советуем использовать пакет SDK версии 27).

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
> Чтобы образец кода для этого краткого руководства работал, вам нужно добавить следующий URL-адрес ответа: **msal {AppId}://auth** (где {AppId} — это идентификатор вашего приложения).
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести это изменение для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-android/green-check.png). Ваше приложение настроено с помощью этих атрибутов

#### <a name="step-2-download-the-project"></a>Шаг 2. Скачивание проекта

* [Скачайте проект Android Studio](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>Шаг 3. Настройка проекта

1. Извлеките и откройте проект в Android Studio.
1. В разделе **Приложение** > **java** > **<i>{узел}.{пространство имен}</i>**, откройте **MainActivity**.
1. Замените строку, начинающуюся с `final static String CLIENT_ID`, следующей:

    > [!div renderon="portal" class="sxs-lookup"]
    > ```java
    > final static String CLIENT_ID = "ENTER_THE_APPLICATION_ID_HERE";
    > ```

    > [!div renderon="docs"]
    > ```java
    > final static String CLIENT_ID = "<ENTER_THE_APPLICATION_ID_HERE>";
    > ```

1. Откройте: **Приложение** > **Манифесты** > **AndroidManifest.xml**.
1. Добавьте приведенное ниже действие в узел **manifest\application**. Этот фрагмент кода регистрирует **BrowserTabActivity**, чтобы позволить ОС возобновить свое приложение после завершения аутентификации.

    > [!div renderon="docs"]
    > ```xml
    > <!--Intent filter to capture System Browser calling back to our app after Sign In-->
    > <activity
    >     android:name="com.microsoft.identity.client.BrowserTabActivity">
    >     <intent-filter>
    >         <action android:name="android.intent.action.VIEW" />
    >         <category android:name="android.intent.category.DEFAULT" />
    >         <category android:name="android.intent.category.BROWSABLE" />
    > 
    >         <!--Add in your scheme/host from registered redirect URI-->
    >         <!--By default, the scheme should be similar to 'msal[appId]' -->
    >         <data android:scheme="msal<ENTER_THE_APPLICATION_ID_HERE>"
    >             android:host="auth" />
    >     </intent-filter>
    > </activity>
    > ```

    > [!div renderon="portal" class="sxs-lookup"]
    > ```xml
    > <!--Intent filter to capture System Browser calling back to our app after Sign In-->
    > <activity
    >     android:name="com.microsoft.identity.client.BrowserTabActivity">
    >     <intent-filter>
    >         <action android:name="android.intent.action.VIEW" />
    >         <category android:name="android.intent.category.DEFAULT" />
    >         <category android:name="android.intent.category.BROWSABLE" />
    > 
    >         <!--Add in your scheme/host from registered redirect URI-->
    >         <!--By default, the scheme should be similar to 'msal[appId]' -->
    >         <data android:scheme="msalENTER_THE_APPLICATION_ID_HERE"
    >             android:host="auth" />
    >     </intent-filter>
    > </activity>
    > ```

> [!div renderon="docs"]
> <span>6.</span> Замените `<ENTER_THE_APPLICATION_ID_HERE>` *идентификатором приложения* для вашего приложения. Если вам нужно найти *идентификатор приложения*, перейдите на страницу *Обзор*.

## <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения к этому краткому руководству.

### <a name="msal"></a>MSAL

MSAL ([com.microsoft.identity.client](http://javadoc.io/doc/com.microsoft.identity.client/msal)) — это библиотека, используемая для входа пользователей и запросов маркеров, которые используются для доступа к API, защищенному Microsoft Azure Active Directory (Azure AD). Вы можете использовать Gradle для его установки, добавив следующее в **Gradle Scripts** > **build.gradle (Module: app)** в разделе **Зависимости**.

```gradle  
implementation 'com.android.volley:volley:1.1.1'
implementation 'com.microsoft.identity.client:msal:0.1.+'
```

### <a name="msal-initialization"></a>Инициализация MSAL

Добавив следующий код, вы можете добавить ссылку на MSAL.

```java
import com.microsoft.identity.client.*;
```

Затем выполните инициализацию MSAL с помощью следующего кода.

```java
sampleApp = new PublicClientApplication(
        this.getApplicationContext(),
        CLIENT_ID);
```

> |Описание ||
> |---------|---------|
> |`CLIENT_ID` | Идентификатор зарегистрированного приложения на *portal.azure.com* |

### <a name="requesting-tokens"></a>Запрос маркеров

MSAL имеет два метода получения маркеров: `acquireToken` и `acquireTokenSilentAsync`

#### <a name="getting-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

Некоторые ситуации требуют принуждения пользователей к взаимодействию с конечной точкой Azure Active Directory версии 2.0, что приводит к переключению контекста в системный браузер для проверки учетных данных пользователей или для получения согласия. Некоторые примеры:

* Первый вход пользователей в приложение.
* Пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля.
* Ваше приложение запрашивает доступ к ресурсу, на обращение к которому пользователь должен дать согласие.
* Требуется двухфакторная проверка подлинности.

```java
sampleApp.acquireToken(this, SCOPES, getAuthInteractiveCallback());
```

> |Описание||
> |---------|---------|
> | `SCOPES` | Содержит запрашиваемые области (то есть `{ "user.read" }` для Microsoft Graph или `{ "<Application ID URL>/scope" }` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Обратный вызов выполняется, когда управление возвращается в приложение после проверки подлинности. |

#### <a name="getting-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Вы не хотите требовать от пользователя проверки своих учетных данных каждый раз, когда им необходимо получить доступ к ресурсу. Большую часть времени вы хотите приобретать и обновлять маркеры без какого-либо взаимодействия с пользователем. Можно использовать метод `AcquireTokenSilentAsync`, чтобы получить маркеры для доступа к защищенным ресурсам после первоначального метода `acquireToken`.

```java
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

> |Описание||
> |---------|---------|
> | `SCOPES` | Содержит запрашиваемые области (то есть `{ "user.read" }` для Microsoft Graph или `{ "<Application ID URL>/scope" }` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`) |
> | `getAuthInteractiveCallback` | Обратный вызов выполняется, когда управление возвращается в приложение после проверки подлинности. |

## <a name="next-steps"></a>Дополнительная информация

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>Изучите этапы создания приложения, используемые в этом кратком руководстве

В руководстве по Android вы найдете пошаговые инструкции для создания приложений и функций, а также полное описание того, о чем говорится в этом кратком руководстве.

> [!div class="nextstepaction"]
> [Вход пользователей и вызов API Microsoft Graph из приложения Android](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-android)

### <a name="msal-for-android-library-wiki"></a>MSAL для вики-сайта библиотеки Android

Дополнительные сведения см. в статье

> [!div class="nextstepaction"]
> [MSAL для вики-сайта библиотеки Android](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]