---
title: Руководство по интеграции Azure Active Directory с приложение N2F - Expense reports | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и приложением N2F - Expense reports.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 27fb299bc3bbbbf75bdf40ae02eac627763ce6d4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007598"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>Руководство по интеграции Azure Active Directory с приложением N2F - Expense reports

В этом руководстве описано, как интегрировать приложение N2F - Expense reports с Azure Active Directory (Azure AD).

Интеграция приложение N2F - Expense reports с Azure AD обеспечивают следующие преимущества.

- C помощью Azure AD вы можете контролировать доступ к N2F - Expense reports.
- Вы можете включить автоматический вход пользователей в N2F - Expense reports (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с N2F - Expense reports, вам потребуется:

- подписка Azure AD;
- подписка N2F - Expense reports с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление N2F - Expense reports из коллекции;
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>Добавление N2F - Expense reports из коллекции

Чтобы настроить интеграцию N2F - Expense reports с Azure AD, необходимо добавить N2F - Expense reports из коллекции в список управляемых приложений SaaS.

**Чтобы добавить N2F - Expense reports из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **N2F - Expense reports**, на панели результатов выберите **N2F - Expense reports** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![N2F - Expense reports в списке результатов](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в N2F - Expense reports с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в N2F - Expense reports соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в N2F - Expense reports.

Чтобы настроить и проверить единый вход Azure AD в N2F - Expense reports, вам потребуется выполнить следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя N2F - Expense reports](#create-a-n2f---expense-reports-test-use)** требуется для создания в N2F - Expense reports пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении N2F - Expense reports.

**Чтобы настроить единый вход Azure AD в N2F - Expense reports, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **N2F - Expense reports** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. Если нужно настроить приложение в режиме, инициируемом **IDP**, то в разделе **Домены и URL-адреса приложения N2F - Expense reports**, пользователю не нужно ничего делать, так как это приложение уже предварительно интегрировано в Azure.

    ![Сведения о домене и URL-адресах единого входа для приложения N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://www.n2f.com/app/`

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. В разделе **Конфигурация N2F - Expense reports** щелкните **Настроить N2F - Expense reports**, чтобы открыть окно **Настройка единого входа**. Скопируйте значение **SAML Entity ID** (Идентификатор сущности SAML) из раздела **Краткий справочник**.

    ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. В другом окне веб-браузера войдите на корпоративный сайт N2F - Expense reports в качестве администратора.

9. Щелкните **Settings** (Параметры), а затем выберите **Advance Settings** (Дополнительные параметры) из раскрывающегося списка.

    ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/configure1.png)

10. Выберите вкладку **Account settings** (Параметры учетной записи).

    ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/configure2.png)

11. Выберите **Authentication** (Проверки подлинности), а затем выберите вкладку **+ Add an authentication method** (Добавить метод проверки подлинности).

    ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/configure3.png)

12. Выберите **SAML Microsoft Office 365** как метод проверки подлинности.

    ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/configure4.png)

13. В разделе **Authentication method** (Метод проверки подлинности) выполните следующие действия.

    ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/configure5.png)

    a. В текстовое поле **Entity ID** (Идентификатор сущности) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure.

    b. В текстовое поле **Metadata URL** (URL-адрес метаданных) вставьте значение **URL-адреса метаданных федерации приложения**, скопированное на портале Azure.

    c. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-n2f---expense-reports-test-user"></a>Создание тестового пользователя N2F - Expense reports

Чтобы разрешить пользователям Azure AD вход в N2F - Expense reports, их необходимо подготовить для N2F - Expense reports. В случае с N2F - Expense reports подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на сайт компании N2F - Expense reports в качестве администратора.

2. Щелкните **Settings** (Параметры), а затем выберите **Advance Settings** (Дополнительные параметры) из раскрывающегося списка.

   ![Добавление пользователя в N2F — Expense](./media/n2f-expensereports-tutorial/configure1.png)

3. Выберите вкладку **Users** (Пользователи) на панели навигации слева.

    ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/user1.png)

4. Щелкните **+ New user** (+ Новый пользователь).

   ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/user2.png)

5. В разделе **User** (Пользователь) выполните следующие действия.

    ![Конфигурация N2F - Expense reports](./media/n2f-expensereports-tutorial/user3.png)

    a. В текстовом поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    b. В текстовое поле **First name** (Имя) введите имя пользователя, например **Britta**.

    c. В текстовое поле **Name** (Имя) введите имя, например **BrittaSimon**.

    d. Выберите **роль, непосредственного руководителя (N + 1)** и **отделение** в соответствии с требованиями своей организации.

    д. Нажмите кнопку **Validate and send invitation** (Проверить и отправить приглашение).

    > [!NOTE]
    > Если во время добавления пользователя возникают проблемы, обратитесь в [службу поддержки N2F - Expense reports](mailto:support@n2f.com)

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к N2F - Expense reports.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению N2F - Expense reports, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **N2F - Expense reports**.

    ![Ссылка на N2F - Expense reports в списке приложений](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку N2F - Expense reports на панели доступа, вы автоматически войдете в приложение N2F - Expense reports.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

