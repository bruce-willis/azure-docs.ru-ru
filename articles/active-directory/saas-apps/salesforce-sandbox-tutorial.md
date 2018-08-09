---
title: Руководство по интеграции Azure Active Directory с песочницей Salesforce | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Salesforce Sandbox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 2e050b363db7ab1d226c5aa6fffefb17c218d377
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424298"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Учебник. Интеграция Azure Active Directory с песочницей Salesforce

В этом руководстве описано, как интегрировать Salesforce Sandbox с Azure Active Directory (Azure AD).

Интеграция Azure AD с Salesforce Sandbox обеспечивает следующие преимущества:

- С помощью Azure AD можно управлять доступом к Salesforce Sandbox.
- Вы можете включить автоматический вход пользователей в Salesforce Sandbox (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Salesforce Sandbox, вам потребуется:

- подписка Azure AD;
- подписка Salesforce Sandbox с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Salesforce Sandbox из коллекции.
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Добавление Salesforce Sandbox из коллекции
Чтобы настроить интеграцию Salesforce Sandbox с Azure AD, нужно добавить Salesforce Sandbox из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Salesforce Sandbox из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Salesforce Sandbox**, выберите **Salesforce Sandbox** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Salesforce Sandbox в списке результатов](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе объясняется, как настроить и проверить единый вход Azure AD в Salesforce Sandbox с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Salesforce Sandbox соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Salesforce Sandbox.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Salesforce Sandbox.

Чтобы настроить и проверить единый вход Azure AD в Salesforce Sandbox, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)** требуется для создания в Salesforce Sandbox пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Salesforce Sandbox.

**Чтобы настроить единый вход Azure AD в Salesforce Sandbox, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Salesforce Sandbox** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

1. В разделе **Домен и URL-адреса Salesforce Sandbox** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    a. В текстовом поле **URL-адрес для входа** введите значение в следующем формате: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`.

    b. В текстовом поле **Идентификатор** введите значение в следующем формате: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`.
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов Salesforce](https://help.salesforce.com/support).

1. В разделе **Сертификат подписи SAML** щелкните **Сертификат**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

1. В разделе **Конфигурация Salesforce Sandbox** щелкните **Настроить Salesforce Sandbox**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png) 

1. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce Sandbox.

1. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure1.png)

1. В области навигации прокрутите вниз до элемента **Settings** (Параметры) и щелкните **Identity** (Удостоверение), чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

1. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

1. Чтобы настроить параметры единого входа SAML, щелкните **Создать**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

1. В разделе «Параметры единого входа SAML» выполните следующие действия:

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. В текстовом поле **Name** (Имя) введите имя конфигурации (например, *SPSSOWAAD_Test*). 

    b. В поле **Issuer** (Издатель) вставьте **идентификатор сущности SAML**, скопированный на портале Azure.

    c. В текстовом поле **Entity Id** (Идентификатор сущности) введите `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`, если это первый экземпляр Salesforce Sandbox, добавляемый в каталог. Если вы уже добавили экземпляр Salesforce Sandbox, то для параметра **Идентификатор сущности** введите значение **URL-адрес входа** в следующем формате: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    d. Для отправки **сертификата поставщика удостоверений** нажмите кнопку **Choose File** (Выбрать файл), чтобы найти и выбрать файл сертификата, скачанный с портала Azure.

    д. Для **типа удостоверения SAML** выберите один из следующих вариантов:

      * Выберите **Assertion contains the User's Salesforce username** (Утверждение содержит имя пользователя Salesforce), если имя пользователя Salesforce передается в утверждении SAML.

      * Выберите **Assertion contains the Federation ID from the User object** (Утверждение содержит идентификатор федерации из объекта-пользователя), если идентификатор федерации из объекта-пользователя передается в утверждении SAML.

      * Выберите **Assertion contains the User ID from the User object** (Утверждение содержит идентификатор пользователя из объекта-пользователя), если идентификатор пользователя из объекта-пользователя передается в утверждении SAML.

    Е. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentifier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).

    ж. В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициированных поставщиком услуг) выберите значение **HTTP POST**.

    h. В текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений) вставьте значение **URL-адреса службы единого входа**, скопированное на портале Azure.

    i. SFDC не поддерживает выход SAML.  В качестве обходного решения вставьте `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` в текстовое поле **Identity Provider Logout URL** (URL-адрес для выхода поставщика удостоверений).

    j. Выберите команду **Сохранить**.

### <a name="enable-your-domain"></a>Включение домена

В этом разделе предполагается, что вы уже создали домен.  Дополнительные сведения см. в разделе [Define Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Определение доменного имени).

**Выполните следующие действия, чтобы включить домен:**

1. В области навигации слева в Salesforce щелкните **Company Settings** (Параметры компании), чтобы развернуть соответствующий раздел, и выберите пункт **My Domain** (Мой домен).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

   >[!NOTE]
   >Убедитесь в правильности настройки домена.

1. В разделе **Authentication Configuration** (Конфигурация аутентификации) щелкните **Edit** (Изменить), а затем для параметра **Authentication Service** (Служба аутентификации) выберите имя параметра единого входа SAML из предыдущего раздела, после чего щелкните **Save** (Сохранить).

   ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

После настройки домена для входа в песочницу Salesforce пользователям необходимо будет использовать URL-адрес домена.

Чтобы получить значение URL-адреса, щелкните профиль единого входа, созданный в предыдущем разделе.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/salesforce-sandbox-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-salesforce-sandbox-test-user"></a>Создание тестового пользователя Salesforce Sandbox

В этом разделе вы создадите в Salesforce Sandbox пользователя с именем Britta Simon. Salesforce Sandbox поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Если пользователь в Salesforce Sandbox еще не существует, он создается при попытке доступа к Salesforce Sandbox. Salesforce также поддерживает автоматическую подготовку пользователей. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](salesforce-sandbox-provisioning-tutorial.md).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Salesforce Sandbox.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Salesforce Sandbox, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. Из списка приложений выберите **Salesforce Sandbox**.

    ![Ссылка на Salesforce Sandbox в списке приложений](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Salesforce Sandbox на панели доступа, вы автоматически войдете в приложение Salesforce Sandbox.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](salesforce-sandbox-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/salesforce-sandbox-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-sandbox-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-sandbox-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-sandbox-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-sandbox-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-sandbox-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-sandbox-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-sandbox-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-sandbox-tutorial/tutorial_general_203.png
