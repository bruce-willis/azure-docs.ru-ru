---
title: Руководство по интеграции Azure Active Directory с Attendance Management Services | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Attendance Management Services.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: c5422c9894c66348d571b757e50073d2a5501c7b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440104"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Руководство по интеграции Azure Active Directory с Attendance Management Services

В этом руководстве описано, как интегрировать Attendance Management Services с Azure Active Directory (Azure AD).

Интеграция Azure AD с Attendance Management Services обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Attendance Management Services.
- Вы можете включить автоматический вход пользователей в Attendance Management Services (единый вход) с применением учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Attendance Management Services, вам потребуется следующее:

- подписка Azure AD;
- подписка Attendance Management Services с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Attendance Management Services из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-attendance-management-services-from-the-gallery"></a>Добавление Attendance Management Services из коллекции
Чтобы настроить интеграцию Attendance Management Services с Azure AD, вам потребуется добавить Attendance Management Services из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Attendance Management Services из коллекции, выполните следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Attendance Management Services**, выберите **Attendance Management Services** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Attendance Management Services в списке результатов](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Attendance Management Services с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, в Azure AD необходимо указать, какой пользователь в Attendance Management Services соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем Attendance Management Services.

Чтобы настроить и проверить единый вход Azure AD в Attendance Management Services, вам потребуется выполнить следующие действия.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Attendance Management Services](#create-an-attendance-management-service-test-user)** требуется для создания соответствующего пользователя Britta Simon в Attendance Management Services, связанного с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе мы включим на портале Azure единый вход Azure AD и настроим его в приложении Attendance Management Services.

**Чтобы настроить единый вход Azure AD в Attendance Management Services, выполните следующее.**

1. На портале Azure на странице интеграции с приложением **Attendance Management Services** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

1. В разделе **Домены и URL-адреса приложения Attendance Management Services** выполните указанные ниже действия.

    ![Сведения о домене и URL-адресах единого входа для Attendance Management Services](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://id.obc.jp/<tenant information >/`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Обратитесь к [группу поддержки Attendance Management Services](http://www.obcnet.jp/) для получения этих значений.

1. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/attendancemanagementservices-tutorial/tutorial_general_400.png)

1. В разделе **Конфигурация Attendance Management Services** щелкните **Настроить Attendance Management Services**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Quick Reference** (Краткий справочник).

    ![Конфигурация Attendance Management Services](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

1. В другом окне браузера войдите на свой корпоративный сайт Attendance Management Services в качестве администратора.

1. Щелкните **SAML authentication** (Аутентификация SAML) в разделе **Security management** (Управление безопасностью).

    ![Конфигурация Attendance Management Services](./media/attendancemanagementservices-tutorial/user1.png)

1. Выполните следующие действия:

    ![Конфигурация Attendance Management Services](./media/attendancemanagementservices-tutorial/user2.png)

    a. Установите флажок **Use SAML authentication** (Использовать аутентификацию SAML).

    b. В текстовое поле **Identifier** (Идентификатор) вставьте значение **идентификатора сущности SAML**, скопированное на портале Azure. 

    c. В текстовое поле **Authentication endpoint URL** (URL-адрес конечной точки аутентификации) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    d. Щелкните **Select a file** (Выбрать файл), чтобы передать сертификат, скачанный из Azure AD.

    д. Установите флажок **Disable password authentication** (Отключить проверку пароля).

    Е. Щелкните **Registration** (Регистрация).

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения. После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/attendancemanagementservices-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/attendancemanagementservices-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/attendancemanagementservices-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-attendance-management-services-test-user"></a>Создание тестового пользователя Attendance Management Services

Чтобы пользователи Azure AD могли входить в Attendance Management Services, их необходимо подготовить в Attendance Management Services. В случае Attendance Management Services подготовка выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите на свой корпоративный сайт Attendance Management Services в качестве администратора.

1. Щелкните **User management** (Управление пользователями) в разделе **Security management** (Управление безопасностью).

    ![Добавление сотрудника](./media/attendancemanagementservices-tutorial/user5.png)

1. Щелкните **New rules login** (Создать имя для входа на основе правил).

    ![Добавление сотрудника](./media/attendancemanagementservices-tutorial/user3.png)

1. В разделе **OBCiD information** (Сведения OBCiD) сделайте следующее.

    ![Добавление сотрудника](./media/attendancemanagementservices-tutorial/user4.png)

    a. В текстовое поле **OBCiD** введите электронную почту пользователя, например **BrittaSimon@contoso.com**.

    b. В текстовом поле **Password** (Пароль) введите пароль пользователя.

    c. Щелкните **Registration** (Регистрация).


### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Attendance Management Services, чтобы этот пользователь мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Attendance Management Services, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. Из списка приложений выберите **Attendance Management Services**.

    ![Ссылка на Attendance Management Services в списке "Приложения"](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Attendance Management Services" на панели доступа, вы автоматически войдете в приложение Attendance Management Services.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/attendancemanagementservices-tutorial/tutorial_general_203.png

