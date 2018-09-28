---
title: Краткое руководство по Azure Active Directory версии 2 для JavaScript | Документация Майкрософт
description: Узнайте, как приложения JavaScript могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2.0.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/21/2018
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 4c64552ab23331755bf1d292bede61e78b339df0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987430"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Краткое руководство. Выполнение входа пользователей и получение маркера доступа из приложения JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Из этого краткого руководства вы узнаете, как использовать пример кода, который демонстрирует, как одностраничное приложение (SPA) JavaScript может выполнять вход с помощью личных, рабочих и учебных учетных записей, получать маркер доступа и вызывать API Microsoft Graph.

![Как работает пример приложения, созданный в этом кратком руководстве](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Регистрация и скачивание приложения, используемого в кратком руководстве
>
> ### <a name="step-1-register-your-application"></a>Шаг 1. Регистрация приложения
>
> 1. Перейдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/portal/register-app) для регистрации приложения.
> 1. В поле **Имя приложения** введите имя приложения.
> 1. Обязательно снимите флажок **Guided Setup** (Интерактивная настройка) и нажмите кнопку **Создать**.
> 1. Щелкните **Добавление платформы** и выберите **Веб**.
> 1. *Установите флажок* **Разрешить неявный поток**.
> 1. В поле **URL-адреса перенаправления** введите значение `http://localhost:30662/`.
> 1. Выберите команду **Сохранить**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Шаг 1. Настройка приложения на портале Azure
> Для работы примера кода в этом кратком руководстве необходимо добавить URL-адрес перенаправления `http://localhost:30662/` и включить **неявное предоставление разрешения**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Внести эти изменения для меня]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Уже настроено](media/quickstart-v2-javascript/green-check.png) Ваше приложение настроено с использованием этих атрибутов

#### <a name="step-2-download-the-project"></a>Шаг 2. Скачивание проекта

Вы можете выбрать один из этих вариантов, подходящий для вашей среды разработки.
* [Скачайте основные файлы проекта для веб-сервера, например Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip).
* [Скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)

Извлеките ZIP-файл в локальную папку (например, **C:\Azure-Samples**).

#### <a name="step-3-configure-your-javascript-app"></a>Шаг 3. Настройка приложения JavaScript

> [!div renderon="docs"]
> В файле `msalconfig.js` замените `Enter_the_Application_Id_here` идентификатором зарегистрированного приложения. *Идентификатор приложения* можно найти на странице *Обзор*.

> [!div class="sxs-lookup" renderon="portal"]
> В файле `msalconfig.js` замените msalconfig на:

```javascript
var msalconfig = {
    clientID: "Enter_the_Application_Id_here",
    redirectUri: location.origin
};
```
> [!NOTE]
> Если вы используете [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), URI перенаправления будет присвоено значение `http://localhost:30662/`, так как оно задано в проекте в примере кода. Если вы используете [Node.js](https://nodejs.org/en/download/) или любой другой веб-сервер, установите URI перенаправления как `http://localhost:30662/`, а затем настройте сервер, чтобы начать прослушивание этого порта.
>

#### <a name="step-4-run-the-project"></a>Шаг 4. Запуск проекта

Если вы используете Visual Studio, то для запуска проекта нажмите клавишу **F5**.

Если используется Node.js, в командной строке из каталога проекта можно выполнить следующую команду, чтобы запустить сервер:
 ```batch
 npm install
 node server.js
 ```
Откройте браузер и перейдите по ссылке `http://localhost:30662/`. Нажмите кнопку **Call Microsoft Graph API** (Вызвать API Microsoft Graph), чтобы начать процедуру входа.


## <a name="more-information"></a>Дополнительные сведения

### <a name="msaljs"></a>*msal.js*

MSAL — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, используемых для доступа к API, защищенному Microsoft Azure Active Directory (Azure AD). В файле *index.html*, используемом в кратком руководстве, содержится ссылка на библиотеку:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
````

Кроме того, если у вас установлен Node, можно загрузить его через npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Инициализация MSAL

В примере кода ниже показано, как инициализировать библиотеку:

```javascript
var userAgentApplication = new Msal.UserAgentApplication(msalconfig.clientID, null, loginCallback, {
    redirectUri: msalconfig.redirectUri
});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |Идентификатор приложения, зарегистрированного на портале Azure.|
> |`authority`    |Это URL-адрес центра. В случае передачи значения *null*, для центра по умолчанию устанавливается значение `https://login.microsoftonline.com/common`. Если вы используете однотенантное приложение (предназначенное для учетных записей только в одном каталоге), установите значение `https://login.microsoftonline.com/<tenant name or ID>`|
> |`loginCallBack`| Метод обратного вызова, вызываемый, когда механизм проверки подлинности перенаправляет обратно в приложение.|
> |`redirectUri`  |URL-адрес, по которому пользователи перенаправляются обратно после проверки подлинности с помощью Azure AD.|

### <a name="sign-in-users"></a>Выполнение входа пользователей

В приведенном ниже фрагменте кода показано, как выполнить вход пользователей:

```javascript
userAgentApplication.loginRedirect(graphAPIScopes);
```

> |Where  |  |
> |---------|---------|
> | `graphAPIScopes`   | (Необязательно.) Содержит области, для которых запрашивается согласие пользователя во время входа в систему (например, `[ "user.read" ]` для Microsoft Graph или `[ "api://<Application ID>/access_as_user" ]` для пользовательских веб-API). |

> [!TIP]
> При желании также можно использовать метод `loginPopup`, чтобы отобразить всплывающее окно для входа пользователя.

### <a name="request-tokens"></a>Запрос маркеров

MSAL имеет три метода получения маркеров: `acquireTokenRedirect`, `acquireTokenPopup` и `acquireTokenSilent`:

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `acquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После первого выполнения метода `loginRedirect` или `loginPopup` обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов. Вызовы для запроса или обновления маркеров выполняются автоматически.

```javascript
// Try to acquire the token used to query Graph API silently first:
userAgentApplication.acquireTokenSilent(graphAPIScopes)
```

> |Where  |  |
> |---------|---------|
> | `graphAPIScopes`   | Содержит запрашиваемые области, которые должны быть возвращены в маркере доступа для API (например, `[ "user.read" ]` для Microsoft Graph или `[ "api://<Application ID>/access_as_user" ]` для пользовательских веб-API). |

#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

 Иногда требуется настроить принудительное взаимодействие пользователей с конечной точкой Azure AD версии 2.0. Например: 
* Пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля.
* Ваше приложение запрашивает доступ к дополнительным областям ресурса, на обращение к которым пользователь должен дать согласие.
* Требуется двухфакторная проверка подлинности

Обычный рекомендуемый шаблон для большинства приложений — вызов `acquireTokenSilent`, перехват исключения, а затем вызов `acquireTokenRedirect` (или `acquireTokenPopup`) для запуска интерактивного запроса.

Вызов `acquireTokenRedirect(scope)` приводит к перенаправлению пользователей на конечную точку Azure AD версии 2.0 (или `acquireTokenPopup(scope)` приводит к отображению всплывающего окна), с которой пользователям нужно взаимодействовать, подтвердив свои учетные данные, дав согласие на доступ к требуемому ресурсу или выполнив двухфакторную проверку подлинности.

```javascript
userAgentApplication.acquireTokenRedirect(graphAPIScopes);
```

## <a name="next-steps"></a>Дополнительная информация

Более подробные инструкции по созданию приложения для этого краткого руководства можно найти в приведенном ниже руководстве по JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Подробнее о шагах, необходимых для создания приложения для этого краткого руководства

> [!div class="nextstepaction"]
> [Руководство по вызову API Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Ознакомление с документацией, часто задаваемыми вопросами, проблемами и многим другим в репозитории MSAL

> [!div class="nextstepaction"]
> [Репозиторий GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
