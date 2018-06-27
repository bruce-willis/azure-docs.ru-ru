---
title: Руководство по интеграции Azure Active Directory с ForeSee CX Suite | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и ForeSee CX Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5f4b7830-6186-4d17-b77b-504d4192bfde
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: jeedes
ms.openlocfilehash: e27bc853db6236165d77f2fb3418bab1170400b2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36227709"
---
# <a name="tutorial-azure-active-directory-integration-with-foresee-cx-suite"></a>Руководство по интеграции Azure Active Directory с ForeSee CX Suite

В этом руководстве описано, как интегрировать ForeSee CX Suite с Azure Active Directory (Azure AD).

Интеграция приложения ForeSee CX Suite с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к ForeSee CX Suite.
- Вы можете включить автоматический вход пользователей в ForeSee CX Suite (единый вход) с использованием их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с ForeSee CX Suite, вам потребуется:

- подписка Azure AD;
- подписка ForeSee CX Suite с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.
Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление ForeSee CX Suite из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-foresee-cx-suite-from-the-gallery"></a>Добавление ForeSee CX Suite из коллекции
Чтобы настроить интеграцию приложения ForeSee CX Suite с Azure AD, вам нужно добавить это приложение из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ForeSee CX Suite из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **ForeSee CX Suite**, выберите **ForeSee CX Suite** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![ForeSee CX Suite в списке результатов](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в ForeSee CX Suite с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в ForeSee CX Suite соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ForeSee CX Suite.

Чтобы настроить и проверить единый вход Azure AD в ForeSee CX Suite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения ForeSee CX Suite](#create-a-foresee-cx-suite-test-user)** требуется для того, чтобы в ForeSee CX Suite существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении ForeSee CX Suite.

**Чтобы настроить единый вход Azure AD в ForeSee CX Suite, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **ForeSee CX Suite** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_samlbase.png)

3. Если у вас есть **файл метаданных поставщика службы**, выполните следующие действия в разделе **Домены и URL-адреса приложения ForeSee CX Suite**:

    ![Сведения о домене и URL-адресах единого входа приложения ForeSee CX Suite](./media/foreseecxsuite-tutorial/upload.png)

    a. Щелкните **Отправить файл метаданных**.

    ![Сведения о домене и URL-адресах единого входа приложения ForeSee CX Suite](./media/foreseecxsuite-tutorial/tutorial_foreseen_uploadconfig.png)

    Б. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    c. После успешной передачи **файла метаданных поставщика службы** значение **Идентификатор** автоматически добавляется в соответствующее текстовое поле в разделе **Домены и URL-адреса приложения ForeSee CX Suite**, как показано ниже:

    ![Сведения о домене и URL-адресах единого входа приложения ForeSee CX Suite](./media/foreseecxsuite-tutorial/urlupload.png)

4. Если у вас нет **файла метаданных поставщика службы**, выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа приложения ForeSee CX Suite](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://cxsuite.foresee.com/`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.okta.com/saml2/service-provider/<UniqueID>`

    > [!NOTE]
    > Значение идентификатора приведено для примера и не является реальным. Вместо него нужно указать фактический идентификатор. Для получения этого значения обратитесь в [группу поддержки клиентов ForeSee CX Suite](mailto:support@foresee.com).

5. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_certificate.png)

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/foreseecxsuite-tutorial/tutorial_general_400.png)

7. Чтобы настроить единый вход на стороне **ForeSee CX Suite**, отправьте скачанный **XML-файл метаданных** [группе поддержки ForeSee CX Suite](mailto:support@foresee.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/foreseecxsuite-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/foreseecxsuite-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/foreseecxsuite-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/foreseecxsuite-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-foresee-cx-suite-test-user"></a>Создание тестового пользователя ForeSee CX Suite

В этом разделе описано, как создать пользователя Britta Simon в приложении ForeSee CX Suite. Обратитесь в [группу поддержки ForeSee CX Suite](mailto:support@foresee.com), чтобы добавить домен или пользователей, которых нужно включить в список разрешений на платформе ForeSee CX Suite. Когда группа поддержки добавит домен, его пользователи будут автоматически подготовлены для платформы ForeSee CX Suite. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ForeSee CX Suite.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в приложении ForeSee CX Suite, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **ForeSee CX Suite**.

    ![Ссылка на ForeSee CX Suite в списке "Приложения"](./media/foreseecxsuite-tutorial/tutorial_foreseecxsuite_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку "ForeSee CX Suite" на панели доступа, вы автоматически войдете в приложение ForeSee CX Suite.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/foreseecxsuite-tutorial/tutorial_general_01.png
[2]: ./media/foreseecxsuite-tutorial/tutorial_general_02.png
[3]: ./media/foreseecxsuite-tutorial/tutorial_general_03.png
[4]: ./media/foreseecxsuite-tutorial/tutorial_general_04.png

[100]: ./media/foreseecxsuite-tutorial/tutorial_general_100.png

[200]: ./media/foreseecxsuite-tutorial/tutorial_general_200.png
[201]: ./media/foreseecxsuite-tutorial/tutorial_general_201.png
[202]: ./media/foreseecxsuite-tutorial/tutorial_general_202.png
[203]: ./media/foreseecxsuite-tutorial/tutorial_general_203.png

