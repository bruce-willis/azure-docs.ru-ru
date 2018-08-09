---
title: Руководство. Интеграция Azure Active Directory с Secret Server (On-Premises) | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Secret Server (On-Premises).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: 30a1498ab41f263c77656400c4200313048cc331
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436171"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Руководство. Интеграция Azure Active Directory с Secret Server (On-Premises)

В этом руководстве описано, как интегрировать Secret Server (On-Premises) с Azure Active Directory (Azure AD).

Интеграция Secret Server (On-Premises) с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Secret Server (On-Premises).
- Вы можете включить автоматический вход (единый вход) пользователей в Secret Server (On-Premises) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Secret Server (On-Premises), вам потребуется следующее:

- подписка Azure AD;
- подписка Secret Server (On-Premises) с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Secret Server (On-Premises) из коллекции приложений
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Добавление Secret Server (On-Premises) из коллекции приложений
Чтобы настроить интеграцию Secret Server (On-Premises) с Azure AD, необходимо добавить Secret Server (On-Premises) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Secret Server (On-Premises) из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Secret Server (On-Premises)**, выберите **Secret Server (On-Premises)** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Secret Server (On-Premises) в списке результатов](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Secret Server (On-Premises) на примере тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Secret Server (On-Premises) соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Secret Server (On-Premises).

Чтобы настроить и проверить единый вход Azure AD в Secret Server (On-Premises), вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя в Secret Server (On-Premises)](#create-a-secret-server-on-premises-test-user)** требуется для создания в Secret Server (On-Premises) пользователя Britta Simon, связанного с представлением этого пользователя в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Secret Server (On-Premises).

**Чтобы настроить единый вход Azure AD в Secret Server (On-Premises), выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Secret Server (On-Premises)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

1. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Secret Server (On-Premises)** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. В текстовом поле **Идентификатор** укажите пользовательское значение, например: `https://secretserveronpremises.azure`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx `.

    > [!NOTE]
    > Представленный выше идентификатор сущности используется только для примера. Вы можете выбрать любое уникальное значение для представления экземпляра Secret Server в AAD. Этот идентификатор сущности следует отправить в [группу поддержки приложения Secret Server (On-Premises)](https://thycotic.force.com/support/s/), чтобы они выполнили настройки со своей стороны. Дополнительные сведения см. в [этой статье](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SecretServerURL>/login.aspx`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Secret Server (On-Premises)](https://thycotic.force.com/support/s/).

1. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

1. Установите флажок **Показать дополнительные параметры подписывания сертификатов** и выберите для параметра **Вариант подписывания** значение **Подписывать ответ и утверждение SAML**.

    ![Вариант подписывания](./media/secretserver-on-premises-tutorial/signing.png)

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
1. В разделе **Конфигурация Secret Server (On-Premises)** щелкните **Настройка Secret Server (On-Premises)**, чтобы открыть окно **Настройка входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

1. Чтобы настроить единый вход на стороне **Secret Server (On-Premises)**, нужно отправить **скачанный сертификат (Base64), URL-адрес выхода, идентификатор сущности SAML, URL-адрес службы единого входа SAML** и **идентификатор сущности SAML** [группе поддержки Secret Server (On-Premises)](https://thycotic.force.com/support/s/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Создание тестового пользователя в Secret Server (On-Premises)

В этом разделе описано, как создать пользователя Britta Simon в приложении Secret Server (On-Premises). Обратитесь к [группе поддержки Secret Server (On-Premises)](https://thycotic.force.com/support/s/), чтобы они добавили пользователей на платформу Secret Server (On-Premises). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Secret Server (On-Premises).

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Secret Server (On-Premises), сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

1. В списке приложений выберите **Secret Server (On-Premises)**.

    ![Ссылка на Secret Server (On-Premises) в списке приложений](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Secret Server (On-Premises) на панели доступа, вы автоматически войдете в приложение Secret Server (On-Premises).
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

