---
title: Руководство по интеграции Azure Active Directory с My Award Points Top Sub/Top Team | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и My Award Points Top Sub/Top Team.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2018
ms.author: jeedes
ms.openlocfilehash: 9e3e3ff05836cb49d46a25ef3fa55d40a0bd134f
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184675"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Руководство по интеграции Azure Active Directory с My Award Points Top Sub/Top Team

Из этого руководства вы узнаете, как интегрировать My Award Points Top Sub/Top Team с Azure Active Directory (AAD).

Интеграция Azure AD с приложением My Award Points Top Sub/Top Team обеспечивает следующие преимущества:

- Вы можете через Azure AD настраивать доступ в My Award Points Top Sub/Top Team.
- Вы можете включить автоматический вход пользователей в My Award Points Top Sub/Top Team (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с My Award Points Top Sub/Top Team, вам потребуется следующее:

- подписка Azure AD;
- подписка My Award Points Top Sub/Top Team с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление My Award Points Top Sub/Top Team из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Добавление My Award Points Top Sub/Top Team из коллекции

Чтобы настроить интеграцию My Award Points Top Sub/Top Team с Azure AD, нужно добавить My Award Points Top Sub/Top Team из коллекции в список управляемых приложений SaaS.

**Чтобы добавить My Award Points Top Sub/Top Team из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **My Award Points Top Sub/Top Team**, выберите **My Award Points Top Sub/Top Team** на панели результатов и щелкните **Добавить**, чтобы добавить это приложение.

    ![Приложение My Award Points Top Sub/Top Team в списке результатов](./media/myawardpoints-tutorial/tutorial_myawardpoints_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Из этого раздела вы узнаете, как настроить и проверить единый вход Azure AD в My Award Points Top Sub/Top Team с использованием данных тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в My Award Points Top Sub/Top Team соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в My Award Points Top Sub/Top Team.

Чтобы настроить и проверить единый вход Azure AD в My Award Points Top Sub/Top Team, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя My Award Points Top Sub/Top Team](#create-a-my-award-points-top-subtop-team-test-user)** — требуется для создания пользователя Britta Simon в приложении My Award Points Top Sub/Top Team, связанного с представлением этого же пользователя в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Из этого раздела вы узнаете, как включить единый вход Azure AD на портале Azure и настроить его в приложении My Award Points Top Sub/Top Team.

**Чтобы настроить единый вход в Azure AD с My Award Points Top Sub/Top Team, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **My Award Points Top Sub/Top Team** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/myawardpoints-tutorial/tutorial_myawardpoints_samlbase.png)

3. В разделе **My Award Points Top Sub/Top Team Domain and URLs** (Домен и URL-адреса My Award Points Top Sub/Top Team) выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для My Award Points Top Sub/Top Team](./media/myawardpoints-tutorial/tutorial_myawardpoints_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<SAMLENTITYID>`

    > [!NOTE]
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com).

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/myawardpoints-tutorial/tutorial_myawardpoints_certificate.png)

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/myawardpoints-tutorial/tutorial_general_400.png)

6. Чтобы настроить единый вход на стороне **My Award Points Top Sub/Top Team**, отправьте скачанный **XML-файл метаданных** в [службу поддержки клиентов My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/myawardpoints-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/myawardpoints-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/myawardpoints-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/myawardpoints-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-my-award-points-top-subtop-team-test-user"></a>Создание тестового пользователя My Award Points Top Sub/Top Team

В этом разделе описано, как создать пользователя Britta Simon в приложении My Award Points Top Sub/Top Team. Обратитесь к [службе поддержки клиентов My Award Points Top Sub/Top Team](mailto:myawardpoints@biworldwide.com), чтобы добавить пользователей на платформу My Award Points Top Sub/Top Team. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к My Award Points Top Sub/Top Team и возможность использовать единый вход Azure.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в приложение My Award Points Top Sub/Top Team, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **My Award Points Top Sub/Top Team**.

    ![Ссылка на My Award Points Top Sub/Top Team в списке приложений](./media/myawardpoints-tutorial/tutorial_myawardpoints_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Когда вы нажмете плитку My Award Points Top Sub/Top Team на панели доступа, вы автоматически войдете в приложение My Award Points Top Sub/Top Team.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/myawardpoints-tutorial/tutorial_general_01.png
[2]: ./media/myawardpoints-tutorial/tutorial_general_02.png
[3]: ./media/myawardpoints-tutorial/tutorial_general_03.png
[4]: ./media/myawardpoints-tutorial/tutorial_general_04.png

[100]: ./media/myawardpoints-tutorial/tutorial_general_100.png

[200]: ./media/myawardpoints-tutorial/tutorial_general_200.png
[201]: ./media/myawardpoints-tutorial/tutorial_general_201.png
[202]: ./media/myawardpoints-tutorial/tutorial_general_202.png
[203]: ./media/myawardpoints-tutorial/tutorial_general_203.png