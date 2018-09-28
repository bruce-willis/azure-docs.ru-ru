---
title: Вход пользователей и вызов API Microsoft Graph из приложения Android | Документация Майкрософт
description: Узнайте, как регистрировать пользователей для входа в систему и вызывать API Microsoft Graph из приложения Android.
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: c3ab241e42c431ae4e95e8154343a949bb9e596e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970184"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-android-app"></a>Краткое руководство. Вход пользователей и вызов API Microsoft Graph из приложения Android

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Корпорация Майкрософт предлагает простые и понятные средства регистрации пользователей Azure Active Directory (Azure AD) для разработчиков приложений для Android. Azure AD позволяет вашему приложению обращаться к данным пользователя через Microsoft Graph или с помощью собственного защищенного веб-API.

Библиотека аутентификации Azure AD (ADAL) для Android позволяет вашему приложению использовать [Microsoft Azure Cloud](https://cloud.microsoft.com) & [API Microsoft Graph](https://graph.microsoft.io) благодаря включенной поддержке [учетных записей Microsoft Azure Active Directory](https://azure.microsoft.com/services/active-directory/) с использованием отраслевых стандартов OAuth версии 2.0 и OpenID Connect.

В этом кратком руководстве описано следующее:

* Получение маркера для Microsoft Graph
* Обновление маркера
* Вызов Microsoft Graph
* Выход пользователя из системы

## <a name="prerequisites"></a>Предварительные требования

Чтобы приступить к работе, вам потребуется клиент Azure AD, в котором вы сможете создать пользователей и зарегистрировать приложение. Если клиента нет, [узнайте, как его получить](quickstart-create-new-tenant.md).

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>Сценарий: вход пользователей и вызов Microsoft Graph

![Топология](./media/quickstart-v1-android/active-directory-android-topology.png)

Это приложение можно использовать для всех учетных записей Azure AD. Оно поддерживает сценарии как с одним, так и с несколькими клиентами (как описано ниже). Вы сможете создавать приложения для подключения к корпоративным пользователям и доступа к их данным в Azure и Office 365 с помощью Microsoft Graph. При проверке подлинности конечным пользователям потребуется войти и подтвердить разрешения для приложения, в некоторых случаях разрешения должен подтвердить системный администратор. Большая часть логики в этом примере иллюстрирует проверку подлинности конечного пользователя и простой вызов Microsoft Graph.

## <a name="sample-code"></a>Пример кода

Полный исходный код примера можно найти на сайте [GitHub](https://github.com/Azure-Samples/active-directory-android).

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="step-1-register-and-configure-your-app"></a>Шаг 1. Регистрация и настройка приложения

Вам потребуется собственное клиентское приложение, зарегистрированное в Майкрософт с помощью [портала Azure](https://portal.azure.com).

1. Регистрация приложения
    - Перейдите на [портал Azure](https://aad.portal.azure.com).
    - Выберите ***Azure Active Directory*** > ***Регистрация приложений***.

2. Создание приложения
    - Выберите **Регистрация нового приложения**.
    - Введите имя приложения в поле **Имя**.
    - В поле **Тип приложения** выберите **Собственное**.
    - В поле **URI перенаправления** введите `http://localhost`.

3. Настройка Microsoft Graph
    - Выберите **Параметры > Требуемые разрешения**.
    - Выберите **Добавить**, затем в разделе **Выберите API** укажите ***Microsoft Graph***.
    - Выберите разрешение **Вход и чтение профиля пользователя**, затем нажмите кнопку **Выбрать**, чтобы сохранить разрешение.
        - Это разрешение будет сопоставлено с областью `User.Read`.
    - Необязательно: в разделе **"Требуемые разрешения" > "Microsoft Azure Active Directory"** снимите выбранное разрешение **Вход и чтение профиля пользователя**. Это позволит избежать того, чтобы разрешение на странице согласия пользователя отображалось два раза.

4. Поздравляем! Ваше приложение настроено. В следующем разделе вам потребуется:
    - `Application ID`
    - `Redirect URI`

## <a name="step-2-get-the-sample-code"></a>Шаг 2. Получение кода примера

1. Скопируйте код.
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. Откройте пример в Android Studio.
    - Выберите **Открыть существующий проект Android Studio**.

## <a name="step-3-configure-your-code"></a>Шаг 3. Настройка кода

Все настройки для этого примера кода можно найти в файле ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java***.

1. Замените константу `CLIENT_ID` на `ApplicationID`.
2. Замените константу `REDIRECT URI` на указанное ранее значение `Redirect URI` (`http://localhost`).

## <a name="step-4-run-the-sample"></a>Шаг 4. Запуск примера

1. Выберите **"Сборка" > "Очистить проект"**.
2. Выберите **"Запуск" > "Запустить приложение"**.
3. Будет выполнена сборка приложения. После этого откроется приложение с базовым пользовательским интерфейсом. При нажатии кнопки `Call Graph API` будет отправлен запрос на вход, а затем выполнен вызов API Microsoft Graph с новым маркером.

## <a name="next-steps"></a>Дополнительная информация

1. Дополнительные сведения о механизме работы библиотеки и подготовке новых сценариев и возможностей см. на [вике-сайте по ADAL для Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki).
2. В сценариях с машинным кодом приложение будет использовать встроенное веб-представление и не выйдет за пределы приложения. `Redirect URI` может быть произвольным.
3. Возникли проблемы или вопросы? Сообщите о проблеме или разместите свой вопрос на сайте Stackoverflow с тегом `azure-active-directory`.

### <a name="cross-app-sso"></a>Единый вход для нескольких приложений

Узнайте, [как включить единый вход для нескольких приложений Android с помощью ADAL](howto-v1-enable-sso-android.md).

### <a name="auth-telemetry"></a>Данные телеметрии для проверки подлинности

Библиотека ADAL предоставляет данные телеметрии для аутентификации подлинности, с помощью которых разработчики приложений могут понять, как работают приложения и как улучшить их работу. Это позволит вам определять успешные события входа, активных пользователей и другие важные сведения. Для использования данных телеметрии для проверки подлинности разработчики приложений должны создать службу телеметрии, которая будет собирать и хранить события.

Дополнительные сведения о данных телеметрии для проверки подлинности см. в разделе [Данные телеметрии для проверки подлинности в библиотеке ADAL для Android](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry).