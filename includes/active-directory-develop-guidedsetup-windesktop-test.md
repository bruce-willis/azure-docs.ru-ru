---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: d333f8ecd7e1044575f570d893227f9dcb394974
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843452"
---
## <a name="test-your-code"></a>Тестирование кода

Чтобы запустить проект в Visual Studio, нажмите клавишу **F5**. Отобразится приложение **MainWindow**, как показано ниже:

![Тестирование приложения](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

При первом запуске приложения и нажатии кнопки **Call Microsoft Graph API** (Вызов API Graph Microsoft) появится запрос на вход. Используйте учетную запись Azure Active Directory (рабочую или учебную) или учетную запись Майкрософт (live.com, outlook.com) для тестирования приложения.

![Вход в приложение](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Предоставление разрешения на доступ к приложению
Войдя в приложение первый раз, необходимо будет предоставить ему разрешение на использование данных вашего профиля для входа, как показано ниже: 

![Предоставление разрешения на доступ к приложению](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Просмотр результатов приложения
После входа вы увидите сведения о профиле пользователя, который возвращается вызовом Microsoft Graph API. Результаты отображаются в поле **API Call Results** (Результаты вызова API). Основные сведения о токене, полученном при вызове `AcquireTokenAsync` или `AcquireTokenSilentAsync`, должны отображаться в поле **Token Info** (Сведения о токене). Результаты содержат следующие свойства:

|Свойство  |Формат  |ОПИСАНИЕ |
|---------|---------|---------|
|**Имя** |Полное имя пользователя |Имя и фамилия пользователя.|
|**Имя пользователя** |<span>user@domain.com</span> |Имя пользователя, которое используется для идентификации пользователя.|
|**Истечение срока действия маркера** |Datetime |Время окончания срока действия маркера. MSAL продлевает срок действия, по мере необходимости обновляя маркер.|
|**Маркер доступа** |Строка |Строка маркера, которая отправляется к HTTP-запросам, требующим *Заголовок авторизации*.|

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область *user.read*. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации приложений. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Для отображения списка календарей пользователя API Microsoft Graph требуется область *Calendars.Read*.

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение *Calendars.Read* в сведения о регистрации приложения. Затем добавьте область *Calendars.Read* в вызов `acquireTokenSilent`. 

>[!NOTE]
>При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
