---
title: Настройка регистрации и входа с учетной записью Microsoft через Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Microsoft, используя Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 16e4dbac4c8146b048d4d9b76544677a6111e2a5
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900835"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Microsoft через Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Создание приложения для учетной записи Майкрософт

Чтобы использовать учетную запись Microsoft в качестве поставщика удостоверений для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи Microsoft, вы можете получить ее по адресу [https://www.live.com/](https://www.live.com/).

1. Войдите на [портал регистрации приложений Майкрософт](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) и войдите учетные данные от учетной записи Майкрософт.
2. В правом верхнем углу выберите **Добавить приложение**.
3. Укажите **имя** приложения и щелкните **Создать**.
4. На странице регистрации скопируйте значение **Идентификатор приложения**. Оно потребуется для настройки учетной записи Майкрософт в качестве поставщика удостоверений в вашем клиенте.
5. Щелкните **Add platform** (Добавить платформу) и выберите вариант **Веб**.
6. Введите значение `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` в поле **Redirect URLs** (URI перенаправления). Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com).
7. Выберите **Generate New Password** (Создать новый пароль) в разделе **Application Secrets** (Секреты приложения) Скопируйте новый пароль с экрана. Он необходим для настройки учетной записи Майкрософт в качестве поставщика удостоверений в вашем клиенте. Данный пароль — важный элемент обеспечения безопасности.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Настройка учетной записи Microsoft в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используется каталог с вашим клиентом Azure AD B2C, переключившись на него в правом верхнем углу окна портала Azure. Выберите сведения о подписке, а затем выберите **Переключение каталога**. 

    ![Переключение на клиент Azure AD B2C](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    Выберите каталог, содержащий ваш клиент.

    ![Выбор каталога](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Укажите **Имя**. Например, введите *MSA*.
6. Щелкните **Тип поставщика удостоверений**, выберите **Учетная запись Майкрософт** и щелкните **ОК**.
7. Выберите действие **Настроить этот поставщик удостоверений** и введите в поле **Идентификатор клиента** сохраненный идентификатор приложения, а в поле **Секрет клиента** — сохраненный пароль от приложения учетной записи Майкрософт, которое вы ранее создали.
8. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию учетной записи Майкрософт.

