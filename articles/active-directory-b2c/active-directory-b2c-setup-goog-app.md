---
title: Настройка Google+ в Azure Active Directory B2C | Документация Майкрософт
description: Обеспечение регистрации и входа для пользователей с учетными записями Google+ в приложениях, защищенных с помощью Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fe5722e8d5a0a8a5bf172577777ccb899fb7b94d
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443432"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-google-accounts"></a>Azure Active Directory B2C: организация регистрации и входа для потребителей с учетными записями Google+
## <a name="create-a-google-application"></a>Создание приложения Google+
Чтобы использовать Google+ в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, необходимо сначала создать приложение Google+ и задать в нем правильные параметры. Для этого потребуется учетная запись Google+. Если у вас нет этой учетной записи, ее можно получить на сайте [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Перейдите на сайт [Google Developers Console](https://console.developers.google.com/) и выполните вход с учетной записью Google+.
2. Щелкните **Create project** (Создать проект), введите значение в поле **Project name** (Имя проекта) и щелкните **Create** (Создать).
   
    ![Google+: приступая к работе](./media/active-directory-b2c-setup-goog-app/google-get-started.png)
   
    ![Google+: создание проекта](./media/active-directory-b2c-setup-goog-app/google-new-project.png)
3. В левой области навигации щелкните **API Manager** (Менеджер API), а затем — **Credentials** (Учетные данные).
4. Щелкните вкладку **OAuth consent screen** (Экран согласия OAuth) вверху.
   
    ![Google+: учетные данные](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)
5. Выберите или укажите допустимый **электронный адрес**, заполните поле **Product name** (Название продукта) и нажмите кнопку **Save** (Сохранить).
   
    ![Google+: экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)
6. Щелкните **New credentials** (Создать учетные данные) и выберите **OAuth client ID** (Идентификатор клиента OAuth).
   
    ![Google+: экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)
7. Из списка **Application type** (Тип приложения) выберите **Web application** (Веб-приложение).
   
    ![Google+: экран согласия OAuth](./media/active-directory-b2c-setup-goog-app/google-web-app.png)
8. В поле **Name** (Имя) введите имя приложения, затем введите `https://login.microsoftonline.com` в поле **Authorized JavaScript origins** (Авторизованные источники JavaScript) и `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` — в поле **Authorized redirect URIs** (Авторизованные URI перенаправления). Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com). В значении **{клиент}** необходимо учитывать регистр. Щелкните **Create**(Создать).
   
    ![Google+: создание идентификатора клиента](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)
9. Скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Оба значения необходимы для настройки Google+ в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента** — это важные учетные данные безопасности.
   
    ![Google+: секрет клиента](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## <a name="configure-google-as-an-identity-provider-in-your-tenant"></a>Настройка Google+ в качестве поставщика удостоверений в клиенте
1. Выполните эти действия, чтобы [перейти к колонке функций B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) на портале Azure.
2. В колонке функций B2C щелкните **Поставщики удостоверений**.
3. Нажмите **+Добавить** в верхней части колонки.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите G+.
5. Щелкните **Тип поставщика удостоверений**, выберите **Google** и нажмите кнопку **ОК**.
6. Щелкните **Настроить этот поставщик удостоверений** и введите идентификатор клиента и секрет клиента ранее созданного приложения Google+.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Google+.

