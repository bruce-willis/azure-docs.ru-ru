---
title: Руководство по интеграции Azure Active Directory с Riskware | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jeedes
ms.openlocfilehash: 4c664fac99e93e94b46f5d917a63aa6530b695bd
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437779"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Руководство по интеграции Azure Active Directory с Riskware

В этом руководстве описано, как интегрировать Riskware с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Riskware обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Riskware.
- Вы можете включить автоматический вход пользователей в Riskware (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Riskware, вам потребуется следующее:

- подписка Azure AD;
- подписка Riskware с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Riskware из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-riskware-from-the-gallery"></a>Добавление Riskware из коллекции
Чтобы настроить интеграцию Riskware с Azure AD, необходимо добавить Riskware из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Riskware из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Riskware**, выберите **Riskware** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Riskware в списке результатов](./media/riskware-tutorial/tutorial_riskware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Riskware с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Riskware соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Riskware.

Чтобы настроить и проверить единый вход Azure AD в Riskware, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя приложения Riskware](#create-a-riskware-test-user)** требуется для того, чтобы в Riskware существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Riskware.

**Чтобы настроить единый вход Azure AD в Riskware, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Riskware** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/riskware-tutorial/tutorial_riskware_samlbase.png)

1. В разделе **Домены и URL-адреса приложения Riskware** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Riskware](./media/riskware-tutorial/tutorial_riskware_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | Среда| Шаблон URL-адреса|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | PROD| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. В текстовом поле **Идентификатор (сущности)** введите такой URL-адрес:
    | Среда| Шаблон URL-адреса|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | PROD| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > Значение URL-адреса входа приведено только для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь к [группе поддержки Riskware](mailto:support@pansoftware.com.au).

1. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/riskware-tutorial/tutorial_riskware_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/riskware-tutorial/tutorial_general_400.png)

1. В разделе **Настройка Riskware** щелкните **Настроить Riskware**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Riskware](./media/riskware-tutorial/tutorial_riskware_configure.png)

1. В другом окне веб-браузера войдите на корпоративный сайт Riskware в качестве администратора.

1. В правом верхнем углу щелкните **Maintenance** (Обслуживание), чтобы открыть страницу обслуживания.

    ![Настройка Riskware, раздел "Обслуживание"](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. На странице обслуживания щелкните **Authentication** (Аутентификация).

    ![Настройка Riskware, аутентификация](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. На странице **Authentication Configuration** (Настройка аутентификации) выполните следующие действия:

    ![Настройка Riskware, настройка аутентификации](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Выберите для параметра аутентификации **Type** (Тип) значение **SAML**.

    b. В текстовом поле **Code** (Код) введите код, например AZURE_UAT.

    c. В текстовом поле **Description** (Описание) введите описание, например "Конфигурация AZURE для единого входа".

    d. В текстовое поле **Single Sign On Page** (Страница единого входа) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    д. В текстовое поле **Sign out page** (Страница выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    Е. В текстовое поле **Post Form Field** (Поле формы POST) введите имя поля из ответа POST, которое содержит значение SAML, например SAMLResponse.

    ж. В текстовом поле **XML Identity Tag Name** (Имя тега идентификатора XML) введите имя атрибута ответа SAML, который содержит уникальный идентификатор, например NameID.

    h. Откройте в Блокноте скачанный с портала Azure **XML-файл метаданных**, скопируйте из этого файла сертификат безопасности и вставьте его в текстовое поле **Certificate** (Сертификат).

    i. В текстовом поле **Consumer URL** (URL-адрес объекта-получателя) вставьте значение **URL-адреса ответа**, которое вы получили от службы поддержки.

    j. В текстовом поле **Issuer** (Издатель) вставьте значение **идентификатора**, которое вы получили от службы поддержки.

    > [!Note]
    > Чтобы получить это значения, обратитесь к [службе поддержки клиентов Riskware](mailto:support@pansoftware.com.au).

    k. Установите флажок **Use POST** (Использовать POST).

    l. Установите флажок **Use SAML Request** (Использовать запрос SAML).

    m. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/riskware-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/riskware-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/riskware-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/riskware-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-riskware-test-user"></a>Создание тестового пользователя Riskware

Чтобы пользователи Azure AD могли выполнять вход в Riskware, они должны быть подготовлены для Riskware. В Riskware подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в Riskware с правами администратора безопасности.

1. В правом верхнем углу щелкните **Maintenance** (Обслуживание), чтобы открыть страницу обслуживания. 

    ![Настройка Riskware, обслуживание](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. На странице обслуживания щелкните **People** (Пользователи).

    ![Настройка Riskware, пользователи](./media/riskware-tutorial/tutorial_riskware_people.png)

1. На вкладке **Details** (Подробные сведения) сделайте следующее:

    ![Настройка Riskware, подробные сведения](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Выберите **Person Type** (Тип роли), например Employee (Сотрудник).

    b. В текстовое поле **First Name** (Имя) введите имя, например **Britta**.

    c. В текстовое поле **Surname** (Фамилия) введите фамилию, например **Simon**.

1. На вкладке **Безопасность** сделайте следующее:

    ![Настройка Riskware, безопасность](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. В разделе **Authentication** (Аутентификация) выберите настроенный ранее **режим аутентификации**, например "Конфигурация AZURE для единого входа".

    b. В разделе **Logon Details** (Данные для входа) введите в текстовое поле **User ID** (Идентификатор пользователя) адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    c. В текстовом поле **Password** (Пароль) введите пароль этого пользователя.

1. На вкладке **Organisation** (Организация) сделайте следующее:

    ![Настройка Riskware, организация](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Выберите параметр от имени организации **Level1**.

    b. В разделе **Person's Primary Workplace** (Основное рабочее место) введите описание расположения в текстовое поле **Location** (Расположение).

    c. В разделе **Employee** (Сотрудник) выберите в поле **Employee Status** (Статус сотрудника) значение Casual (Обычный).

1. Выберите команду **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Riskware.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Riskware, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. Из списка приложений выберите **Riskware**.

    ![Ссылка на Riskware в списке "Приложения"](./media/riskware-tutorial/tutorial_riskware_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Riskware на панели доступа, вы автоматически войдете в приложение Riskware.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/riskware-tutorial/tutorial_general_01.png
[2]: ./media/riskware-tutorial/tutorial_general_02.png
[3]: ./media/riskware-tutorial/tutorial_general_03.png
[4]: ./media/riskware-tutorial/tutorial_general_04.png

[100]: ./media/riskware-tutorial/tutorial_general_100.png

[200]: ./media/riskware-tutorial/tutorial_general_200.png
[201]: ./media/riskware-tutorial/tutorial_general_201.png
[202]: ./media/riskware-tutorial/tutorial_general_202.png
[203]: ./media/riskware-tutorial/tutorial_general_203.png

