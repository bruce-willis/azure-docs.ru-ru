---
title: Руководство по интеграции Azure Active Directory с Mimecast Personal Portal | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Mimecast Personal Portal.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: 71ecffebe095fd325837aeb1d6e741a2f3321aea
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421844"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Руководство по интеграции Azure Active Directory с Mimecast Personal Portal

В этом руководстве описано, как интегрировать Mimecast Personal Portal с Azure Active Directory (Azure AD).

Интеграция Azure AD с Mimecast Personal Portal обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Mimecast Personal Portal.
- Вы можете включить автоматический вход пользователей в Mimecast Personal Portal (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Mimecast Personal Portal, вам потребуется:

- подписка Azure AD;
- Подписка на личный портал Mimecast с поддержкой единого входа

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Mimecast Personal Portal из коллекции.
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Добавление Mimecast Personal Portal из коллекции
Чтобы настроить интеграцию Mimecast Personal Portal с Azure AD, необходимо добавить Mimecast Personal Portal из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Mimecast Personal Portal из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Mimecast Personal Portal**, выберите **Mimecast Personal Portal** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Mimecast Personal Portal в списке результатов](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Mimecast Personal Portal с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Mimecast Personal Portal соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Mimecast Personal Portal.

Чтобы настроить и проверить единый вход Azure AD в Mimecast Personal Portal, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Mimecast Personal Portal](#create-a-mimecast-personal-portal-test-user)** требуется для того, чтобы в Mimecast Personal Portal существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Mimecast Personal Portal.

**Чтобы настроить единый вход Azure AD в Mimecast Personal Portal, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Mimecast Personal Portal** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

1. В разделе **Домены и URL-адреса приложения Mimecast Personal Portal** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Mimecast Personal Portal](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес. 

    | Регион  |  Значение | 
    | --------------- | --------------- | 
    | Европа          | `https://eu-api.mimecast.com/login/saml`|
    | США   | `https://us-api.mimecast.com/login/saml`|
    | ЮАР    | `https://za-api.mimecast.com/login/saml`|
    | Австралия       | `https://au-api.mimecast.com/login/saml`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/login/saml`|

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:

    | Регион  |  Значение | 
    | --------------- | --------------- |
    | Европа          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | США   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | ЮАР    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Австралия       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес: 

    | Регион  |  Значение | 
    | --------------- | --------------- | 
    | Европа          | `https://eu-api.mimecast.com/login/saml`|
    | США   | `https://us-api.mimecast.com/login/saml`|
    | ЮАР    | `https://za-api.mimecast.com/login/saml`|
    | Австралия       | `https://au-api.mimecast.com/login/saml`|
    | Внешнее расположение        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > Значение идентификатора приведено для примера и не является реальным. Вместо него нужно указать фактический идентификатор. Чтобы получить это значение, обратитесь к [группе поддержки клиентов Mimecast Personal Portal](http://www.mimecast.com/customer-success/technical-support/). 

1. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/mimecast-personal-portal-tutorial/tutorial_general_400.png)

1. В разделе **Конфигурация Mimecast Personal Portal** щелкните **Настроить Mimecast Personal Portal**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Mimecast Personal Portal](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

1. В другом окне веб-браузера войдите в личный портал Mimecast в качестве администратора.

1. Выберите **Services \> Application** ("Службы" > "Приложение").
   
    ![Приложения](./media/mimecast-personal-portal-tutorial/ic794998.png "Приложения")

1. Щелкните **Профили проверки подлинности**.
   
    ![Authentication Profiles](./media/mimecast-personal-portal-tutorial/ic794999.png "Authentication Profiles") (Профили аутентификации)

1. Щелкните **Новый профиль проверки подлинности**.
   
    ![Создание профиля аутентификации](./media/mimecast-personal-portal-tutorial/ic795000.png "New Authentication Profile")

1. В разделе **Профиль проверки подлинности** сделайте следующее:
   
    ![Authentication Profile](./media/mimecast-personal-portal-tutorial/ic795001.png "Authentication Profile") (Профиль аутентификации)
   
    a. В текстовом поле **Описание** введите имя конфигурации.
   
    b. Выберите **Обязательное использование проверки подлинности SAML для личного портала Mimecast**.
   
    c. В поле **Provider** (Поставщик) выберите **Azure Active Directory**.
   
    d. В текстовое поле **Issuer URL** (URL-адрес издателя) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.
   
    д. В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.
   
    Е. В текстовое поле **Logout URL** (URL-адрес выхода) вставьте значение **URL-адрес выхода**, скопированное на портале Azure.

    ж. Откройте в Блокноте сертификат в кодировке **Base64**, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Identity Provider Certificate (Metadata)** (Сертификат поставщика удостоверений (метаданные)).

    h. Установите флажок **Разрешить единый вход**.
   
    i. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/mimecast-personal-portal-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/mimecast-personal-portal-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/mimecast-personal-portal-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Создание тестового пользователя Mimecast Personal Portal

Чтобы разрешить пользователям Azure AD вход в личный портал Mimecast, они должны быть подготовлены для личного портала Mimecast. В случае с личным порталом Mimecast подготовка выполняется вручную.

Перед созданием пользователей необходимо зарегистрировать домен.

**Чтобы настроить подготовку учетных записей пользователей, выполните следующие действия.**

1. Войдите на **Mimecast Personal Portal** в качестве администратора.

1. Выберите **Directories \> Internal** (Каталоги > Внутренние).
   
    ![Directories](./media/mimecast-personal-portal-tutorial/ic795003.png "Directories") (Каталоги)

1. Щелкните **Зарегистрировать новый домен**.
   
    ![Register New Domain](./media/mimecast-personal-portal-tutorial/ic795004.png "Register New Domain") (Зарегистрировать новый домен)

1. После создания нового домена щелкните **Новый адрес**.
   
    ![New Address](./media/mimecast-personal-portal-tutorial/ic795005.png "New Address") (Новый адрес)

1. В диалоговом окне "New Address" (Новый адрес) введите соответствующие данные действующей учетной записи Azure AD, которую необходимо подготовить.
   
    ![Сохранить](./media/mimecast-personal-portal-tutorial/ic795006.png "Сохранить")
   
    a. В текстовое поле **Email Address** (Адрес электронной почты) введите **адрес электронной почты** пользователя, например **BrittaSimon@contoso.com**.
    
    b. В текстовое поле **Global Name** (Глобальное имя) введите **имя пользователя** **BrittaSimon**.

    c. В текстовые поля **Password** (Пароль) и **Confirm Password** (Подтверждение пароля) введите **пароль** пользователя.
   
    b. Выберите команду **Сохранить**.

>[!NOTE]
>Вы можете использовать любые другие инструменты создания учетных записей пользователя Mimecast Personal Portal или API, предоставляемые Mimecast Personal Portal для подготовки учетных записей пользователя Azure Active Directory.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Mimecast Personal Portal.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Mimecast Personal Portal, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. Из списка приложений выберите **Mimecast Personal Portal**.

    ![Ссылка на Mimecast Personal Portal в списке приложений](./media/mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Mimecast Personal Portal" на панели доступа, вы автоматически войдете в приложение Mimecast Personal Portal.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/mimecast-personal-portal-tutorial/tutorial_general_203.png

