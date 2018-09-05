---
title: Руководство по интеграции Azure Active Directory с Workteam | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.openlocfilehash: 8d6ca6395e4f5e1aca361c56e21afc4e6bd1fc0c
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43132617"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>Руководство по интеграции Azure Active Directory с Workteam

В этом руководстве описано, как интегрировать Workteam с Azure Active Directory (Azure AD).

Интеграция Workteam с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Workteam.
- Вы можете включить автоматический вход пользователей в Workteam (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Workteam, вам потребуется следующее:

- подписка Azure AD;
- подписка Workteam с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление Workteam из коллекции;
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-workteam-from-the-gallery"></a>Добавление Workteam из коллекции
Чтобы настроить интеграцию Workteam с Azure AD, необходимо добавить Workteam из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Workteam из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Workteam**, выберите **Workteam** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Workteam в списке результатов](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Workteam с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Workteam соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Workteam.

Чтобы настроить и проверить единый вход Azure AD в Workteam, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Workteam](#create-a-workteam-test-user)** требуется для того, чтобы в Workteam существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Workteam.

**Чтобы настроить единый вход Azure AD в Workteam, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Workteam** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Workteam** не нужно выполнять никаких действий, так как это приложение предварительно интегрировано с Azure.

    ![Сведения о домене и URL-адресах единого входа для приложения Workteam](./media/workteam-tutorial/tutorial_workteam_url.png)

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Workteam](./media/workteam-tutorial/tutorial_workteam_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в формате `https://app.workte.am`.
     
5. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/workteam-tutorial/tutorial_general_400.png)
    
7. В разделе **Настройка Workteam** щелкните **Настроить Workteam**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Настройка Workteam](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. В другом окне веб-браузера войдите в Workteam в качестве администратора безопасности.

9. В правом верхнем углу щелкните **логотип профиля** и выберите пункт **Organization settings** (Параметры организации). 

    ![Параметры Workteam](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. В разделе **Authentication** (Проверка подлинности) щелкните **значок параметров**.

     ![Workteam и Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. На странице **Параметры SAML** выполните следующие действия.

     ![Workteam и Saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Укажите для параметра **SAML IdP** (Поставщик удостоверений SAML) значение **AD Azure**.

    b. В текстовое поле **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    c. В текстовое поле **SAML Entity ID** (Идентификатор сущности SAML) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.

    d. В Блокноте откройте скачанный с портала Azure **сертификат в кодировке base-64**, скопируйте его содержимое, а затем вставьте его в поле **SAML Signing Certificate (Base64)** (Сертификат для подписи SAML (Base64)).

    д. Последовательно выберите **ОК**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/workteam-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/workteam-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/workteam-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/workteam-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-workteam-test-user"></a>Создание тестового пользователя Workteam

Чтобы пользователи Azure AD могли выполнять вход в Workteam, их следует подготовить в Workteam. В Workteam подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в Workteam с правами администратора безопасности.

2. В средней верхней части страницы **Organization settings** (Параметры организации) щелкните вкладку **Users** (Пользователи) и нажмите кнопку **New User** (Новый пользователь).

    ![Пользователь Workteam](./media/workteam-tutorial/tutorial_workteam_user.png)

3. На странице **New employee** (Новый сотрудник) выполните следующие действия:

    ![Новый пользователь Workteam](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. В текстовое поле **Name** (Имя) введите имя пользователя, например **Brittasimon**.

    b. В текстовое поле **Email** (Адрес электронной почты) введите адрес электронной почты пользователя, например **Brittasimon@contoso.com**.

    c. Последовательно выберите **ОК**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив доступ к Workteam.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Workteam, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Workteam**.

    ![Ссылка на Workteam в списке приложений](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Workteam на панели доступа, вы автоматически войдете в приложение Workteam.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png

