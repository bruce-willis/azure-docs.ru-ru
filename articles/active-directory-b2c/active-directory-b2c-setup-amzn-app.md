---
title: Настройка регистрации и входа с учетной записью Amazon через Azure Active Directory B2C | Документация Майкрософт
description: Вы можете организовать в приложениях регистрацию и вход для клиентов с учетными записями Amazon, используя Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5fb6289f75f0c98cc218233d8adb900484ee4a17
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916502"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Настройка регистрации и входа с учетной записью Amazon через Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Создание приложения Amazon

Чтобы использовать учетную запись Amazon в качестве поставщика удостоверений для Azure Active Directory (Azure AD) B2C, необходимо создать в клиенте приложение, которое будет представлять этого поставщика. Если у вас нет учетной записи Amazon, вы можете получить ее по адресу [http://www.amazon.com/](http://www.amazon.com/).

1. Выполните вход в [Amazon Developer Center](https://login.amazon.com/) с учетными данными от учетной записи Amazon.
2. Если это еще не сделано, нажмите кнопку **Sign Up**(Регистрация), выполните действия для регистрации разработчика и примите условия политики.
3. Щелкните **Register new application** (Зарегистрировать новое приложение).
4. Введите значения **Имя**, **Описание**, и **URL-адрес заявления о конфиденциальности**, а затем щелкните **Сохранить**.
5. В разделе **Web Settings** (Веб-параметры) скопируйте значение **Client ID** (Идентификатор клиента). Выберите **Показать секрет**, чтобы просмотреть и скопировать секрет клиента. Оба значения потребуются для настройки учетной записи Amazon в качестве поставщика удостоверений в вашем клиенте. **Секрет клиента** — это важные учетные данные безопасности.
6. В разделе **Web Settings** (Веб-параметры) выберите действие **Редактировать**, а затем введите значение `https://login.microsoftonline.com` в поле **Allowed JavaScript Origins** (Допустимые источники JavaScript) и значение `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` в поле **Allowed Return URLs** (Допустимые URL-адреса возврата). Замените **{tenant}** именем своего клиента (например, contoso.onmicrosoft.com). 
7. Выберите команду **Сохранить**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Настройка учетной записи Amazon в качестве поставщика удостоверений

1. Войдите на [портал Azure](https://portal.azure.com/) с правами глобального администратора клиента Azure AD B2C.
2. Убедитесь, что используется каталог с вашим клиентом Azure AD B2C, переключившись на него в правом верхнем углу окна портала Azure. Выберите сведения о подписке, а затем выберите **Переключение каталога**. 

    ![Переключение на клиент Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Выберите каталог, содержащий ваш клиент.

    ![Выбор каталога](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Выберите **Все службы** в левом верхнем углу окна портала Azure, найдите службу **Azure AD B2C** и выберите ее.
4. Щелкните **Поставщики удостоверений** и выберите **Добавить**.
5. Введите **Имя**. Например, введите *Amazon*.
6. Щелкните **Тип поставщика удостоверений**, выберите **Amazon** и щелкните **ОК**.
7. Выберите действие **Настроить этот поставщик удостоверений** и введите в поле **Идентификатор клиента** сохраненный идентификатор клиента, а в поле **Секрет клиента** — сохраненный секрет клиента от учетной записи Amazon, которую вы ранее создали.
8. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Amazon.

