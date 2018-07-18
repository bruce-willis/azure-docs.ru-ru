---
title: Настройка Twitter для Azure Active Directory B2C | Документация Майкрософт
description: Обеспечение регистрации и входа для пользователей с учетными записями Twitter в приложениях, защищенных с помощью Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 4/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68b9e9edbacda08bc98b6b7af5d944d3df9edca1
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709586"
---
# <a name="provide-sign-up-and-sign-in-to-consumers-with-twitter-accounts-using-azure-ad-b2c"></a>Обеспечение регистрации и входа для пользователей с учетными записями Twitter с помощью Azure AD B2C

## <a name="create-a-twitter-application"></a>Создание приложения Twitter
Чтобы использовать Twitter в качестве поставщика удостоверений в Azure Active Directory (Azure AD) B2C, нужно сначала создать приложение Twitter и задать в нем правильные параметры. Для этого потребуется учетная запись Twitter. Если у вас нет этой учетной записи, ее можно получить на сайте [https://twitter.com/signup](https://twitter.com/signup).

1. Перейдите на веб-сайт [Twitter Apps](https://apps.twitter.com/) и войдите с помощью своих учетных данных.
2. Щелкните **Создать новое приложение**. 
3. В форме укажите значения для полей **Name** (Имя), **Description** (Описание) и **Website** (Веб-сайт).
4. В поле **Callback URL** (URL-адрес обратного вызова) введите значение `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp`. Замените **{tenant}** именем своего клиента (например, contosob2c.onmicrosoft.com).
5. Установите флажок, чтобы принять **соглашение с разработчиком** и нажмите кнопку **Create your Twitter application** (Создать приложение Twitter).
6. После создания приложения выберите его в списке, а затем выберите вкладку **Параметры**.
7. Снимите флажок **Enable Callback Locking** (Включить блокировку обратного вызова), а затем нажмите **Обновить настройки**.
8. Откройте вкладку **Ключи и токены доступа** .
9. Скопируйте значения **Consumer Key** (Ключ потребителя) и **Consumer Secret** (Секрет потребителя). Оба значения необходимы для настройки Twitter в качестве поставщика удостоверений в вашем клиенте.

## <a name="configure-twitter-as-an-identity-provider-in-your-tenant"></a>Настройка Twitter в качестве поставщика удостоверений в клиенте
1. Войдите на [портал Azure](https://portal.azure.com/) как глобальный администратор клиента Azure AD B2C. 
2. Чтобы переключиться на клиент Azure AD B2C, выберите каталог Azure AD B2C в правом верхнем углу портала.
3. Щелкните **Все службы**, а затем выберите **Azure AD B2C** в списке служб в разделе **Безопасность и идентификация**.
4. Щелкните **Поставщики удостоверений**.
4. Укажите понятное **имя** конфигурации поставщика удостоверений. Например, введите Twitter.
5. Щелкните **Тип поставщика удостоверений**, выберите **Twitter (Preview)** (Twitter (предварительная версия)) и нажмите кнопку **ОК**.
6. Щелкните **Настроить этот поставщик удостоверений** и введите **ключ потребителя** в поле **Идентификатор клиента** и **секрет потребителя** Twitter в поле **Секрет клиента**.
7. Нажмите кнопку **ОК**, а затем — **Создать**, чтобы сохранить конфигурацию Twitter.

## <a name="next-steps"></a>Дополнительная информация

Создайте или измените [встроенную политику](active-directory-b2c-reference-policies.md) и добавьте Twitter в качестве поставщика удостоверений.