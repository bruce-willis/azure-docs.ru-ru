---
title: Руководство. Интеграция Azure Active Directory с Comm100 Live Chat | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2018
ms.author: jeedes
ms.openlocfilehash: b85162c8392e8ecdb08a3ed04ff5b9de835808a1
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42145165"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Руководство. Интеграция Azure Active Directory с Comm100 Live Chat

В этом руководстве описано, как интегрировать Comm100 Live Chat с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Comm100 Live Chat обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Comm100 Live Chat.
- Вы можете включить автоматический вход пользователей в Comm100 Live Chat (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Comm100 Live Chat, вам потребуется:

- подписка Azure AD;
- подписка Comm100 Live Chat с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Comm100 Live Chat из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Добавление Comm100 Live Chat из коллекции
Чтобы настроить интеграцию Comm100 Live Chat с Azure AD, необходимо добавить Comm100 Live Chat из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Comm100 Live Chat из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Comm100 Live Chat**, выберите **Comm100 Live Chat** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Comm100 Live Chat в списке результатов](./media/comm100livechat-tutorial/tutorial_comm100livechat_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Comm100 Live Chat с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Comm100 Live Chat соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Comm100 Live Chat.

Чтобы настроить и проверить единый вход Azure AD в Comm100 Live Chat, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Comm100 Live Chat](#create-a-comm100-live-chat-test-user)** требуется для того, чтобы в Comm100 Live Chat существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Comm100 Live Chat.

**Чтобы настроить единый вход Azure AD в Comm100 Live Chat, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Comm100 Live Chat** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/comm100livechat-tutorial/tutorial_comm100livechat_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Comm100 Live Chat** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа для приложения Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`
    
    > [!NOTE] 
    > Значение URL-адреса входа приведено для примера. Вы замените это значение на фактический URL-адрес для входа, который описывается далее в этом руководстве.

4. Приложение Comm100 Live Chat ожидает утверждений SAML, которые содержат определенные атрибуты. Настройте приведенные ниже атрибуты для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/comm100livechat-tutorial/tutorial_attribute_03.png)
    
5. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    |  Имя атрибута  | Значение атрибута |
    | --------------- | -------------------- |    
    |   email    | user.mail |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/comm100livechat-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/comm100livechat-tutorial/tutorial_attribute_05.png)
    
    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.

6. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/comm100livechat-tutorial/tutorial_comm100livechat_certificate.png) 

7. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/comm100livechat-tutorial/tutorial_general_400.png)

8. В разделе **Настройка Comm100 Live Chat** щелкните **Настроить Comm100 Live Chat**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_configure.png)

9. В другом окне браузера войдите в приложение Comm100 Live Chat с правами администратора безопасности.

10. В правом верхнем углу страницы щелкните **My Account** (Моя учетная запись).

    ![Моя учетная запись Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. В меню в левой части щелкните **Security** (Безопасность), затем выберите **Agent Single Sign-On** (Единый вход агента).

    ![Безопасность Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. На странице **Agent Single Sign-On** (Единый вход агента) выполните следующие действия:

    ![Безопасность Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Скопируйте первую выделенную ссылку и вставьте ее в текстовое поле **URL-адрес входа** в разделе **Домены и URL-адреса приложения Comm100 Live Chat** на портале Azure.

    b. В текстовое поле **SAML SSO URL** (URL-адрес единого входа SAML) вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.

    c. В текстовое поле **Remote Logout URL** (URL-адрес удаленного выхода) вставьте значение **URL-адреса выхода**, скопированное на портале Azure.

    d. В поле **Certificate** (Сертификат) щелкните **Choose a File** (Выбрать файл), чтобы отправить сертификат в кодировке Base64, который был скачан с портала Azure.

    д. Нажмите кнопку **Save Changes** (Сохранить изменения).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/comm100livechat-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/comm100livechat-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/comm100livechat-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/comm100livechat-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-comm100-live-chat-test-user"></a>Создание тестового пользователя Comm100 Live Chat

Чтобы пользователи Azure AD могли входить в Comm100 Live Chat, их необходимо подготовить в Comm100 Live Chat. Подготовка в Live Chat Comm100 выполняется вручную.

**Чтобы подготовить учетную запись пользователя, сделайте следующее:**

1. Войдите в Comm100 Live Chat с правами администратора безопасности.

2. В правом верхнем углу страницы щелкните **My Account** (Моя учетная запись).

    ![Моя учетная запись Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. В меню в левой части щелкните **Agents** (Агенты), затем выберите **New Agent** (Новый агент).

    ![Агенты Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. На странице **New Agent** (Новый агент) выполните следующие действия:

    ![Новый агент Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. В текстовое поле **Email** (Адрес электронной почты) введите адрес электронной почты пользователя, например **Brittasimon@contoso.com**.

    b. В текстовое поле **First Name** (Имя) введите имя пользователя, например **Britta**.

    c. В текстовое поле **Last Name** (Фамилия) введите фамилию пользователя, например **Simon**.

    d. В текстовое поле **Display Name** (Отображаемое имя) введите отображаемое имя пользователя, например **Britta Simon**.

    д. В текстовое поле **Password** (Пароль) введите пароль.

    Е. Выберите команду **Сохранить**.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Comm100 Live Chat, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Comm100 Live Chat, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Comm100 Live Chat**.

    ![Ссылка на Comm100 Live Chat в списке приложений](./media/comm100livechat-tutorial/tutorial_comm100livechat_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Comm100 Live Chat на панели доступа, вы автоматически войдете в приложение Comm100 Live Chat.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/comm100livechat-tutorial/tutorial_general_01.png
[2]: ./media/comm100livechat-tutorial/tutorial_general_02.png
[3]: ./media/comm100livechat-tutorial/tutorial_general_03.png
[4]: ./media/comm100livechat-tutorial/tutorial_general_04.png

[100]: ./media/comm100livechat-tutorial/tutorial_general_100.png

[200]: ./media/comm100livechat-tutorial/tutorial_general_200.png
[201]: ./media/comm100livechat-tutorial/tutorial_general_201.png
[202]: ./media/comm100livechat-tutorial/tutorial_general_202.png
[203]: ./media/comm100livechat-tutorial/tutorial_general_203.png

