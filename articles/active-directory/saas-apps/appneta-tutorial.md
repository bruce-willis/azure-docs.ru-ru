---
title: Руководство. Интеграция Azure Active Directory с AppNeta Performance Monitor | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в AppNeta Performance Monitor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: ccedc0288e313df2639862a14078d8cad9951286
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054555"
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Руководство. Интеграция Azure Active Directory с AppNeta Performance Monitor

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с AppNeta Performance Monitor.

Интеграция AppNeta Performance Monitor с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к AppNeta Performance Monitor.
- Вы можете включить автоматический вход пользователей в AppNeta Performance Monitor (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с AppNeta Performance Monitor, вам потребуется:

- подписка Azure AD;
- подписка AppNeta Performance Monitor с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление AppNeta Performance Monitor из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Добавление AppNeta Performance Monitor из коллекции.
Чтобы настроить интеграцию AppNeta Performance Monitor с Azure AD, необходимо добавить AppNeta Performance Monitor из коллекции в список управляемых приложений SaaS.

**Чтобы добавить AppNeta Performance Monitor из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **AppNeta Performance Monitor**, выберите **AppNeta Performance Monitor** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![AppNeta Performance Monitor в списке результатов](./media/appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в AppNeta Performance Monitor с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в AppNeta Performance Monitor соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в AppNeta Performance Monitor.

Чтобы настроить и проверить единый вход в Azure AD с AppNeta Performance Monitor, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя AppNeta Performance Monitor](#create-an-appneta-performance-monitor-test-user)** нужно для того, чтобы в AppNeta Performance Monitor существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в AppNeta Performance Monitor.

**Чтобы настроить единый вход Azure AD в AppNeta Performance Monitor, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **AppNeta Performance Monitor** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/appneta-tutorial/tutorial_appneta_samlbase.png)

3. В разделе **Домены и URL-адреса приложения AppNeta Performance Monitor** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для AppNeta Performance Monitor](./media/appneta-tutorial/tutorial_appneta_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.pm.appneta.com`

    b. В текстовом поле **Идентификатор** введите значение `PingConnect`.

    > [!NOTE] 
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, свяжитесь со [службой поддержки клиентов AppNeta Performance Monitor](mailto:support@appneta.com). 

5. Приложение AppNeta Performance Monitor ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения.

    ![Настройка единого входа](./media/appneta-tutorial/attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия.
           
    | Имя атрибута | Значение атрибута |
    | ---------------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | email| user.userprincipalname|
    | name| user.userprincipalname|
    | groups   | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user.jobtitle|

    > [!NOTE]
    > groups — это группа безопасности в AppNeta, которая сопоставлена с Role в Azure AD. Подробные сведения о создании пользовательских ролей в Azure AD см. в [этом](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) документе.
        
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/appneta-tutorial/tutorial_attribute_04.png)
    
    ![Настройка единого входа](./media/appneta-tutorial/tutorial_attribute_05.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пространство имен пустым.
    
    д. Нажмите кнопку **ОК**.  

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/appneta-tutorial/tutorial_appneta_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/appneta-tutorial/tutorial_general_400.png)

6. Для настройки единого входа на стороне **AppNeta Performance Monitor** необходимо отправить загруженный **XML-файл метаданных** в [службу поддержки AppNeta Performance Monitor](mailto:support@appneta.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/appneta-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/appneta-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/appneta-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/appneta-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Создание тестового пользователя AppNeta Performance Monitor

Цель этого раздела — создать в приложении AppNeta Performance Monitor пользователя с именем Britta Simon. Приложение AppNeta Performance Monitor поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к AppNeta Performance Monitor (если он еще не создан).
>[!Note]
>Если необходимо вручную создать пользователя, обратитесь в [службу поддержки клиентов AppNeta Performance Monitor](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к AppNeta Performance Monitor.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в AppNeta Performance Monitor, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **AppNeta Performance Monitor**.

    ![Ссылка AppNeta Performance Monitor в списке приложений](./media/appneta-tutorial/tutorial_appneta_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку AppNeta Performance Monitor на панели доступа, вы автоматически войдете в приложение AppNeta Performance Monitor.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appneta-tutorial/tutorial_general_01.png
[2]: ./media/appneta-tutorial/tutorial_general_02.png
[3]: ./media/appneta-tutorial/tutorial_general_03.png
[4]: ./media/appneta-tutorial/tutorial_general_04.png

[100]: ./media/appneta-tutorial/tutorial_general_100.png

[200]: ./media/appneta-tutorial/tutorial_general_200.png
[201]: ./media/appneta-tutorial/tutorial_general_201.png
[202]: ./media/appneta-tutorial/tutorial_general_202.png
[203]: ./media/appneta-tutorial/tutorial_general_203.png

