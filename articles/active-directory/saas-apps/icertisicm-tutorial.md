---
title: Учебник. Интеграция Azure Active Directory с Icertis Contract Management Platform | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Icertis Contract Management Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6627e6dd-f559-4cd4-a509-f6d9a4961b49
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: 8a158f2edb5fd9c9b72f4de2dcb27cf6b3c71874
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444990"
---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>Руководство. Интеграция Azure Active Directory с приложением Icertis Contract Management Platform

В этом руководстве описано, как интегрировать Icertis Contract Management Platform с Azure Active Directory (Azure AD).

Интеграция Icertis Contract Management Platform с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD можно контролировать доступ к Icertis Contract Management Platform.
- Вы можете включить автоматический вход пользователей в Icertis Contract Management Platform (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Icertis Contract Management Platform, вам потребуется следующее:

- подписка Azure AD;
- подписка Icertis Contract Management Platform с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Icertis Contract Management Platform из коллекции.
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-icertis-contract-management-platform-from-the-gallery"></a>Добавление Icertis Contract Management Platform из коллекции.
Чтобы настроить интеграцию Icertis Contract Management Platform с Azure AD, вам потребуется добавить Icertis Contract Management Platform из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Icertis Contract Management Platform из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

1. В поле поиска введите **Icertis Contract Management Platform**.

    ![Создание тестового пользователя Azure AD](./media/icertisicm-tutorial/tutorial_icertisicm_search.png)

1. В области результатов выберите **Icertis Contract Management Platform** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/icertisicm-tutorial/tutorial_icertisicm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Icertis Contract Management Platform для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Icertis Contract Management Platform соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Icertis Contract Management Platform

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Icertis Contract Management Platform.

Чтобы настроить и проверить единый вход Azure AD в Icertis Contract Management Platform, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Icertis Contract Management Platform](#creating-an-icertis-contract-management-platform-test-user)** требуется для создания пользователя Britta Simon в Icertis Contract Management Platform, связанного с представлением этого пользователя в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
1. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Icertis Contract Management Platform.

**Чтобы настроить единый вход Azure AD в Icertis Contract Management Platform, сделайте следующее:**

1. На портале управления Azure на странице интеграции с приложением **Icertis Contract Management Platform** щелкните **Единый вход**.

    ![Настройка единого входа][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/icertisicm-tutorial/tutorial_icertisicm_samlbase.png)

1. В разделе **Домены и URL-адреса Icertis Contract Management Platform** выполните следующие действия:

    ![Настройка единого входа](./media/icertisicm-tutorial/tutorial_icertisicm_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.icertis.com`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.icertis.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Icertis Contract Management Platform](https://www.icertis.com/company/contact/). 

1. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Настройка единого входа](./media/icertisicm-tutorial/tutorial_icertisicm_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/icertisicm-tutorial/tutorial_general_400.png)

1. В разделе **Настройка Icertis Contract Management Platform** щелкните **Настроить Icertis Contract Management Platform**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/icertisicm-tutorial/tutorial_icertisicm_configure.png) 

1. Чтобы настроить единый вход на стороне **Icertis Contract Management Platform**, нужно отправить скачанный **XML-файл метаданных**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [службу поддержки Icertis Contract Management Platform](https://www.icertis.com/company/contact/).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/icertisicm-tutorial/create_aaduser_01.png) 

1. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/icertisicm-tutorial/create_aaduser_02.png) 

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/icertisicm-tutorial/create_aaduser_03.png) 

1. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/icertisicm-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-icertis-contract-management-platform-test-user"></a>Создание тестового пользователя Icertis Contract Management Platform

В этом разделе описано, как создать пользователя Britta Simon в Icertis Contract Management Platform. Чтобы добавить пользователей в Icertis Contract Management Platform, обратитесь в [службу поддержки Icertis Contract Management Platform](https://www.icertis.com/company/contact/).

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Icertis Contract Management Platform.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Icertis Contract Management Platform, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **Icertis Contract Management Platform**.

    ![Настройка единого входа](./media/icertisicm-tutorial/tutorial_icertisicm_app.png) 

1. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Icertis Contract Management Platform на панели доступа, вы автоматически войдете в приложение Icertis Contract Management Platform.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/icertisicm-tutorial/tutorial_general_01.png
[2]: ./media/icertisicm-tutorial/tutorial_general_02.png
[3]: ./media/icertisicm-tutorial/tutorial_general_03.png
[4]: ./media/icertisicm-tutorial/tutorial_general_04.png

[100]: ./media/icertisicm-tutorial/tutorial_general_100.png

[200]: ./media/icertisicm-tutorial/tutorial_general_200.png
[201]: ./media/icertisicm-tutorial/tutorial_general_201.png
[202]: ./media/icertisicm-tutorial/tutorial_general_202.png
[203]: ./media/icertisicm-tutorial/tutorial_general_203.png

