---
title: Учебник. Интеграция Azure Active Directory с HireVue | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в HireVue.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: aadfc342-14db-4d74-a83d-f0c76f0cf63c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: f208a0c1a5f2fd0c07e6df9e96004d481fd06870
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39433758"
---
# <a name="tutorial-azure-active-directory-integration-with-hirevue"></a>Руководство. Интеграция Azure Active Directory с HireVue

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением HireVue.

Интеграция Azure AD с приложением HireVue обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к HireVue.
- Вы можете включить автоматический вход пользователей в HireVue (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с HireVue, вам потребуется:

- подписка Azure AD;
- подписка на HireVue с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление HireVue из коллекции.
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-hirevue-from-the-gallery"></a>Добавление HireVue из коллекции.
Чтобы настроить интеграцию HireVue с Azure AD, необходимо добавить HireVue из коллекции в список управляемых приложений SaaS.

**Чтобы добавить HireVue из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

1. В поле поиска введите **HireVue**.

    ![Создание тестового пользователя Azure AD](./media/hirevue-tutorial/tutorial_hirevue_search.png)

1. На панели результатов выберите **HireVue** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/hirevue-tutorial/tutorial_hirevue_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в HireVue с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в HireVue соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в HireVue.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в HireVue.

Чтобы настроить и проверить единый вход Azure AD в HireVue, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя HireVue](#creating-a-hirevue-test-user)** требуется для создания пользователя Britta Simon в HireVue, связанного с представлением этого пользователя в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
1. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении HireVue.

**Чтобы настроить единый вход Azure AD в HireVue, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **HireVue** щелкните **Единый вход**.

    ![Настройка единого входа][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/hirevue-tutorial/tutorial_hirevue_samlbase.png)

1. В разделе **Домены и URL-адреса HireVue** выполните следующие действия:

    ![Настройка единого входа](./media/hirevue-tutorial/tutorial_hirevue_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате:

    | Среда | URL-адрес |
    |-------------|---|
    | Производство | `https://<companyname>.hirevue.com` |
    | Промежуточная    | `https://<companyname>.stghv.com` |
    
    b. В текстовом поле **Идентификатор** введите URL-адрес в формате:
    
    | Среда | URN |
    |-------------|-----|
    | Производство |`urn:federation:hirevue.com:saml:sp:prod` |
    | Промежуточная    | `urn:federation:hirevue.com:saml:sp:staging`|
    
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов HireVue](mailto:samlsupport@hirevue.com). 
 
1. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/hirevue-tutorial/tutorial_hirevue_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/hirevue-tutorial/tutorial_general_400.png)

1. В разделе **Настройка HireVue** щелкните **Настроить HireVue**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/hirevue-tutorial/tutorial_hirevue_configure.png) 

1. Чтобы настроить единый вход на стороне **HireVue**, нужно отправить скачанный **сертификат (Base64)**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [службу поддержки HireVue](mailto:samlsupport@hirevue.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/hirevue-tutorial/create_aaduser_01.png) 

1. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/hirevue-tutorial/create_aaduser_02.png) 

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/hirevue-tutorial/create_aaduser_03.png) 

1. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/hirevue-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-hirevue-test-user"></a>Создание тестового пользователя HireVue

В этом разделе описано, как создать пользователя Britta Simon в приложении HireVue. Обратитесь в [службу поддержки HireVue](mailto:samlsupport@hirevue.com), чтобы добавить пользователей на платформу HireVue.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon, предоставив этому пользователю доступ к HireVue.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в HireVue, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **HireVue**.

    ![Настройка единого входа](./media/hirevue-tutorial/tutorial_hirevue_app.png) 

1. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент HireVue на панели доступа, вы автоматически войдете в приложение HireVue.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hirevue-tutorial/tutorial_general_01.png
[2]: ./media/hirevue-tutorial/tutorial_general_02.png
[3]: ./media/hirevue-tutorial/tutorial_general_03.png
[4]: ./media/hirevue-tutorial/tutorial_general_04.png

[100]: ./media/hirevue-tutorial/tutorial_general_100.png

[200]: ./media/hirevue-tutorial/tutorial_general_200.png
[201]: ./media/hirevue-tutorial/tutorial_general_201.png
[202]: ./media/hirevue-tutorial/tutorial_general_202.png
[203]: ./media/hirevue-tutorial/tutorial_general_203.png

