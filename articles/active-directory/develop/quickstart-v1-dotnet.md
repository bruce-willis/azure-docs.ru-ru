---
title: Вход пользователей и вызов API Microsoft Graph из классического приложения .NET (WPF) | Документация Майкрософт
description: Узнайте, как создать классическое приложение .NET Windows, которое интегрируется с Azure AD для входа в систему и вызывает программные интерфейсы приложения, защищенные Azure AD, по протоколу OAuth 2.0.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f9389a7c0e80f075c01f2236fa1bdf9dc9544ac6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987447"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Краткое руководство. Вход пользователей и вызов API Microsoft Graph из классического приложения .NET (WPF)

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Собственные клиенты .NET, которым необходим доступ к защищенным ресурсам, могут использовать библиотеку проверки подлинности Azure AD (ADAL), предоставляемую Azure Active Directory (Azure AD). ADAL упрощает доступ к маркерам доступа для вашего приложения. 

В этом кратком руководстве вы узнаете, как создать приложение списка задач для .NET WPF, которое:

* получает маркеры доступа для вызова интерфейса API Graph для Azure AD с помощью протокола проверки подлинности OAuth 2.0;
* Осуществляет поиск пользователей в каталоге по псевдониму.
* Обеспечивает функцию выхода пользователя из приложения.

Для создания полного рабочего приложения необходимо сделать следующее.

1. Зарегистрировать приложение в Azure AD.
2. Установить и настроить ADAL.
3. использовать ADAL для получения маркеров из Azure AD.

## <a name="prerequisites"></a>Предварительные требования

Чтобы начать работу, выполните следующие предварительные требования.

* [Загрузите каркас приложения](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/skeleton.zip) или [готовый пример](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip)
* Убедитесь в наличии клиента Azure AD, в котором можно создать пользователей и зарегистрировать приложение. Если клиента нет, [узнайте, как его получить](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>Шаг 1. Регистрация приложения DirectorySearcher

Чтобы приложение могло получать маркеры, зарегистрируйте его в клиенте Azure AD и предоставьте ему разрешение на доступ к интерфейсу API Graph для Azure AD.

1. Войдите на [портале Azure](https://portal.azure.com).
2. На верхней панели выберите учетную запись и в списке **Каталог** выберите клиент Active Directory, в котором хотите зарегистрировать приложение.
3. В меню навигации слева щелкните **Все службы** и выберите **Azure Active Directory**.
4. В **Регистрация приложений** выберите **Добавить**.
5. Следуйте инструкциям на экране, а затем создайте новое **Собственное** клиентское приложение.
    * **Имя** приложения будет описывать его конечным пользователям
    * **Uri перенаправления** представляет собой комбинацию схемы и строки, которую Azure AD будет использовать для возврата ответов на маркеры. Укажите значение, специфичное для вашего приложения, например `http://DirectorySearcher`.

6. После завершения регистрации служба Azure AD присваивает приложению уникальный идентификатор приложения. Это значение вам понадобится в следующих разделах, поэтому не забудьте скопировать его на странице приложения.
7. На странице **Параметры** выберите **Необходимые разрешения** и щелкните **Добавить**. Выберите **Microsoft Graph** в качестве интерфейса API и добавьте разрешение **Чтение данных каталога** в **Делегированные разрешения**. Установка этого разрешения позволит приложению запрашивать API Graph для пользователей.

## <a name="step-2-install-and-configure-adal"></a>Шаг 2. Установка и настройка ADAL

Теперь, когда приложение зарегистрировано в Azure AD, можно установить библиотеку ADAL и написать код для работы с удостоверением. Чтобы ADAL могла обмениваться информацией с Azure AD, необходимо предоставить некоторую информацию о регистрации вашего приложения.

1. Сначала добавьте ADAL в проект `DirectorySearcher` с помощью консоли диспетчера пакетов.

    ```
    PM> Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. В проекте `DirectorySearcher` откройте `app.config`.
1. Замените значения элементов в разделе `<appSettings>` на значения, введенные на портале Azure. Код будет использовать эти значения при каждом обращении к библиотеке ADAL.
  * `ida:Tenant` — это домен клиента Azure AD, например contoso.onmicrosoft.com
  * `ida:ClientId` — это идентификатор клиента приложения, скопированный с портала.
  * `ida:RedirectUri` — это URL-адрес перенаправления, зарегистрированный на портале.

## <a name="step-3-use-adal-to-get-tokens-from-azure-ad"></a>Шаг 3. Использование ADAL для получения маркеров из Azure AD

Основной принцип ADAL состоит в следующем: каждый раз, когда вашему приложению необходим маркер доступа, оно будет просто вызывать `authContext.AcquireTokenAsync(...)`, а ADAL сделает все остальное.

1. В проекте `DirectorySearcher` откройте `MainWindow.xaml.cs`.
1. Найдите метод `MainWindow()`. 
1. Инициализируйте `AuthenticationContext` приложения — основной класс ADAL. В `AuthenticationContext` вы отправляете в библиотеку ADAL координаты, которые ей требуются для взаимодействия с Azure AD, и сообщаете о способе кэширования маркеров.

    ```csharp
    public MainWindow()
    {
        InitializeComponent();

        authContext = new AuthenticationContext(authority, new FileCache());

        CheckForCachedToken();
    }
    ```

1. Найдите метод `Search(...)`, который будет вызываться при нажатии кнопки **Поиск** в пользовательском интерфейсе приложения. Этот метод выполняет запрос GET в интерфейс Graph API службы Azure AD для запроса списка пользователей, чьи UPN начинаются с данного слова поиска.
1. Чтобы запросить API Graph, включите access_token в заголовок запроса `Authorization`, в который входит ADAL.

    ```csharp
    private async void Search(object sender, RoutedEventArgs e)
    {
        // Validate the Input String
        if (string.IsNullOrEmpty(SearchText.Text))
        {
            MessageBox.Show("Please enter a value for the To Do item name");
            return;
        }

        // Get an Access Token for the Graph API
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Auto));
            UserNameLabel.Content = result.UserInfo.DisplayableId;
            SignOutButton.Visibility = Visibility.Visible;
        }
        catch (AdalException ex)
        {
            // An unexpected error occurred, or user canceled the sign in.
            if (ex.ErrorCode != "access_denied")
                MessageBox.Show(ex.Message);

            return;
        }

        ...
    }
    ```

    Когда приложение запрашивает маркер путем вызова `AcquireTokenAsync(...)`, библиотека ADAL пытается вернуть маркер без запроса учетных данных пользователя.
    * Если ADAL решит, что пользователь должен войти в систему для получения маркера, то служба отобразит диалоговое окно входа, соберет учетные данные пользователя и вернет маркер после успешной проверки подлинности. 
    * Если библиотеке ADAL не удастся по какой-либо причине вернуть маркер, она вызовет исключение `AdalException`.

1. Обратите внимание, что объект `AuthenticationResult` содержит объект `UserInfo`, который может использоваться для сбора сведений, необходимых приложению. В DirectorySearcher объект `UserInfo` используется для настройки пользовательского интерфейса приложения на основе идентификатора пользователя.
1. Когда пользователь нажимает кнопку **Выход**, необходимо, чтобы при следующем вызове `AcquireTokenAsync(...)` пользователю было предложено войти. Вы можете легко сделать это с помощью ADAL, очистив кэш маркера.

    ```csharp
    private void SignOut(object sender = null, RoutedEventArgs args = null)
    {
        // Clear the token cache
        authContext.TokenCache.Clear();

        ...
    }
    ```

    Если пользователь не нажимает кнопку **Выход**, нужно реализовать восстановление данных сеанса пользователя при следующем запуске DirectorySearcher. При запуске приложения можно проверить кэш маркеров библиотеки ADAL на наличие соответствующего маркера и соответственно обновить пользовательский интерфейс.

1. В методе `CheckForCachedToken()` выполните другой вызов `AcquireTokenAsync(...)`, на этот раз передав параметр `PromptBehavior.Never`. `PromptBehavior.Never` сообщает ADAL, что пользователю не следует предлагать войти, и вместо этого библиотека ADAL должна породить исключение, если не удается вернуть маркер.

    ```csharp
    public async void CheckForCachedToken() 
    {
        // As the application starts, try to get an access token without prompting the user. If one exists, show the user as signed in.
        AuthenticationResult result = null;
        try
        {
            result = await authContext.AcquireTokenAsync(graphResourceId, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never));
        }
        catch (AdalException ex)
        {
            if (ex.ErrorCode != "user_interaction_required")
            {
                // An unexpected error occurred.
                MessageBox.Show(ex.Message);
            }

            // If user interaction is required, proceed to main page without singing the user in.
            return;
        }

        // A valid token is in the cache
        SignOutButton.Visibility = Visibility.Visible;
        UserNameLabel.Content = result.UserInfo.DisplayableId;
    }
    ```

Поздравляем! Теперь у нас есть рабочее приложение .NET WPF, которое позволяет проверять подлинность пользователей, безопасно вызывать веб-API по протоколу OAuth 2.0 и получать основные сведения о пользователе. Если же вы этого еще не сделали, пришло время добавить в клиент нескольких пользователей. Запустите приложение DirectorySearcher и войдите как один из этих пользователей. Осуществите поиск других пользователей по их имени участника-пользователя. Закройте приложение и снова запустите его. Обратите внимание на то, что пользовательский сеанс остался без изменений. Выйдите и снова войдите под именем другого пользователя.

Библиотека ADAL упрощает включение этих общих компонентов идентификации в ваше приложение. Она выполняет за вас "грязную" работу, включая управление кэшем, поддержку протокола OAuth, предоставление пользователю диалогового окна входа, обновление маркеров безопасности с истекшим сроком действия и многое другое. Все, что вам действительно нужно знать, — это вызов интерфейса API `authContext.AcquireTokenAsync(...)`.

Для справки просмотрите готовый пример (без ваших значений конфигурации) [на GitHub](https://github.com/AzureADQuickStarts/NativeClient-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как защитить веб-API, используя маркеры доступа носителя OAuth 2.0.
> [!div class="nextstepaction"]
> [Защита веб-API с помощью Azure AD для .NET >>](quickstart-v1-dotnet-webapi.md)
