---
title: Руководство по интеграции Azure Active Directory с приложением Proxyclick | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.openlocfilehash: d93c5486d9c23558995742fc27e1222834cf4452
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446319"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Руководство по интеграции Azure Active Directory с Proxyclick

В этом руководстве описано, как интегрировать Proxyclick с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Proxyclick обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Proxyclick.
- Вы можете включить автоматический вход пользователей в Proxyclick (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Proxyclick, вам потребуется следующее:

- подписка Azure AD;
- подписка Proxyclick с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Proxyclick из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-proxyclick-from-the-gallery"></a>Добавление Proxyclick из коллекции
Чтобы настроить интеграцию Proxyclick с Azure AD, необходимо добавить Proxyclick из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Proxyclick из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Proxyclick**, выберите **Proxyclick** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Proxyclick в списке результатов](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Proxyclick с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какой пользователь в Proxyclick соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Proxyclick.

Чтобы настроить и проверить единый вход Azure AD в Proxyclick, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Proxyclick](#create-a-proxyclick-test-user)** требуется для того, чтобы в Proxyclick существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Proxyclick.

**Чтобы настроить единый вход Azure AD в Proxyclick, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Proxyclick** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

1. Если вы хотите настроить приложение в режиме, инициируемом **IdP**, то в разделе **Домены и URL-адреса приложения Proxyclick** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://saml.proxyclick.com/init/<companyId>`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://saml.proxyclick.com/consume/<companyId>`.

1. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа, которые описываются далее в этом руководстве.

1. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/proxyclick-tutorial/tutorial_general_400.png)

1. В разделе **Конфигурация Proxyclick** щелкните **Настроить Proxyclick**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

1. В другом окне веб-браузера войдите на свой корпоративный сайт Proxyclick в качестве администратора.

1. Выберите **Account & Settings** (Учетная запись и параметры).

    ![Конфигурация Proxyclick](./media/proxyclick-tutorial/configure1.png)

1. Прокрутите экран вниз до раздела **Интеграции** и выберите **SAML**.

    ![Конфигурация Proxyclick](./media/proxyclick-tutorial/configure2.png)

1. В разделе **SAML** выполните следующие действия.

    ![Конфигурация Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Скопируйте значение **SAML Consumer URL** (URL-адрес объекта-получателя SAML) и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Proxyclick** на портале Azure.

    b. Скопируйте значение **SAML SSO Redirect URL** (URL-адрес перенаправления для единого входа SAML) и вставьте его в текстовые поля **URL-адрес для входа** и **Идентификатор** в разделе **Домены и URL-адреса приложения Proxyclick** на портале Azure.

    c. Для параметра **SAML Request Method** (Метод запроса SAML) выберите значение **HTTP Redirect** (Перенаправление HTTP).

    d. В текстовое поле **Issuer** (Издатель) вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    д. В текстовое поле **SAML 2.0 Endpoint URL** (URL-адрес конечной точки SAML 2.0) вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    Е. Откройте в Блокноте скачанный с портала Azure файл сертификата, а затем скопируйте и вставьте его содержимое в текстовое поле **Certificate** (Сертификат).

    ж. Нажмите кнопку **Сохранить изменения**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/proxyclick-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/proxyclick-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/proxyclick-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-proxyclick-test-user"></a>Создание тестового пользователя Proxyclick

Чтобы пользователи Azure AD могли выполнять вход в Proxyclick, они должны быть подготовлены для Proxyclick. В случае с Proxyclick подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на корпоративный сайт Proxyclick в качестве администратора.

1. Щелкните **Colleagues** (Коллеги) на верхней панели навигации.

    ![Добавление сотрудника](./media/proxyclick-tutorial/user1.png)

1. Щелкните **Add Colleague** (Добавить коллегу).

    ![Добавление сотрудника](./media/proxyclick-tutorial/user2.png)

1. В разделе **Add a colleague** (Добавление коллеги) сделайте следующее.

    ![Добавление сотрудника](./media/proxyclick-tutorial/user3.png)

    a. В текстовом поле **Email** (Электронная почта) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    b. В текстовом поле **First Name** (Имя) введите имя пользователя, например Britta.

    c. В текстовом поле **Last Name** (Фамилия) введите фамилию пользователя, например Simon.

    d. Нажмите кнопку **Add User**(Добавить пользователя).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Proxyclick.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Proxyclick, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. Из списка приложений выберите **Proxyclick**.

    ![Ссылка на Proxyclick в списке "Приложения"](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Proxyclick" на панели доступа, вы автоматически войдете в приложение Proxyclick.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

