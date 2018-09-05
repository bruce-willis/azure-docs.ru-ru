---
title: Руководство по интеграции Azure Active Directory с ArcGIS Enterprise | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory и ArcGIS Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 24809e9d-a4aa-4504-95a9-e4fcf484f431
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: jeedes
ms.openlocfilehash: ca5bf7ae49cf120c0566419ccadeff92433c6467
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819397"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Руководство по интеграции Azure Active Directory с ArcGIS Enterprise

В этом руководстве описано, как интегрировать ArcGIS Enterprise с Azure Active Directory (Azure AD).

Интеграция ArcGIS Enterprise с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к ArcGIS Enterprise.
- Вы можете включить автоматический вход пользователей в ArcGIS Enterprise (единый вход) с использованием их учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с ArcGIS Enterprise, вам потребуются:

- подписка Azure AD;
- подписка ArcGIS Enterprise с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление ArcGIS Enterprise из коллекции;
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-arcgis-enterprise-from-the-gallery"></a>Добавление ArcGIS Enterprise из коллекции

Чтобы настроить интеграцию ArcGIS Enterprise в Azure AD, необходимо добавить ArcGIS Enterprise из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ArcGIS Enterprise из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **ArcGIS Enterprise**, выберите **ArcGIS Enterprise** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![ArcGIS Enterprise в списке результатов](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в ArcGIS Enterprise с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD нужно знать, какой пользователь в ArcGIS Enterprise соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ArcGIS Enterprise.

Чтобы настроить и проверить единый вход между Azure AD и ArcGIS Enterprise, выполните действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ArcGIS Enterprise](#create-an-arcgis-enterprise-test-user)** требуется для того, чтобы в ArcGIS Enterprise существовал пользователь Britta Simon, связанный с соответствующим пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении ArcGIS Enterprise.

**Чтобы настроить единый вход Azure AD с ArcGIS Enterprise, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **ArcGIS Enterprise** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения ArcGIS Enterprise** сделайте следующее:

    ![Сведения о домене и URL-адресах в приложении ArcGIS Enterprise](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `<EXTERNAL_DNS_NAME>.portal`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах в приложении ArcGIS Enterprise](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [группу поддержки клиентов ArcGIS Enterprise](mailto:nshampur@esri.com). Вы получите значение идентификатора из раздела **Set Identity Provider** (Назначение поставщика удостоверений), который описан далее в этом руководстве.

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_certificate.png)

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/arcgisenterprise-tutorial/tutorial_general_400.png)

7. В другом окне веб-браузера войдите на корпоративный сайт ArcGIS Enterprise в качестве администратора.

8. Выберите **Organization > Edit Settings** (Организация > Изменить параметры).

    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure1.png)

9. Выберите вкладку **Security** (Безопасность).

    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure2.png)

10. Прокрутите вниз до раздела **Enterprise Logins via SAML** (Корпоративный вход через SAML) и выберите **Set Enterprise Login** (Настроить корпоративный вход).

    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure3.png)

11. В разделе **Set Identity Provider** (Назначение поставщика удостоверений) выполните следующие действия:

    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure4.png)

    a. В текстовом поле **Имя** укажите имя, например **Azure Active Directory Test**.

    b. В текстовое поле **URL** (URL-адрес) вставьте значение **URL-адреса метаданных федерации приложения**, скопированное на портале Azure.

    c. Щелкните ссылку **Show advanced settings** (Показать дополнительные параметры), скопируйте значение **идентификатора сущности** и вставьте его в текстовое поле **Идентификатор** в разделе **Домены и URL-адреса приложения ArcGIS Enterprise** на портале Azure.
    
    ![Настройка ArcGIS Enterprise](./media/arcgisenterprise-tutorial/configure5.png)

    d. Нажмите кнопку **Update Identity Provider** (Обновить поставщика удостоверений).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/arcgisenterprise-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/arcgisenterprise-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/arcgisenterprise-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/arcgisenterprise-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-an-arcgis-enterprise-test-user"></a>Создание тестового пользователя ArcGIS Enterprise

Цель этого раздела — создать пользователя с именем Britta Simon в ArcGIS Enterprise. ArcGIS Enterprise поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к ArcGIS Enterprise (если он еще не создан).

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к [группе поддержки ArcGIS Enterprise](mailto:nshampur@esri.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к ArcGIS Enterprise.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению ArcGIS Enterprise, выполните следующие шаги:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **ArcGIS Enterprise**.

    ![Ссылка ArcGIS Enterprise в списке приложений](./media/arcgisenterprise-tutorial/tutorial_arcgisenterprise_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку ArcGIS Enterprise на панели доступа, вы автоматически войдете в приложение ArcGIS Enterprise.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgisenterprise-tutorial/tutorial_general_01.png
[2]: ./media/arcgisenterprise-tutorial/tutorial_general_02.png
[3]: ./media/arcgisenterprise-tutorial/tutorial_general_03.png
[4]: ./media/arcgisenterprise-tutorial/tutorial_general_04.png

[100]: ./media/arcgisenterprise-tutorial/tutorial_general_100.png

[200]: ./media/arcgisenterprise-tutorial/tutorial_general_200.png
[201]: ./media/arcgisenterprise-tutorial/tutorial_general_201.png
[202]: ./media/arcgisenterprise-tutorial/tutorial_general_202.png
[203]: ./media/arcgisenterprise-tutorial/tutorial_general_203.png