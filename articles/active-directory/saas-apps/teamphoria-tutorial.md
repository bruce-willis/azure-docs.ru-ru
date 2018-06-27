---
title: Руководство по интеграции Azure Active Directory с Teamphoria | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Teamphoria.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d569c705-6f0f-4ec1-b485-ba82526b5d32
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: jeedes
ms.openlocfilehash: 0ff053b88a718c152d31b593a7759b034347f3ef
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293676"
---
# <a name="tutorial-azure-active-directory-integration-with-teamphoria"></a>Руководство по интеграции Azure Active Directory с Teamphoria

В этом руководстве описано, как интегрировать приложение Teamphoria с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Teamphoria обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Teamphoria.
- Вы можете включить автоматический вход пользователей в Teamphoria (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Teamphoria, вам потребуется:

- подписка Azure AD;
- подписка Teamphoria с активированной функцией единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.
Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Teamphoria из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-teamphoria-from-the-gallery"></a>Добавление Teamphoria из коллекции
Чтобы настроить интеграцию Teamphoria с Azure AD, необходимо добавить Teamphoria из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Teamphoria из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Teamphoria**.

    ![Создание тестового пользователя Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_search.png)

5. На панели результатов выберите **Teamphoria** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/teamphoria-tutorial/tutorial_teamphoria_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Teamphoria с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Teamphoria соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем в Azure AD и соответствующим пользователем в Teamphoria.

Чтобы настроить и проверить единый вход Azure AD в Teamphoria, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Teamphoria](#creating-a-teamphoria-test-user)** требуется для создания пользователя Britta Simon в Teamphoria, связанного с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure, а также как настроить его в приложении Teamphoria.

**Чтобы настроить единый вход Azure AD в Teamphoria, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Teamphoria** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Настройка единого входа](./media/teamphoria-tutorial/tutorial_teamphoria_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Teamphoria** сделайте следующее.

    ![Настройка единого входа](./media/teamphoria-tutorial/tutorial_teamphoria_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<sub-domain>.teamphoria.com/login`.   

    > [!NOTE] 
    > Значение URL-адреса входа приведено для примера. Вместо него нужно указать фактический URL-адрес для входа. Чтобы получить его, обратитесь в [службу поддержки клиентов Teamphoria](https://www.teamphoria.com/).

4. В разделе **Сертификат подписи SAML** щелкните **(Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/teamphoria-tutorial/tutorial_teamphoria_certificate.png)

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/teamphoria-tutorial/tutorial_general_400.png)

6. В разделе **Настройка Teamphoria** щелкните **Настройка Teamphoria**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/teamphoria-tutorial/tutorial_teamphoria_configure.png)

7. Для настройки единого входа в **Teamphoria** войдите в приложение Teamphoria с правами администратора.

8. Перейдите к пункту **ADMIN SETTINGS** (Параметры администратора) на панели слева инструментов и на вкладке Configure (Настройка) щелкните **SINGLE SIGN-ON** (Единый вход), чтобы открыть окно настройки единого входа.

    ![Настройка единого входа](./media/teamphoria-tutorial/admin_sso_configure.png)

9. Щелкните параметр **ADD NEW IDENTITY PROVIDER** (Добавить нового поставщика удостоверений) в верхнем правом углу, чтобы открыть форму для добавления параметров единого входа.

    ![Настройка единого входа](./media/teamphoria-tutorial/add_new_identity_provider.png)

10. Введите сведения в полях, как описано ниже.

    ![Настройка единого входа](./media/teamphoria-tutorial/Teamphoria_sso_save.png)

    a. **DISPLAY NAME** (Отображаемое имя). Введите отображаемое имя подключаемого модуля на странице администрирования.

    Б. **BUTTON NAME** (Имя кнопки). Имя вкладки, которая будет отображаться на странице входа при едином входе.

    c. **CERTIFICATE** (Сертификат). Откройте в Блокноте сертификат, скачанный ранее с портала Azure, скопируйте его содержимое и вставьте в это поле.

    d. **ENTRY POINT** (Точка входа). Вставьте **URL-адрес службы единого входа SAML**, скопированный ранее на портале Azure.

    д. Установите переключатель в положение **Вкл.** и нажмите кнопку **Сохранить**.

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/teamphoria-tutorial/create_aaduser_01.png) 

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Создание тестового пользователя Azure AD](./media/teamphoria-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/teamphoria-tutorial/create_aaduser_03.png)

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/teamphoria-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    Б. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="creating-a-teamphoria-test-user"></a>Создание тестового пользователя Teamphoria

Чтобы пользователи Azure AD могли выполнить вход в Teamphoria, они должны быть подготовлены в Teamphoria. В случае с Teamphoria подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на веб-сайт Teamphoria вашей компании в качестве администратора.

2. Щелкните **параметры администратора** на левой панели и на вкладке **Управление** щелкните **Пользователи**, чтобы открыть страницу администрирования для пользователей.

    ![Добавление сотрудника](./media/teamphoria-tutorial/admin_manage_users.png)

3. Щелкните **MANUAL INVITE** (Пригласить вручную).

    ![Приглашение пользователей](./media/teamphoria-tutorial/admin_manage_add_users.png)

4. На этой странице сделайте следующее.
    
    ![Приглашение пользователей](./media/teamphoria-tutorial/manual_user_invite.png)

    a. В текстовом поле **Адрес электронной почты** находится **электронный адрес** пользователя Britta Simon.

    Б. В текстовом поле **Имя** введите **Britta**.

    c. В текстовом поле **Фамилия** введите **Simon**.

    d. Нажмите кнопку **INVITE 1 USER** (Пригласить одного пользователя). Пользователь должен принять приглашение, чтобы его создали в системе.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Teamphoria.

![Назначение пользователя][200]

**Чтобы назначить пользователя Britta Simon в Teamphoria, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **Teamphoria**.

    ![Настройка единого входа](./media/teamphoria-tutorial/tutorial_teamphoria_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Если вы хотите проверить параметры единого входа, откройте панель доступа. Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/teamphoria-tutorial/tutorial_general_01.png
[2]: ./media/teamphoria-tutorial/tutorial_general_02.png
[3]: ./media/teamphoria-tutorial/tutorial_general_03.png
[4]: ./media/teamphoria-tutorial/tutorial_general_04.png

[100]: ./media/teamphoria-tutorial/tutorial_general_100.png

[200]: ./media/teamphoria-tutorial/tutorial_general_200.png
[201]: ./media/teamphoria-tutorial/tutorial_general_201.png
[202]: ./media/teamphoria-tutorial/tutorial_general_202.png
[203]: ./media/teamphoria-tutorial/tutorial_general_203.png
