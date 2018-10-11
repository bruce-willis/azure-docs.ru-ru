---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 3cbcae9c02abaf11ded31304f97abdd74f905c6e
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48842916"
---
## <a name="test-your-code"></a>Тестирование кода

### <a name="test-with-node"></a>Тестирование кода в Node
Если вы не используете Visual Studio, убедитесь, что веб-сервер запущен.
1. Настройте для сервера ожидание передачи данных через TCP-порт на основе расположения файла **index.html**. Чтобы запустить веб-сервер для ожидания передачи данных через порт в Node, выполните следующие команды в командной строке из папки приложения:

    ```bash
    npm install
    node server.js
    ```
1. Откройте браузер и введите http://<span></span>localhost:30662 или http://<span></span>localhost:{port}, где **port** соответствует порту, с которого веб-сервер ожидает передачи данных. Появится содержимое файла index.html с кнопкой **Войти**.

<p/><!-- -->

### <a name="test-with-visual-studio"></a>Тестирование кода в Visual Studio
Если вы используете Visual Studio, выберите решение проекта и нажмите клавишу **F5**, чтобы запустить проект. В браузере откроется адрес http://<span></span>localhost:{port} и отобразится кнопка **Войти**.


## <a name="test-your-application"></a>Тестирование приложения

После загрузки файла index.html в браузер щелкните **Войти**. Вам будет предложено войти, используя конечную точку Microsoft Azure Active Directory (Azure AD) версии 2.0.

![Вход в учетную запись JavaScript SPA](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)


### <a name="provide-consent-for-application-access"></a>Предоставление разрешения на доступ к приложению

После первого входа в приложение предоставьте ему разрешение на использование данных вашего профиля для входа:

![Предоставление разрешения на доступ к приложению](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Просмотр результатов приложения
После входа вы увидите сведения о профиле пользователя, полученные из ответа API Microsoft Graph.

![Ожидаемые результаты после вызова Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область `user.read`. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Например, чтобы отобразился список календарей пользователя, API Microsoft Graph требуется область `Calendars.Read`.

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение `Calendars.Read` в сведения о регистрации приложения. Затем добавьте область `Calendars.Read` в вызов `acquireTokenSilent`.

>[!NOTE]
>При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

Если API серверной части не требуется область (не рекомендуется), вы можете использовать `clientId` в качестве области в вызовах для получения маркеров.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
