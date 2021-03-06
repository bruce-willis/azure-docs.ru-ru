---
title: Руководство. Интеграция Azure Active Directory с PlanMyLeave | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и PlanMyLeave.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: jeedes
ms.openlocfilehash: 2f5dde2d99844ecc71d72207296cf8c7dac29e46
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424346"
---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>Руководство. Интеграция Azure Active Directory с PlanMyLeave

В этом руководстве описано, как интегрировать PlanMyLeave с Azure Active Directory (Azure AD).

Интеграция PlanMyLeave с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к PlanMyLeave.
- Вы можете включить автоматический вход пользователей в PlanMyLeave (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с PlanMyLeave, вам потребуется следующее:

- подписка Azure AD;
- подписка PlanMyLeave с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление PlanMyLeave из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-planmyleave-from-the-gallery"></a>Добавление PlanMyLeave из коллекции
Чтобы настроить интеграцию PlanMyLeave с Azure AD, необходимо добавить PlanMyLeave из коллекции в список управляемых приложений SaaS.

**Чтобы добавить PlanMyLeave из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **PlanMyLeave**, выберите **PlanMyLeave** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![PlanMyLeave в списке результатов](./media/planmyleave-tutorial/tutorial_planmyleave_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в PlanMyLeave с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в PlanMyLeave соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в PlanMyLeave.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в PlanMyLeave.

Чтобы настроить и проверить единый вход Azure AD в PlanMyLeave, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя приложения PlanMyLeave](#create-a-planmyleave-test-user)** требуется для создания в PlanMyLeave пользователя Britta Simon, связанного с представлением этого же пользователя в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении PlanMyLeave.

**Чтобы настроить единый вход Azure AD в PlanMyLeave, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **PlanMyLeave** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/planmyleave-tutorial/tutorial_planmyleave_samlbase.png)

1. В разделе **Домены и URL-адреса приложения PlanMyLeave** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения PlanMyLeave](./media/planmyleave-tutorial/tutorial_planmyleave_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company-name>.planmyleave.com/Login.aspx`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company-name>.planmyleave.com`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов PlanMyLeave](mailto:support@planmyleave.com). 
 
1. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/planmyleave-tutorial/tutorial_planmyleave_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/planmyleave-tutorial/tutorial_general_400.png)

1. В разделе **PlanMyLeave Configuration** (Конфигурация Fuse) щелкните **Configure PlanMyLeave** (Настроить Fuse), чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка PlanMyLeave](./media/planmyleave-tutorial/tutorial_planmyleave_configure.png) 
1. В другом окне веб-браузера войдите в свой клиент PlanMyLeave в качестве администратора.

1. Перейдите в окно **System Setup** (Настройка системы). Затем в разделе **Security Management** (Управление безопасностью) щелкните **Company SAML settings** (Параметры SAML компании).

    ![Настройка единого входа на стороне приложения](./media/planmyleave-tutorial/tutorial_planmyleave_002.png) 

1. В разделе **SAML Settings** (Параметры SAML) щелкните значок редактора.

    ![Настройка единого входа на стороне приложения](./media/planmyleave-tutorial/tutorial_planmyleave_003.png)

1. В разделе **Update SAML Settings** (Изменение параметров SAML) выполните следующее.

    ![Настройка единого входа на стороне приложения](./media/planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  В текстовое поле **Login URL** (URL-адрес входа) вставьте значение **URL-адрес службы единого входа SAML**, скопированное на портале Azure.

    b.  Откройте скачанные метаданные, скопируйте значение **сертификата X509** и вставьте его в текстовое поле **Сертификат**.

    c. Для параметра "**Is Enable**" (Включено) установите значение "**Yes**" (Да).

    d. Выберите команду **Сохранить**. 

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/planmyleave-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/planmyleave-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/planmyleave-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/planmyleave-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-planmyleave-test-user"></a>Создание тестового пользователя PlanMyLeave

Цель этого раздела — создать пользователя с именем Britta Simon в PlanMyLeave. Приложение PlanMyLeave поддерживает JIT-подготовку. Эта функция включена по умолчанию.

В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к PlanMyLeave (если он еще не создан).

> [!NOTE]
> Если нужно создать пользователя вручную, обратитесь в [службу поддержки PlanMyLeave](mailto:support@planmyleave.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к PlanMyLeave.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в PlanMyLeave, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **PlanMyLeave**.

    ![Ссылка на PlanMyLeave в списке "Приложения"](./media/planmyleave-tutorial/tutorial_planmyleave_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент PlanMyLeave на панели доступа, вы автоматически войдете в приложение PlanMyLeave.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/planmyleave-tutorial/tutorial_general_203.png

