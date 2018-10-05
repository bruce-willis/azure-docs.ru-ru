---
title: Краткое руководство по Azure Active Directory версии 2 для JavaScript | Документация Майкрософт
description: Узнайте, как приложения JavaScript могут вызывать API, которому необходимы маркеры доступа, с помощью конечной точки Azure Active Directory версии 2.0.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 1b884571707aab71e8a8d124ba68f938e5a63a43
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063750"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Краткое руководство. Выполнение входа пользователей и получение маркера доступа из приложения JavaScript

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Из этого краткого руководства вы узнаете, как использовать пример кода, который демонстрирует, как одностраничное приложение (SPA) JavaScript может выполнять вход с помощью личных, рабочих и учебных учетных записей, а также получать маркер доступа, чтобы вызывать API Microsoft Graph или любой другой веб-API.

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
* [Скачайте основные файлы проекта для веб-сервера, например Node.js](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
* [Скачайте проект Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Извлеките ZIP-файл в локальную папку (например, **C:\Azure-Samples**).

#### <a name="step-3-configure-your-javascript-app"></a>Шаг 3. Настройка приложения JavaScript

> [!div renderon="docs"]
> В файле `index.html` замените `Enter_the_Application_Id_here` в разделе `applicationConfig` идентификатором зарегистрированного приложения.

> [!div class="sxs-lookup" renderon="portal"]
> В файле `index.html` измените `applicationConfig` на:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!NOTE]
>Если вы используете [Node.js](https://nodejs.org/en/download/), файл *server.js* настраивается таким образом, чтобы сервер ожидал передачи данных через порт 30662.
> Если вы используете [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), файл *.csproj* из примера кода настраивается таким образом, чтобы сервер ожидал передачи данных через порт 30662.
>

#### <a name="step-4-run-the-project"></a>Шаг 4. Запуск проекта

Если используется Node.js, в командной строке из каталога проекта можно выполнить следующую команду, чтобы запустить сервер:
 ```batch
 npm install
 node server.js
 ```
Откройте браузер и перейдите по ссылке `http://localhost:30662/`. Нажмите кнопку **Войти**, чтобы войти в систему и вызывать API Microsoft Graph.

Если вы используете Visual Studio, выберите решение проекта и нажмите клавишу **F5**, чтобы запустить проект.

## <a name="more-information"></a>Дополнительные сведения

### <a name="msaljs"></a>*msal.js*

MSAL — это библиотека, используемая для выполнения входа пользователей и запросов маркеров, используемых для доступа к API, защищенному Microsoft Azure Active Directory (Azure AD). В файле *index.html*, используемом в кратком руководстве, содержится ссылка на библиотеку:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Кроме того, если у вас установлен Node, можно загрузить его через npm:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Инициализация MSAL

В примере кода ниже показано, как инициализировать библиотеку:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Where  |  |
> |---------|---------|
> |`ClientId`     |Идентификатор приложения, зарегистрированного на портале Azure.|
> |`authority`    |Это URL-адрес центра. В случае передачи значения *null*, для центра по умолчанию устанавливается значение `https://login.microsoftonline.com/common`. Если вы используете однотенантное приложение (предназначенное для учетных записей только в одном каталоге), установите значение `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Метод обратного вызова, вызываемый, когда механизм аутентификации выполняет перенаправление обратно в приложение. Здесь передается `acquireTokenRedirectCallBack`. При использовании loginPopup он имеет значение NULL.|
> |`options`  |Коллекция необязательных параметров. В этом случае `storeAuthStateInCookie` и `cacheLocation` являются необязательными настройками. Дополнительные сведения о параметрах см. на [этом вики-сайте](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options). |

### <a name="sign-in-users"></a>Выполнение входа пользователей

В приведенном ниже фрагменте кода показано, как выполнить вход пользователей:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | (Необязательно) Содержит области, для которых запрашивается согласие пользователя во время входа в систему, например `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`). Здесь передается `applicationConfig.graphScopes`. |

> [!TIP]
> Также можно использовать метод `loginRedirect`, чтобы с текущей страницы выполнялось перенаправление на страницу входа вместо отображения всплывающего окна.


### <a name="request-tokens"></a>Запрос маркеров

MSAL имеет три метода получения маркеров: `acquireTokenRedirect`, `acquireTokenPopup` и `acquireTokenSilent`:

#### <a name="get-a-user-token-silently"></a>Автоматическое получение маркера пользователя

Метод `acquireTokenSilent` обрабатывает получение и обновление маркера без участия пользователя. После первого выполнения метода `loginRedirect` или `loginPopup` обычно используется метод `acquireTokenSilent`, чтобы получить маркеры для доступа к защищенным ресурсам для последующих вызовов. Вызовы для запроса или обновления маркеров выполняются автоматически.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Where  |  |
> |---------|---------|
> | `scopes`   | Содержит запрашиваемые области, которые должны быть возвращены в маркере доступа для API, например `[ "user.read" ]` для Microsoft Graph или `[ "<Application ID URL>/scope" ]` для пользовательских веб-API (т. е. `api://<Application ID>/access_as_user`). Здесь передается `applicationConfig.graphScopes`.|

#### <a name="get-a-user-token-interactively"></a>Интерактивное получение маркера пользователя

 Иногда требуется настроить принудительное взаимодействие пользователей с конечной точкой Azure AD версии 2.0. Например: 
* Пользователям может потребоваться повторно ввести учетные данные, так как истек срок действия пароля.
* Ваше приложение запрашивает доступ к дополнительным областям ресурса, на обращение к которым пользователь должен дать согласие.
* Требуется двухфакторная проверка подлинности

Обычный рекомендуемый шаблон для большинства приложений — вызов `acquireTokenSilent`, перехват исключения, а затем вызов `acquireTokenRedirect` (или `acquireTokenPopup`) для запуска интерактивного запроса.

После вызова `acquireTokenPopup(scope)` отображается всплывающее окно для входа (или `acquireTokenRedirect(scope)` перенаправляет пользователей на конечную точку Azure AD версии 2.0), где пользователям нужно подтвердить свои учетные данные, предоставить согласие на доступ к требуемому ресурсу или пройти двухфакторную аутентификацию.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> В этом кратком руководстве для Internet Explorer используются методы `loginRedirect` и `acquireTokenRedirect` из-за [известной проблемы](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) с обработкой всплывающих окон в Internet Explorer.

## <a name="next-steps"></a>Дополнительная информация

Более подробные инструкции по созданию приложения для этого краткого руководства можно найти в приведенном ниже руководстве по JavaScript.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Подробнее о шагах, необходимых для создания приложения для этого краткого руководства

> [!div class="nextstepaction"]
> [Руководство по вызову API Graph](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Ознакомление с документацией, часто задаваемыми вопросами, проблемами и многим другим в репозитории MSAL

> [!div class="nextstepaction"]
> [Репозиторий GitHub msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
