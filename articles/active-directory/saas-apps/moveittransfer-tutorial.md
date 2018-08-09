---
title: Руководство по интеграции Azure Active Directory с приложением MOVEit Transfer - Azure AD integration | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в MOVEit Transfer - Azure AD integration.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: jeedes
ms.openlocfilehash: e73ca95c27e7c9ef0799107dadc58c17aea5a9ca
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435922"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Руководство по интеграции Azure Active Directory с MOVEit Transfer - Azure AD integration

В этом руководстве, вы узнаете, как интегрировать приложение MOVEit Transfer - Azure AD integration с Azure Active Directory (Azure AD).

Интеграция MOVEit Transfer - Azure AD integration с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к MOVEit Transfer - Azure AD integration.
- Вы можете включить автоматический вход пользователей в MOVEit Transfer - Azure AD integration (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с MOVEit Transfer - Azure AD integration, вам потребуется:

- подписка Azure AD;
- подписка MOVEit Transfer с Azure AD integration с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление приложения MOVEit Transfer - Azure AD integration из коллекции.
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Добавление приложения MOVEit Transfer - Azure AD integration из коллекции
Чтобы настроить интеграцию MOVEit Transfer - Azure AD integration с Azure AD, необходимо добавить MOVEit Transfer - Azure AD integration из коллекции в список управляемых приложений SaaS.

**Чтобы добавить MOVEit Transfer - Azure AD integration из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **MOVEit Transfer - Azure AD integration**, выберите **MOVEit Transfer - Azure AD integration** на панели результатов и щелкните **Добавить**, чтобы добавить приложение.

    ![Приложение MOVEit Transfer - Azure AD integration в списке результатов](./media/moveittransfer-tutorial/tutorial_moveittransfer_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе показано, как настроить и проверить единый вход Azure AD в приложение MOVEit Transfer - Azure AD integration с использованием тестового пользователя Britta Simon.

Для работы единого входа Azure AD необходимо знать, какой пользователь в MOVEit Transfer - Azure AD integration соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в MOVEit Transfer - Azure AD integration.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в MOVEit Transfer - Azure AD integration.

Чтобы настроить и проверить единый вход Azure AD в MOVEit Transfer - Azure AD integration, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя MOVEit Transfer - Azure AD integration](#create-a-moveit-transfer---azure-ad-integration-test-user)** требуется для создания в MOVEit Transfer - Azure AD integration пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении MOVEit Transfer - Azure AD integration.

**Чтобы настроить единый вход Azure AD в MOVEit Transfer - Azure AD integration, выполните следующие действия.**

1. На портале Azure на странице приложения **MOVEit Transfer - Azure AD integration** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/moveittransfer-tutorial/tutorial_moveittransfer_samlbase.png)

1. В разделе **Домены и URL-адреса приложения MOVEit Transfer - Azure AD integration** выполните следующие действия:

    ![Настройка единого входа](./media/moveittransfer-tutorial/tutorial_moveittransfer_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://contoso.com`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://contoso.com/<tenatid>`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://contoso.com/<tenatid>/SAML/SSO/HTTP-Post`.    
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Вы можете просмотреть эти значения позже в разделе **URL-адреса метаданных поставщика службы** или обратиться в [службу поддержки клиентов MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support) для получения этих значений.

1. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/moveittransfer-tutorial/tutorial_moveittransfer_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/moveittransfer-tutorial/tutorial_general_400.png)
    
1. Войдите в клиент MOVEit Transfer с правами администратора.

1. В левой панели навигации щелкните **Settings**(Параметры).

    ![Раздел Settings (Параметры) на стороне приложения](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

1. Щелкните ссылку **Single Signon** (Единый вход), которая расположена в разделе **Security Policies -> User Auth** (Политики безопасности -> Проверка подлинности пользователя).

    ![Политики безопасности на стороне приложения](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

1. Щелкните ссылку с URL-адресом метаданных, чтобы скачать документ метаданных.

    ![URL-адрес метаданных поставщика службы](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
    * В разделе **Домены и URL-адреса приложения MOVEit Transfer - Azure AD integration** убедитесь, что значения **entityID** (Идентификатор сущности) и **Идентификатор** совпадают.
    * В разделе **Домены и URL-адреса приложения MOVEit Transfer - Azure AD integration** убедитесь, что URL-адрес расположения **AssertionConsumerService** совпадает с **URL-адрес ответа**.
    
    ![Настройка единого входа на стороне приложения](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

1. Щелкните кнопку **Add Identity Provider** (Добавить поставщик удостоверений), чтобы добавить новый федеративный поставщик удостоверений.

    ![Добавление поставщика удостоверений](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

1. Щелкните **Browse...** (Обзор...), чтобы выбрать файл метаданных, который вы скачали на портале Azure, затем нажмите кнопку **Add Identity Provider** (Добавить поставщик удостоверений) и отправьте скачанный файл.

    ![Поставщик удостоверений SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

1. Выберите значение **Yes** (Да) для параметра **Enabled** (Включено) в разделе **Edit Federated Identity Provider Settings...** (Изменение параметров федеративного поставщика удостоверений) и нажмите кнопку **Save** (Сохранить).

    ![Параметры федеративного поставщика удостоверений](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

1. На странице **Edit Federated Identity Provider User Settings** (Изменение параметров федеративного поставщика удостоверений) выполните следующие действия:
    
    ![Изменение параметров федеративного поставщика удостоверений](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Выберите значение **SAML NameID** для параметра **Login name** (Имя для входа).
    
    b. Выберите **Other** (Другое) в качестве **полного имени** и в тестовом поле **Attribute name** (Имя атрибута) введите значение: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Выберите **Other** (Другое) в качестве **электронной почты** и в тестовом поле **Attribute name** (Имя атрибута) введите значение: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Выберите значение **Yes** (Да) для параметра **Auto-create account on signon** (Автоматическое создание учетной записи при первом входе).
    
    д. Нажмите кнопку **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/moveittransfer-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/moveittransfer-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/moveittransfer-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/moveittransfer-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-moveit-transfer---azure-ad-integration-test-user"></a>Создание тестового пользователя MOVEit Transfer - Azure AD integration

Цель этого раздела — создать пользователя с именем Britta Simon в MOVEit Transfer - Azure AD integration. Приложение MOVEit Transfer - Azure AD integration поддерживает JIT-подготовку, и вы уже включили ее. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к MOVEit Transfer - Azure AD integration (если он еще не создан).

>[!NOTE]
>Если вам нужно вручную создать пользователя, необходимо обратиться в [службу поддержки клиентов MOVEit Transfer - Azure AD integration](https://community.ipswitch.com/s/support).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к MOVEit Transfer - Azure AD integration.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в MOVEit Transfer - Azure AD integration, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **MOVEit Transfer - Azure AD integration**.

    ![Ссылка на MOVEit Transfer - Azure AD integration в списке "Приложения"](./media/moveittransfer-tutorial/tutorial_moveittransfer_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

Цель этого раздела — проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку MOVEit Transfer - Azure AD integration на панели доступа, вы автоматически войдете в приложение MOVEit Transfer - Azure AD integration. 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/moveittransfer-tutorial/tutorial_general_01.png
[2]: ./media/moveittransfer-tutorial/tutorial_general_02.png
[3]: ./media/moveittransfer-tutorial/tutorial_general_03.png
[4]: ./media/moveittransfer-tutorial/tutorial_general_04.png

[100]: ./media/moveittransfer-tutorial/tutorial_general_100.png

[200]: ./media/moveittransfer-tutorial/tutorial_general_200.png
[201]: ./media/moveittransfer-tutorial/tutorial_general_201.png
[202]: ./media/moveittransfer-tutorial/tutorial_general_202.png
[203]: ./media/moveittransfer-tutorial/tutorial_general_203.png

