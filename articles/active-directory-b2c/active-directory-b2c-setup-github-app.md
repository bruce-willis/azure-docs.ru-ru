---
title: Настройка регистрации и входа с учетной записью GitHub через Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями GitHub, используя Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 88fffd28319101c112f848eebc6e8ee27f7f863e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952024"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью GitHub через Azure Active Directory B2C

> [!NOTE]
> Эта функция предоставляется в предварительной версии.
> 

Чтобы использовать учетную запись Github в качестве поставщика удостоверений для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи GitHub, вы можете получить ее по адресу [https://www.github.com/](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Создание приложения OAuth GitHub

1. Войдите на сайт [разработчика GitHub](https://github.com/settings/developers), используя учетные данные GitHub.
2. Выберите **Приложения OAuth**, а затем щелкните **Зарегистрировать новое приложение**.
3. Заполните поля **Application name** (Имя приложения) и **Homepage URL** (URL-адрес домашней страницы).
4. Введите значение `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` в поле **Authorization callback URL** (URL-адрес обратного вызова авторизации). Замените **{tenant}** именем своего клиента Azure AD B2C (например, contosob2c.onmicrosoft.com).
5. Щелкните **Register application** (Зарегистрировать приложение).
6. Скопируйте значения **Идентификатор клиента** и **Секрет клиента**. Оба этих значения потребуются при добавлении поставщика удостоверений для вашего клиента.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Настройка учетной записи GitHub в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используется каталог с вашим клиентом Azure AD B2C, переключившись на него в правом верхнем углу окна портала Azure. Выберите сведения о подписке, а затем выберите **Переключение каталога**. 

    ![Переключение на клиент Azure AD B2C](./media/active-directory-b2c-setup-github-app/switch-directories.png)

    Выберите каталог, содержащий ваш клиент.

    ![Выбор каталога](./media/active-directory-b2c-setup-github-app/select-directory.png)

3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Укажите **имя**. Например, введите значение *GitHub*.
6. Щелкните **Тип поставщика удостоверений**, выберите **Github (Preview)** (Github (предварительная версия)) и щелкните **ОК**.
7. Выберите действие **Настроить этот поставщик удостоверений** и введите в поле **Идентификатор клиента** сохраненный идентификатор клиента, а в поле **Секрет клиента** — сохраненный секрет клиента от учетной записи Github, которую вы ранее создали.
8. Щелкните **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию учетной записи GitHub.