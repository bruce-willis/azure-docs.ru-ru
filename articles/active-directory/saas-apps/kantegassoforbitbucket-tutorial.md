---
title: Руководство по интеграции Azure Active Directory с Kantega SSO for Bitbucket | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Kantega SSO for Bitbucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a81ea48937927e13141642d70093bc322196b2cc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434998"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Руководство по интеграции Azure Active Directory с Kantega SSO for Bitbucket

В этом руководстве описано, как интегрировать Kantega SSO for Bitbucket с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Kantega SSO for Bitbucket обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Kantega SSO for Bitbucket.
- Вы можете включить автоматический вход пользователей в Kantega SSO for Bitbucket (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Kantega SSO for Bitbucket, вам потребуется:

- подписка Azure AD;
- подписка Kantega SSO for Bitbucket с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Kantega SSO for Bitbucket из коллекции.
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Добавление Kantega SSO for Bitbucket из коллекции
Чтобы настроить интеграцию Kantega SSO for Bitbucket с Azure AD, необходимо добавить Kantega SSO for Bitbucket из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Kantega SSO for Bitbucket из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

1. В поле поиска введите **Kantega SSO for Bitbucket**.

    ![Создание тестового пользователя Azure AD](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

1. На панели результатов выберите **Kantega SSO for Bitbucket** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Kantega SSO for Bitbucket с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Kantega SSO for Bitbucket соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Kantega SSO for Bitbucket.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Kantega SSO for Bitbucket.

Чтобы настроить и проверить единый вход Azure AD в Kantega SSO for Bitbucket, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Kantega SSO for Bitbucket](#creating-a-kantega-sso-for-bitbucket-test-user)** требуется для того, чтобы в Kantega SSO for Bitbucket существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
1. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В данном разделе описано, как включить единый вход в Azure AD на портале Azure и настроить его в приложении Kantega SSO for Bitbucket.

**Чтобы настроить единый вход Azure AD в Kantega SSO for Bitbucket, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Kantega SSO for Bitbucket** щелкните **Единый вход**.

    ![Настройка единого входа][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

1. Чтобы использовать режим, инициируемый **IdP**, в разделе **Домены и URL-адреса приложения Kantega SSO for Bitbucket** выполните следующие действия.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`.

1. Чтобы использовать режим, инициируемый **поставщиком услуг**, установите флажок **Показать дополнительные параметры URL-адресов**.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Эти значения предоставляются во время настройки подключаемого модуля Bitbucket, которая описывается далее в этом руководстве.

1. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/tutorial_general_400.png)

1. В другом окне веб-браузера войдите на портал администрирования Bitbucket в качестве администратора.

1. Щелкните шестеренку и выберите **Find new add-ons** (Найти новые надстройки).

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Найдите подключаемый модуль **Kantega SSO for Bitbucket (SAML & Kerberos)** и нажмите кнопку **Install** (Установить), чтобы установить новый подключаемый модуль SAML.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Начнется установка подключаемого модуля.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Установка завершится. Нажмите кнопку **Закрыть**

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon33.png)

1.  Нажмите кнопку **Управление**.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon34.png)
    
1. Щелкните **Configure** (Настройка), чтобы настроить новый подключаемый модуль. 

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. В разделе **SAML** сделайте следующее. Выберите **Azure Active Directory (Azure AD)** из раскрывающегося списка **Добавление поставщика удостоверений**.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Выберите уровень подписки **Базовый**.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. В разделе **Свойства приложения** выполните следующие действия.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Скопируйте значение **App ID URI** (URI кода приложения) и используйте его как **идентификатор, URL-адрес ответа и URL-адрес входа** в разделе **Домены и URL-адреса приложения Kantega SSO for Bitbucket** на портале Azure.

    b. Щелкните **Далее**.

1. В разделе **Metadata import** (Импорт метаданных) выполните следующие действия.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Щелкните **Metadata file on my computer** (Файл метаданных на моем компьютере) и передайте файл метаданных, который вы скачали с портала Azure.

    b. Щелкните **Далее**.

1. В разделе **Name and SSO location** (Имя и расположение единого входа) выполните следующее.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. В текстовом поле **Provider Name** (Имя поставщика) введите имя поставщика (например, Azure AD).

    b. Щелкните **Далее**.

1. Проверьте сертификат для подписи и нажмите кнопку **Next** (Далее).   

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. В разделе **Bitbucket user accounts** (Учетные записи пользователей Bitbucket) выполните следующие действия.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. Щелкните переключатель **Create users in Bitbucket's internal Directory if needed** (При необходимости создать пользователей во внутреннем каталоге Bitbucket) и введите соответствующее имя группы пользователей (это может быть несколько групп, разделенных запятой).

    b. Щелкните **Далее**.

1. Нажмите кнопку **Готово**

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. В разделе **Known domains for Azure AD** (Известные домены для Azure AD) выполните следующие действия.  

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Щелкните **Known domains** (Известные домены) на левой панели страницы.

    b. Введите имя домена в текстовое поле **Known domains** (Известные домены).

    c. Выберите команду **Сохранить**.  

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

1. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

1. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>Создание тестового пользователя Kantega SSO for Bitbucket

Чтобы пользователи Azure AD могли выполнять вход в Bitbucket, они должны быть подготовлены в Bitbucket. В Kantega SSO for Bitbucket подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Выполните вход на свой корпоративный сайт Bitbucket в качестве администратора.

1. Щелкните значок параметров.

    ![Добавление сотрудника](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. В разделе **Administration** (Администрирование) щелкните **Users** (Пользователи).

    ![Добавление сотрудника](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Щелкните **Create user** (Создать пользователя).

    ![Добавление сотрудника](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. На странице **Create User** (Создание пользователя) выполните следующие действия.

    ![Добавление сотрудника](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. В текстовом поле **Username** (Имя пользователя) введите электронный адрес пользователя, например Brittasimon@contoso.com.
    
    b. В текстовом поле **Full Name** (Полное имя) введите полное имя пользователя, например Britta Simon.
    
    c. В текстовом поле **Email address** (Адрес электронной почты) введите адрес электронной почты пользователя, например Brittasimon@contoso.com.

    d. В текстовом поле **Password** (Пароль) введите пароль пользователя.  

    д. В текстовом поле **Confirm Password** (Подтверждение пароля) введите пароль еще раз.

    Е. Щелкните **Create user** (Создать пользователя).   

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Kantega SSO for Bitbucket.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Kantega SSO for Bitbucket, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. Из списка приложений выберите **Kantega SSO for Bitbucket**.

    ![Настройка единого входа](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

1. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Kantega SSO for Bitbucket" на панели доступа, вы автоматически войдете в приложение Kantega SSO for Bitbucket.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_203.png

