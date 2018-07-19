---
title: Руководство по интеграции Azure Active Directory с Zoho One | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Zoho One.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 9f1db693ac75d7ab4b9776d60f63a0ca7524f760
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050190"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Руководство по интеграции Azure Active Directory с Zoho One

B этом руководстве описано, как интегрировать Zoho One с Azure Active Directory (Azure AD).

Интеграция Microsoft Azure Active Directory с Zoho One обеспечивает следующие преимущества:

- С помощью Microsoft Azure Active Directory вы можете контролировать доступ к Zoho One.
- Вы можете включить автоматический вход в Zoho One (единый вход) с учетной записью Microsoft Azure Active Directory.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Microsoft Azure Active Directory с Zoho One, вам потребуется следующее:

- подписка Azure AD;
- подписка Zoho One с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Zoho One из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-zoho-one-from-the-gallery"></a>Добавление Zoho One из коллекции
Чтобы настроить интеграцию Zoho One с Microsoft Azure Active Directory, необходимо добавить Zoho One из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Zoho One из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Zoho One**, выберите **Zoho One** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Zoho One в списке результатов](./media/zohoone-tutorial/tutorial_zohoone_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Microsoft Azure Active Directory в приложение Zoho One с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Microsoft Azure Active Directory необходима информация о том, какой пользователь в Zoho One соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Microsoft Azure Active Directory и соответствующим пользователем Zoho One.

Чтобы настроить и проверить единый вход Microsoft Azure Active Directory в Zoho One, выполните действия из следующих разделов:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Zoho One](#create-a-zoho-one-test-user)** требуется для того, чтобы в Zoho One существовал пользователь Britta Simon, связанный с одноименным пользователем в Microsoft Azure Active Directory.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Microsoft Azure единый вход Microsoft Azure Active Directory и настроим его в приложении Zoho One.

**Чтобы настроить единый вход Azure AD в Zoho One, сделайте следующее:**

1. На портале Microsoft Azure на странице интеграции с приложением **Zoho One** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/zohoone-tutorial/tutorial_zohoone_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения Zoho One** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения Zoho One](./media/zohoone-tutorial/tutorial_zohoone_url.png)

    a. В текстовом поле **Идентификатор (сущности)** введите такой URL-адрес: `one.zoho.com`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://accounts.zoho.com/samlresponse/<saml-identifier>`.

    c. Установите флажок **Показать дополнительные параметры URL-адресов**,

    d. В текстовом поле **Состояние ретрансляции** введите такой URL-адрес: `https://one.zoho.com`

4. Если вы хотите настроить приложение в режиме, инициированном **поставщиком услуг**, сделайте следующее:

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com`
     
    > [!NOTE] 
    > Приведенные выше значения **URL-адрес ответа** и **URL-адрес для входа** используются только для примера. Их нужно заменить фактическими URL-адресами. Этот процесс описан далее в этом руководстве. 

5. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/zohoone-tutorial/tutorial_zohoone_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/zohoone-tutorial/tutorial_general_400.png)
    
7. В разделе **Конфигурация Zoho One** щелкните **Настроить Zoho One**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Zoho One](./media/zohoone-tutorial/tutorial_zohoone_configure.png) 

8. В другом окне браузера войдите на свой корпоративный сайт Zoho One в качестве администратора.

9. На вкладке **Organization** (Организация) в разделе **SAML Authentication** (Аутентификация SAML) щелкните **Setup** (Настройка).

    ![Вкладка "Организация" на сайте Zoho One](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

10. В открывшемся окне сделайте следующее:

    ![Адреса для входа-выхода, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. В текстовое поле **Sign-in URL** (URL-адрес входа) вставьте **URL-адрес службы единого входа SAML**, скопированный на портале Microsoft Azure.

    b. В текстовое поле **Sign-out URL** (URL-адрес выхода) вставьте **URL-адрес выхода**, скопированный на портале Microsoft Azure.

    c. Нажмите кнопку **Browse** (Обзор), чтобы отправить **сертификат (Base64)**, скачанный с портала Microsoft Azure.

    d. Выберите команду **Сохранить**.

11. Сохранив параметры аутентификации SAML, скопируйте значение **SAML-Identifier** (Идентификатор SAML) и вставьте его в поле **URL-адрес ответа** на портале Microsoft Azure в разделе **Домены и URL-адреса приложения Zoho One**.

    ![Идентификатор SAML, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

12. Перейдите на вкладку **Domains** (Домены) и нажмите кнопку **Add Domain** (Добавить домен).

    ![Домены, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

13. На странице **Add Domain** (Добавление домена) сделайте следующее:

    ![Добавление домена, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. В текстовом поле **Domain Name** (Имя домена) введите домен, например **contoso.com**.

    b. Щелкните **Добавить**.

    >[!Note]
    >Добавив домен, выполните [эти](https://www.zoho.com/one/help/admin-guide/domain-verification.html) действия, чтобы подтвердить его. Когда домен будет подтвержден, укажите его имя в поле **URL-адрес входа** на портале Microsoft Azure в разделе **Домены и URL-адреса приложения Zoho One**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/zohoone-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/zohoone-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/zohoone-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/zohoone-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-zoho-one-test-user"></a>Создание тестового пользователя Zoho One

Чтобы пользователи Microsoft Azure Active Directory могли входить в Zoho One, они должны быть подготовлены в приложении Zoho One. В Zoho One подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в Zoho One с правами администратора безопасности.

2. На вкладке **Users** (Пользователи) щелкните **значок пользователя**.

    ![Пользователь, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. На странице **Добавление пользователя** сделайте следующее:

    ![Добавление пользователя, Zoho One](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. В текстовое поле **Name** (Имя) введите имя пользователя, например **Britta Simon**.
    
    b. В текстовое поле **Email Address** (Адрес электронной почты) введите адрес электронной почты пользователя (например, **brittasimon@contoso.com**).

    >[!Note]
    >В списке доменов выберите свой подтвержденный домен.

    c. Щелкните **Добавить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Zoho One.

![Назначение роли пользователя][200] 

**Назначение пользователя Britta Simon в Zoho One**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Zoho One**.

    ![Ссылка на Zoho One в списке приложений](./media/zohoone-tutorial/tutorial_zohoone_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Zoho One на панели доступа, вы автоматически войдете в приложение Zoho One.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zohoone-tutorial/tutorial_general_01.png
[2]: ./media/zohoone-tutorial/tutorial_general_02.png
[3]: ./media/zohoone-tutorial/tutorial_general_03.png
[4]: ./media/zohoone-tutorial/tutorial_general_04.png

[100]: ./media/zohoone-tutorial/tutorial_general_100.png

[200]: ./media/zohoone-tutorial/tutorial_general_200.png
[201]: ./media/zohoone-tutorial/tutorial_general_201.png
[202]: ./media/zohoone-tutorial/tutorial_general_202.png
[203]: ./media/zohoone-tutorial/tutorial_general_203.png

