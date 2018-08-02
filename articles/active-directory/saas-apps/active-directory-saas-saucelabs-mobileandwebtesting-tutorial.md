---
title: Руководство по интеграции Azure Active Directory с Sauce Labs - Mobile and Web Testing | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Sauce Labs - Mobile and Web Testing.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.openlocfilehash: 2c7c0283178e35fb8065bca3c6e380445ab43ae5
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240574"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Руководство по интеграции Azure Active Directory с Sauce Labs - Mobile and Web Testing

В этом руководстве описано, как интегрировать Sauce Labs - Mobile and Web Testing с Azure Active Directory (Azure AD).

Интеграция Sauce Labs - Mobile and Web Testing с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Sauce Labs - Mobile and Web Testing.
- Вы можете настроить автоматический вход пользователей в Sauce Labs - Mobile and Web Testing (единый вход) под учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Sauce Labs - Mobile and Web Testing, вам потребуются:

- подписка Azure AD;
- подписка Sauce Labs - Mobile and Web Testing с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Sauce Labs - Mobile and Web Testing из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Добавление Sauce Labs - Mobile and Web Testing из коллекции
Чтобы настроить интеграцию Sauce Labs - Mobile and Web Testing с Azure AD, необходимо добавить Sauce Labs - Mobile and Web Testing из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Sauce Labs - Mobile and Web Testing из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Sauce Labs - Mobile and Web Testing**, выберите **Sauce Labs - Mobile and Web Testing** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Sauce Labs - Mobile and Web Testing в списке результатов](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описывается настройка и проверка единого входа Azure AD в Sauce Labs - Mobile and Web Testing с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход, Azure AD необходимо знать, какой пользователь в Sauce Labs - Mobile and Web Testing соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Sauce Labs - Mobile and Web Testing.

Чтобы настроить и проверить единый вход Azure AD в Sauce Labs - Mobile and Web Testing, потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Sauce Labs - Mobile and Web Testing](#create-a-sauce-labs---mobile-and-web-testing-test-user)** требуется для того, чтобы в Sauce Labs - Mobile and Web Testing существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описывается, как включить единый вход Azure AD на портале Azure и настроить единый вход в приложении Sauce Labs - Mobile and Web Testing.

**Чтобы настроить единый вход Azure AD в Sauce Labs - Mobile and Web Testing, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Sauce Labs - Mobile and Web Testing** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Sauce Labs - Mobile and Web Testing** не нужно выполнять никаких действий, так как приложение уже предварительно интегрировано с Azure.

    ![Сведения о домене и URL-адресах единого входа для Sauce Labs - Mobile and Web Testing](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. В другом окне веб-браузера войдите на сайт Sauce Labs - Mobile and Web Testing своей организации в качестве администратора.

7. Щелкните значок **User** (Пользователь) и выберите вкладку **Team Management** (Управление командой).

    ![Настройка единого входа](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. Введите доменное имя в текстовом поле **Domain name** (Доменное имя).

    ![Настройка единого входа](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. Выберите вкладку **Configure** (Настройка).

    ![Настройка единого входа](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. В разделе **Configure Single Sign On** (Настройка единого входа) сделайте следующее.

    ![Настройка единого входа](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Нажмите кнопку **Browse** (Обзор) и передайте файл метаданных, скачанный из Azure AD.

    b. Установите флажок **ALLOW JUST-IN-TIME PROVISIONING** (Разрешить JIT-подготовку).

    c. Выберите команду **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>Создание тестового пользователя Sauce Labs - Mobile and Web Testing

Цель этого раздела — создать в приложении Sauce Labs - Mobile and Web Testing пользователя с именем Britta Simon. Приложение Sauce Labs - Mobile and Web Testing поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь создается при попытке получить доступ к приложению Sauce Labs - Mobile and Web Testing (если он еще не создан).
>[!Note]
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки Sauce Labs - Mobile and Web Testing](mailto:support@saucelabs.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Sauce Labs - Mobile and Web Testing.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в Sauce Labs - Mobile and Web Testing, выполните следующие действия**.

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **Sauce Labs - Mobile and Web Testing**.

    ![Ссылка на Sauce Labs - Mobile and Web Testing в списке приложений](./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Sauce Labs - Mobile and Web Testing" на панели доступа, вы автоматически войдете в приложение Sauce Labs - Mobile and Web Testing.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png
