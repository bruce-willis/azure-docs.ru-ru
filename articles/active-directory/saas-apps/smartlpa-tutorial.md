---
title: Руководство по интеграции Azure Active Directory с SmartLPA | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и SmartLPA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ed5f6580-bee6-4f44-aceb-f8f7feda3fb8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: jeedes
ms.openlocfilehash: eedec7de74f3bdd7ae43d1b1eb0decd5fe83ebf6
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075584"
---
# <a name="tutorial-azure-active-directory-integration-with-smartlpa"></a>Учебник. Интеграция Azure Active Directory с SmartLPA

В этом руководстве описано, как интегрировать SmartLPA с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением SmartLPA обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к SmartLPA.
- Вы можете включить автоматический вход пользователей в SmartLPA (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с SmartLPA, вам потребуется:

- подписка Azure AD;
- подписка SmartLPA с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SmartLPA из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-smartlpa-from-the-gallery"></a>Добавление SmartLPA из коллекции
Чтобы настроить интеграцию SmartLPA с Azure AD, необходимо добавить SmartLPA из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SmartLPA из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SmartLPA**, выберите **SmartLPA** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SmartLPA в списке результатов](./media/smartlpa-tutorial/tutorial_smartlpa_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SmartLPA с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в SmartLPA соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в SmartLPA.

Чтобы настроить и проверить единый вход Azure AD в SmartLPA, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SmartLPA](#create-a-smartlpa-test-user)** требуется для того, чтобы в SmartLPA существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SmartLPA.

**Чтобы настроить единый вход Azure AD в SmartLPA, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **SmartLPA** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/smartlpa-tutorial/tutorial_smartlpa_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SmartLPA** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения SmartLPA](./media/smartlpa-tutorial/tutorial_smartlpa_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<TENANTNAME>.smartlpa.com/`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<TENANTNAME>.smartlpa.com/<UNIQUE ID>`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь в [службу поддержки клиентов SmartLPA](mailto:support@smartlpa.com).

4. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/smartlpa-tutorial/tutorial_smartlpa_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/smartlpa-tutorial/tutorial_general_400.png)

6. В разделе **Настройка SmartLPA** щелкните **Настроить SmartLPA**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация SmartLPA](./media/smartlpa-tutorial/tutorial_smartlpa_configure.png) 

7. Чтобы настроить единый вход на стороне **SmartLPA**, нужно отправить скачанный **сертификат в формате Base64**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [службу поддержки SmartLPA](mailto:support@smartlpa.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/smartlpa-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/smartlpa-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/smartlpa-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/smartlpa-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-smartlpa-test-user"></a>Создание тестового пользователя в SmartLPA

В этом разделе описано, как создать пользователя Britta Simon в приложении SmartLPA. Обратитесь в [службу поддержки SmartLPA](mailto:support@smartlpa.com), чтобы добавить пользователей на платформу SmartLPA. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SmartLPA.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению SmartLPA, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SmartLPA**.

    ![Ссылка на приложение SmartLPA в списке "Приложения"](./media/smartlpa-tutorial/tutorial_smartlpa_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент SmartLPA на панели доступа, вы автоматически войдете в приложение SmartLPA.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/smartlpa-tutorial/tutorial_general_01.png
[2]: ./media/smartlpa-tutorial/tutorial_general_02.png
[3]: ./media/smartlpa-tutorial/tutorial_general_03.png
[4]: ./media/smartlpa-tutorial/tutorial_general_04.png

[100]: ./media/smartlpa-tutorial/tutorial_general_100.png

[200]: ./media/smartlpa-tutorial/tutorial_general_200.png
[201]: ./media/smartlpa-tutorial/tutorial_general_201.png
[202]: ./media/smartlpa-tutorial/tutorial_general_202.png
[203]: ./media/smartlpa-tutorial/tutorial_general_203.png

