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
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 6feafba41cf65a752dd5bf0819b0b93bacff0aff
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42141530"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Учебник. Интеграция Azure Active Directory с песочницей Salesforce

В этом руководстве описано, как интегрировать Salesforce Sandbox с Azure Active Directory (Azure AD).

Песочницы позволяет создать несколько копий организации в отдельных средах для различных целей, например для разработки, тестирования и обучения, не подвергая риску данные и приложения в рабочей организации Salesforce.
Дополнительные сведения см. в статье[Sandbox Types and Templates](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5) (Типы и шаблоны песочниц).

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
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Добавление Salesforce Sandbox из коллекции

Чтобы настроить интеграцию Salesforce Sandbox с Azure AD, нужно добавить Salesforce Sandbox из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Salesforce Sandbox из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Salesforce Sandbox**, выберите **Salesforce Sandbox** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Salesforce Sandbox в списке результатов](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе объясняется, как настроить и проверить единый вход Azure AD в Salesforce Sandbox с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Salesforce Sandbox соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Salesforce Sandbox.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Salesforce Sandbox.

Чтобы настроить и проверить единый вход Azure AD в Salesforce Sandbox, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Salesforce Sandbox](#create-a-salesforce-sandbox-test-user)** требуется для создания в Salesforce Sandbox пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Salesforce Sandbox.

**Чтобы настроить единый вход Azure AD в Salesforce Sandbox, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Salesforce Sandbox** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **IDP**, то в разделе **Домены и URL-адреса приложения Salesforce Sandbox** сделайте следующее:

   ![Сведения о домене и URL-адресах единого входа для приложения Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url1.png)

   В текстовом поле **URL-адрес ответа** введите **URL-адрес ответа** своей организации.

   > [!NOTE]
   > Обновите значение с помощью фактического URL-адреса ответа, описанного далее в этом руководстве.

4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (RAW)**, а затем сохраните файл сертификата на компьютер.

    ![Ссылка для скачивания сертификата](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация Salesforce Sandbox** щелкните **Настроить Salesforce Sandbox**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png)

7. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce Sandbox.

8. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure1.png)

9. В области навигации слева прокрутите вниз до элемента **Параметры** и щелкните **Удостоверение**, чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

10. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure3.png)

11. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

12. Чтобы настроить параметры единого входа SAML, щелкните **Создать**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

13. В разделе **Параметры единого входа** выполните следующие действия:

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-saml-config1.png)

    a. Установите флажок для параметра **SAML Enabled** (SAML включен).

    b. В поле **Issuer** (Издатель) вставьте **идентификатор сущности SAML**, скопированный на портале Azure.

    c. Для отправки **сертификата поставщика удостоверений** нажмите кнопку **Обзор**, чтобы найти и выбрать файл сертификата, скачанный с портала Azure.

    d. В текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений) вставьте значение **URL-адреса службы единого входа**, скопированное на портале Azure.

    д. Для **типа удостоверения SAML** выберите один из следующих вариантов:

      * Выберите **Assertion contains the User's Salesforce username** (Утверждение содержит имя пользователя Salesforce), если имя пользователя Salesforce передается в утверждении SAML.

      * Выберите **Assertion contains the Federation ID from the User object** (Утверждение содержит идентификатор федерации из объекта-пользователя), если идентификатор федерации из объекта-пользователя передается в утверждении SAML.
  
    Е. В качестве **расположения удостоверения SAML** выберите **Identity is an Attribute element** (Удостоверение — элемент атрибута).

    ж. SFDC не поддерживает выход SAML.  Вы можете вставить `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` в текстовое поле**Custom Logout URL** (Пользовательский URL-адрес выхода).

    h. Выберите команду **Сохранить**.

14. На странице **Параметры единого входа** нажмите кнопку **Download Metadata** (Скачать метаданные ).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure4.png)

15. Откройте скачанный файл метаданных в другом окне браузера и скопируйте значение **расположения**. Вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Salesforce Sandbox** на портале Azure.  

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure5.png)

16. Если вы хотите настроить приложение в режиме, инициированном **SP**, для этого есть следующие требования:

    a. Вам необходимо получить проверенный домен.

    b. Необходимо настроить и включить домен в Salesforce Sandbox. Дальнейшие действия объясняются далее в этом руководстве.

    c. На портале Azure в разделе **Домены и URL-адреса приложения Salesforce Sandbox** установите флажок для параметра **Показать дополнительные параметры URL-адресов** и выполните следующие действия:
  
    ![Сведения о домене и URL-адресах единого входа для приложения Salesforce Sandbox](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_url.png)

    В текстовом поле **URL-адрес для входа** введите значение в следующем формате: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`.

    > [!NOTE]
    > Это значение следует скопировать с портала Salesforce Sandbox сразу после включения домена.

17. В разделе **Сертификат подписи SAML** щелкните **Сертификат (RAW)**, а затем сохраните файл сертификата на компьютер.

    ![Ссылка для скачивания сертификата](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_certificate.png)

18. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/salesforce-sandbox-tutorial/tutorial_general_400.png)

19. В разделе **Конфигурация Salesforce Sandbox** щелкните **Настроить Salesforce Sandbox**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_configure.png)

20. Откройте новую вкладку в браузере и войдите в учетную запись администратора Salesforce Sandbox.

21. Щелкните **Setup** (Настройка) под **значком параметров** в правом верхнем углу страницы.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure1.png)

22. В области навигации слева прокрутите вниз до элемента **Параметры** и щелкните **Удостоверение**, чтобы развернуть соответствующий раздел. Затем щелкните **Параметры единого входа**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

23. На странице **Single Sign-On Settings** (Параметры единого входа) нажмите кнопку **Edit** (Изменить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure3.png)

24. Выберите **SAML Enabled** (SAML включен), а затем щелкните **Save** (Сохранить).

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

25. Чтобы настроить параметры единого входа SAML, щелкните **Создать**.

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

26. Если вы добавляете второй экземпляр, необходимо включить домен, как показано выше (при инициировании со стороны SP). В разделе «Параметры единого входа SAML» выполните следующие действия:

    ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

    a. В текстовом поле **Name** (Имя) введите имя конфигурации (например, *SPSSOWAAD_Test*).

    b. В поле **Issuer** (Издатель) вставьте **идентификатор сущности SAML**, скопированный на портале Azure.

    c. В текстовом поле**Идентификатор сущности** используйте значение`https://test.salesforce.com` для первого экземпляра, а из второго экземпляра приложения можно использовать значение идентификатора конкретного клиента.

    d. Для отправки **сертификата поставщика удостоверений** нажмите кнопку **Choose File** (Выбрать файл), чтобы найти и выбрать файл сертификата, скачанный с портала Azure.

    д. Для **типа удостоверения SAML** выберите один из следующих вариантов:

      * Выберите **Assertion contains the User's Salesforce username** (Утверждение содержит имя пользователя Salesforce), если имя пользователя Salesforce передается в утверждении SAML.

      * Выберите **Assertion contains the Federation ID from the User object** (Утверждение содержит идентификатор федерации из объекта-пользователя), если идентификатор федерации из объекта-пользователя передается в утверждении SAML.

      * Выберите **Assertion contains the User ID from the User object** (Утверждение содержит идентификатор пользователя из объекта-пользователя), если идентификатор пользователя из объекта-пользователя передается в утверждении SAML.

    Е. В поле **SAML Identity Location** (Расположение удостоверения SAML) выберите значение **Identity is in the NameIdentifier element of the Subject statement** (Удостоверение находится в элементе NameIdentifier оператора Subject).

    ж. В поле **Service Provider Initiated Request Binding** (Связывание запросов, инициированных поставщиком услуг) выберите значение **HTTP POST**.

    h. В текстовое поле **Identity Provider Login URL** (URL-адрес входа поставщика удостоверений) вставьте значение **URL-адреса службы единого входа**, скопированное на портале Azure.

    i. SFDC не поддерживает выход SAML.  Вы можете вставить `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` в текстовое поле**Custom Logout URL** (Пользовательский URL-адрес выхода).

    j. Выберите команду **Сохранить**.

27. Чтобы включить домен в приложении Salesforce Sandbox, выполните следующие действия:

    > [!NOTE]
    > Перед включением домена необходимо его создать в приложении Salesforce Sandbox. Дополнительные сведения см. в разделе [Define Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Определение доменного имени). Создав домен, убедитесь, что он настроен правильно.

    * В области навигации слева в Salesforce Sandbox щелкните **Company Settings** (Параметры компании), чтобы развернуть соответствующий раздел, и выберите пункт **My Domain** (Мой домен).

         ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

    * В разделе **Authentication Configuration** (Конфигурация проверки подлинности) щелкните **Edit** (Редактировать).

        ![Настройка единого входа](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

    * В разделе **Authentication Configuration** (Конфигурация проверки подлинности) в качестве **службы проверки подлинности** выберите имя параметра единого входа SAML, установленного при конфигурации единого входа в Salesforce Sandbox, после чего щелкните **Сохранить**.

        ![Настройка единого входа](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/salesforce-sandbox-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/salesforce-sandbox-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/salesforce-sandbox-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

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

2. Из списка приложений выберите **Salesforce Sandbox**.

    ![Ссылка на Salesforce Sandbox в списке приложений](./media/salesforce-sandbox-tutorial/tutorial_salesforcesandbox_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
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