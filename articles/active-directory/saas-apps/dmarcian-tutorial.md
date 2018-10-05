---
title: Руководство. Интеграция Azure Active Directory с dmarcian | Документация Майкрософт
description: Сведения о том, как настроить единый вход между Azure Active Directory и dmarcian.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a04b9383-3a60-4d54-9412-123daaddff3b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2018
ms.author: jeedes
ms.openlocfilehash: 0f8878505280371bf6046c1d1f0d7fc1275dd496
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039893"
---
# <a name="tutorial-azure-active-directory-integration-with-dmarcian"></a>Руководство. Интеграция Azure Active Directory с dmarcian

В этом руководстве описано, как интегрировать dmarcian с Azure Active Directory (Azure AD).

Интеграция dmarcian с Azure AD предоставляет следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к dmarcian.
- Вы можете включить автоматический вход пользователей в dmarcian (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с dmarcian, потребуется следующее:

- подписка Azure AD;
- подписка dmarcian с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление dmarcian из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-dmarcian-from-the-gallery"></a>Добавление dmarcian из коллекции
Чтобы настроить интеграцию dmarcian с Azure AD, необходимо добавить dmarcian из коллекции в список управляемых приложений SaaS.

**Чтобы добавить dmarcian из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Пункт "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **dmarcian**, выберите **dmarcian** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![dmarcian в списке результатов](./media/dmarcian-tutorial/tutorial_dmarcian_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в dmarcian с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в dmarcian соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в dmarcian.

Чтобы настроить и проверить единый вход Azure AD в dmarcian, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя dmarcian](#create-a-dmarcian-test-user)** требуется для того, чтобы в dmarcian существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении dmarcian.

**Чтобы настроить единый вход Azure AD в dmarcian, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **dmarcian** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/dmarcian-tutorial/tutorial_dmarcian_samlbase.png)

3. Если нужно настроить приложение в режиме, инициированном **поставщиком удостоверений**, в разделе **Домены и URL-адреса приложения dmarcian** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения dmarcian](./media/dmarcian-tutorial/tutorial_dmarcian_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    | -- |
    | `https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml` |
    | `https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml` |

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/` |
    | `https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/` |

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа приложения dmarcian](./media/dmarcian-tutorial/tutorial_dmarcian_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://us.dmarcian.com/login/<ACCOUNT_ID>` |
    | `https://dmarcian-eu.com/login/<ACCOUNT_ID>` |
    | `https://dmarciam-ap.com/login/<ACCOUNT_ID>` |
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа, которые описываются далее в этом руководстве. 

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/dmarcian-tutorial/tutorial_dmarcian_certificate.png) 

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/dmarcian-tutorial/tutorial_general_400.png)
    
7. В другом окне веб-браузера войдите в dmarcian в качестве администратора безопасности.

8. Щелкните **Profile** (Профиль) в верхнем правом углу, а затем выберите **Preferences** (Предпочтения).

    ![Предпочтения ](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

9. Прокрутите вниз до раздела **Single Sign-On** (Единый вход) и нажмите кнопку **Configure** (Настроить).

    ![Единый вход ](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

10. На странице **SAML Single Sign-On** (Единый вход SAML) установите в поле **Status** (Состояние) значение **Enabled** (Включено) и выполните следующие действия:

    ![Аутентификация ](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * В разделе **Add dmarcian to your Identity Provider** (Добавление dmarcian к поставщику удостоверений) нажмите **COPY** (Копировать), чтобы скопировать **URL-адрес службы обработчика утверждений** для вашего экземпляра и вставить его в текстовое поле **URL-адреса ответа** в разделе **Домены и URL-адреса dmarcian** на портале Azure.

    * В разделе **Add dmarcian to your Identity Provider** (Добавление dmarcian к поставщику удостоверений) нажмите **COPY** (Копировать), чтобы скопировать **идентификатор сущности** для вашего экземпляра и вставить его в текстовое поле **идентификатора** в разделе **Домены и URL-адреса dmarcian** на портале Azure.

    * В разделе **Set up Authentication** (Настройка проверки подлинности) в текстовое поле **Identity Provider Metadata** (Метаданные поставщика удостоверений) вставьте **URL-адрес метаданных федерации приложений**, который вы скопировали на портале Azure.

    * В разделе **Set up Authentication** (Настройка проверки подлинности) в текстовое поле **Attribute Statements** (Операторы атрибута) вставьте URL-адрес `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    * В разделе **Set up Login URL** (Настройка URL-адреса входа) скопируйте **URL-адрес входа** для вашего экземпляра и вставьте его в текстовое поле **URL-адреса входа** в разделе **Домены и URL-адреса dmarcian** на портале Azure.

        > [!Note]
        > Вы можете изменить **URL-адрес входа** в соответствии с вашей организацией.

    * Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/dmarcian-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/dmarcian-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/dmarcian-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/dmarcian-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-dmarcian-test-user"></a>Создание тестового пользователя dmarcian

Чтобы пользователи Azure AD могли выполнять вход в dmarcian, они должны быть подготовлены для dmarcian. В dmarcian подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в dmarcian с правами администратора безопасности.

2. Щелкните **Profile** (Профиль) в верхнем правом углу, а затем выберите **Manage Users** (Управление пользователями).

    ![Пользователь ](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. В правой части раздела **SSO Users** (Пользователи единого входа) щелкните **Add New User** (Добавить нового пользователя).

    ![Добавление пользователя ](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. Во всплывающем меню **Add New User** (Добавление нового пользователя) сделайте следующее:

    ![Новый пользователь ](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. В текстовое поле **New User Email** (Новая электронная почта Пользователя) введите адрес электронной почты пользователя, например **brittasimon@contoso.com**.

    b. Если нужно предоставить пользователю права администратора, выберите **Make User an Admin** (Сделать пользователя администратором).

    c. Нажмите кнопку **Add User**(Добавить пользователя).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к dmarcian.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в dmarcian, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **dmarcian**.

    ![Ссылка на dmarcian в списке приложений](./media/dmarcian-tutorial/tutorial_dmarcian_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент dmarcian на панели доступа, вы автоматически войдете в приложение dmarcian.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/dmarcian-tutorial/tutorial_general_01.png
[2]: ./media/dmarcian-tutorial/tutorial_general_02.png
[3]: ./media/dmarcian-tutorial/tutorial_general_03.png
[4]: ./media/dmarcian-tutorial/tutorial_general_04.png

[100]: ./media/dmarcian-tutorial/tutorial_general_100.png

[200]: ./media/dmarcian-tutorial/tutorial_general_200.png
[201]: ./media/dmarcian-tutorial/tutorial_general_201.png
[202]: ./media/dmarcian-tutorial/tutorial_general_202.png
[203]: ./media/dmarcian-tutorial/tutorial_general_203.png

