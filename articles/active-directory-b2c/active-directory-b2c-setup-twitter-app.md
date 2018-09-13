---
title: Настройка регистрации и входа с учетной записью Twitter через Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Twitter, используя Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6d8e9245e95c08aad69cd05f338b6260e554469b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337796"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Twitter через Azure Active Directory B2C

## <a name="create-a-twitter-application"></a>Создание приложения Twitter

Чтобы использовать учетную запись Twitter в качестве поставщика удостоверений для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи Twitter, вы можете получить ее по адресу [https://twitter.com/signup](https://twitter.com/signup).

1. Перейдите на веб-сайт [Приложения Twitter](https://apps.twitter.com/) и выполните вход с учетными данными Twitter.
2. Выберите **Create New App** (Создать приложение).
3. Введите значения **Имя**, **Описание** и **Веб-сайт**.
4. Введите `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/{policyId}/oauth1/authresp` в поле **URL-адрес обратного вызова**. Замените **{tenant}** именем своего клиента (например, contosob2c), а **{policyId}** — идентификатором политики (например, b2c_1_policy). Добавьте URL-адрес обратного вызова для всех политик, использующих учетную запись Twitter. 
5. Подтвердите **Соглашение с разработчиком** и щелкните **Create your Twitter application** (Создать приложение Twitter).
7. Откройте вкладку **Ключи и токены доступа** .
8. Скопируйте значения **Consumer Key** (Ключ потребителя) и **Consumer Secret** (Секрет потребителя). Оба значения потребуются для настройки учетной записи Twitter в качестве поставщика удостоверений в вашем клиенте.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Настройка Twitter в качестве поставщика удостоверений в клиенте

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используется каталог с вашим клиентом Azure AD B2C, переключившись на него в правом верхнем углу окна портала Azure. Выберите сведения о подписке, а затем выберите **Переключение каталога**. 

    ![Переключение на клиент Azure AD B2C](./media/active-directory-b2c-setup-twitter-app/switch-directories.png)

    Выберите каталог, содержащий ваш клиент.

    ![Выбор каталога](./media/active-directory-b2c-setup-twitter-app/select-directory.png)

3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Укажите **имя**. Например, введите *Twitter*.
6. Щелкните **Тип поставщика удостоверений**, выберите **Twitter** и щелкните **ОК**.
7. Щелкните **Настроить этот поставщик удостоверений** и введите ключ потребителя в поле **Идентификатор клиента** и **секрет потребителя** в поле **Секрет клиента**.
8. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Twitter.