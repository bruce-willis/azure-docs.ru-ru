---
title: Настройка регистрации и входа с учетной записью WeChat в Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями WeChat при помощи Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 418f399213f2b1b60d4d4f464fd9fe89c6f35ba7
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338213"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью WeChat в Azure Active Directory B2C

> [!NOTE]
> Эта функция предоставляется в предварительной версии.
> 

## <a name="create-a-wechat-application"></a>Создание приложения WeChat

Чтобы использовать учетную запись WeChat в качестве поставщика удостоверений для Azure Active Directory (Azure AD) B2C, необходимо создать в своем клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи WeChat, см. страницу [http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html](http://kf.qq.com/faq/161220Brem2Q161220uUjERB.html).

### <a name="register-a-wechat-application"></a>Регистрация приложения WeChat

1. Войдите в [https://open.weixin.qq.com/](https://open.weixin.qq.com/) с учетными данными WeChat.
2. Выберите **管理中心** (Центр управления).
3. Выполните отображаемые инструкции, чтобы зарегистрировать новое приложение.
4. Введите значение `https://{tenant_name}.b2clogin.com/te/{tenant_name}.onmicrosoft.com/oauth2/authresp` в поле **授权回调域** (URL-адрес обратного вызова). Например, если `tenant_name` — contoso, задайте URL-адрес `https://contoso.b2clogin.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
5. Скопируйте **идентификатор приложения** и **ключ приложения**. Эти значения потребуются при добавлении поставщика удостоверений для вашего клиента.

## <a name="configure-wechat-as-an-identity-provider-in-your-tenant"></a>Настройка WeChat в качестве поставщика удостоверений в клиенте

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используется каталог с вашим клиентом Azure AD B2C, переключившись на него в правом верхнем углу окна портала Azure. Выберите сведения о подписке, а затем выберите **Переключение каталога**. 

    ![Переключение на клиент Azure AD B2C](./media/active-directory-b2c-setup-wechat-app/switch-directories.png)

    Выберите каталог, содержащий ваш клиент.

    ![Выбор каталога](./media/active-directory-b2c-setup-wechat-app/select-directory.png)

3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Укажите **имя**. Например, введите *WeChat*.
6. Щелкните **Тип поставщика удостоверений**, выберите **WeChat (Preview)** (WeChat (предварительная версия)) и щелкните **ОК**.
7. Выберите **Настроить этот поставщик удостоверений** и введите в поле **Идентификатор клиента** сохраненный идентификатор приложения, а в поле **Секрет клиента** — сохраненный ключ приложения WeChat, которое вы создали ранее.
8. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию WeChat.

