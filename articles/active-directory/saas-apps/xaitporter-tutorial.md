---
title: Руководство по интеграции Azure Active Directory с XaitPorter | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.openlocfilehash: 12fb8e5b2b940c48de766a48f59ed0cc342b5356
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421072"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Руководство по интеграции Azure Active Directory с XaitPorter

В этом руководстве описано, как интегрировать XaitPorter с Azure Active Directory (Azure AD).

Интеграция Azure AD с XaitPorter обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к XaitPorter.
- Вы можете включить автоматический вход пользователей в XaitPorter (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с XaitPorter, вам потребуется:

- подписка Azure AD;
- подписка XaitPorter с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление XaitPorter из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-xaitporter-from-the-gallery"></a>Добавление XaitPorter из коллекции
Чтобы настроить интеграцию XaitPorter с Azure AD, необходимо добавить XaitPorter из коллекции в список управляемых приложений SaaS.

**Чтобы добавить XaitPorter из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **XaitPorter**, выберите **XaitPorter** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![XaitPorter в списке результатов](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в XaitPorter с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в XaitPorter соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в XaitPorter.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в XaitPorter.

Чтобы настроить и проверить единый вход Azure AD в XaitPorter, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя XaitPorter](#create-a-xaitporter-test-user)** требуется для того, чтобы в XaitPorter существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении XaitPorter.

**Чтобы настроить единый вход Azure AD в XaitPorter, выполните следующее.**

1. На портале Azure на странице интеграции с приложением **XaitPorter** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

1. В разделе **Домены и URL-адреса приложения XaitPorter** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения XaitPorter](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.xaitporter.com/saml/login`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов XaitPorter](https://www.xait.com/support/).
     
1. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот. 

    ![Ссылка для скачивания сертификата](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/xaitporter-tutorial/tutorial_general_400.png)

1. Предоставьте **IP-адрес** или **URL-адрес метаданных федерации приложений** [группе поддержки SmartRecruiters](https://www.smartrecruiters.com/about-us/contact-us/), чтобы она могла проверить, доступен ли этот IP-адрес вашему экземпляру XaitPorter, настроив список разрешений на своей стороне. 

1. В другом окне веб-браузера войдите на корпоративный веб-сайт XaitPorter в качестве администратора.

1. Щелкните **Admin** (Администратор).

    ![Настройка единого входа](./media/xaitporter-tutorial/user1.png)

1. Из раскрывающегося списка **System Setup** (Настройка системы) выберите **Manage Single Sign-On** (Управление единым входом).

    ![Настройка единого входа](./media/xaitporter-tutorial/user2.png)

1. В разделе **MANAGE SINGLE SIGN-ON** (Управление единым входом) выполните следующие действия.

    ![Настройка единого входа](./media/xaitporter-tutorial/user3.png)

    a. Выберите **Enable Single Sign-On Authentication** (Включить аутентификацию с помощью единого входа).

    b. В текстовое поле **Параметры поставщика удостоверений** вставьте **URL-адрес метаданных федерации приложений**, скопированный на портале Azure, и нажмите кнопку **Fetch** (Получить).

    c. Выберите **Enable Autocreation of Users** (Включить автоматическое создание пользователей).

    d. Последовательно выберите **ОК**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/xaitporter-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/xaitporter-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/xaitporter-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-xaitporter-test-user"></a>Создание тестового пользователя XaitPorter

В этом разделе описано, как создать пользователя Britta Simon в XaitPorter. Обратитесь к [группе поддержки XaitPorter](https://www.xait.com/support/), чтобы добавить пользователей на платформу XaitPorter. Перед использованием единого входа необходимо создать и активировать пользователей. 

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к XaitPorter.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в XaitPorter, выполните следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. Из списка приложений выберите **XaitPorter**.

    ![Ссылка на XaitPorter в списке "Приложения"](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "XaitPorter" на панели доступа, вы автоматически войдете в приложение XaitPorter.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png

