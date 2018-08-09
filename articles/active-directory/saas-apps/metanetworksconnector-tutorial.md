---
title: Руководство по интеграции Azure Active Directory с Meta Networks Connector | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Meta Networks Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.openlocfilehash: 8e27ba7d5b245d8857f0c07bfe2923afe9d7e3a0
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39267535"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Руководство по интеграции Azure Active Directory с Meta Networks Connector

В этом руководстве описано, как интегрировать Meta Networks Connector с Azure Active Directory (Azure AD).

Интеграция Meta Networks Connector с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Meta Networks Connector.
- Вы можете включить автоматический вход пользователей в Meta Networks Connector (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Meta Networks Connector, вам потребуется:

- подписка Azure AD;
- подписка Meta Networks Connector с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Meta Networks Connector из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Добавление Meta Networks Connector из коллекции
Чтобы настроить интеграцию Meta Networks Connector с Azure AD, нужно добавить Meta Networks Connector из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Meta Networks Connector из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Meta Networks Connector**, выберите **Meta Networks Connector** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Meta Networks Connector в списке результатов](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Meta Networks Connector с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в Meta Networks Connector соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Meta Networks Connector.

Чтобы настроить и проверить единый вход Azure AD в Meta Networks Connector, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Meta Networks Connector](#create-a-meta-networks-connector-test-user)** требуется для того, чтобы в Meta Networks Connector существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Meta Networks Connector.

**Чтобы настроить единый вход Azure AD в Meta Networks Connector, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Meta Networks Connector** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Meta Networks Connector** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения Meta Networks Connector](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`.

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Meta Networks Connector](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. В текстовом поле **Состояние ретранслятора** введите URL-адрес в формате `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа, которые описываются далее в этом руководстве.

5. Приложение Meta Networks Connector ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)

6. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:

    | Имя атрибута | Значение атрибута | NAMESPACE|
    | ---------------| --------------- | -------- |
    | firstname | user.givenname | |
    | lastname | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. В текстовом поле **Пространство имен** введите значение пространства имен, показанное для этой строки.

    д. Нажмите кнопку **ОК**.

7. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)

8. В разделе **Конфигурация Meta Networks Connector** щелкните **Настроить Meta Networks Connector**, чтобы открыть окно **Настройка единого входа**. Скопируйте **идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)

9. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/metanetworksconnector-tutorial/tutorial_general_400.png)

10. Откройте новую вкладку в браузере и войдите в учетную запись администратора Meta Networks Connector.

    > [!NOTE]
    > Meta Networks Connector является безопасной системой. Поэтому перед переходом на портал этого приложения необходимо, чтобы ваш общедоступный IP-адрес был добавлен в список разрешений на его стороне. Чтобы получить свой общедоступный IP-адрес, перейдите по ссылке, указанной [здесь](https://whatismyipaddress.com/). Отправьте свой IP-адрес [группе поддержки клиентов Meta Networks Connector](mailto:support@metanetworks.com), чтобы он был добавлен в список разрешений.

11. Выберите **Administrator** (Администратор) и щелкните **Settings** (Параметры).

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure3.png)

12. Убедитесь, что параметры **Log Internet Traffic** (Ведение журнала трафика Интернета) и **Force VPN MFA** (Принудительная многофакторная проверка подлинности через VPN) отключены.

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure1.png)

13. Выберите **Administrator** (Администратор) и щелкните **SAML**.

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure4.png)

14. Выполните следующие действия на странице **DETAILS** (Сведения).

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure2.png)

    a. Скопируйте значение **SSO URL** (URL-адрес единого входа) и вставьте его в текстовое поле **URL-адрес входа** в разделе **Домены и URL-адреса приложения Meta Networks Connector**.

    b. Скопируйте значение **Recipient URL** (URL-адрес получателя) и вставьте его в текстовое поле **URL-адрес ответа** в разделе **Домены и URL-адреса приложения Meta Networks Connector**.

    c. Скопируйте значение **Audience URI (SP Entity ID)** (URI аудитории (Идентификатор сущности поставщика услуг)) и вставьте его в текстовое поле **Идентификатор (сущности)** в разделе **Домены и URL-адреса приложения Meta Networks Connector**.

    d. Включение SAML

15. На вкладке **GENERAL** (Общие) выполните следующее.

    ![Настройка единого входа](./media/metanetworksconnector-tutorial/configure5.png)

    a. В текстовое поле **URL-адреса единого входа для поставщика удостоверений** вставьте значение **SAML Single Sign-On Service URL** (URL-адрес службы единого входа SAML), скопированное на портале Azure.

    b. В текстовое поле **Издатель поставщика удостоверений** вставьте значение **SAML Entity ID** (Идентификатор сущности SAML), скопированное на портале Azure.

    c. Откройте в Блокноте сертификат, скачанный с портала Azure, и вставьте его в текстовое поле **Сертификат X.509**.

    d. Включите **JIT-подготовку**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/metanetworksconnector-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/metanetworksconnector-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/metanetworksconnector-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/metanetworksconnector-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-meta-networks-connector-test-user"></a>Создание тестового пользователя Meta Networks Connector

Цель этого раздела — создать пользователя с именем Britta Simon в Meta Networks Connector. Приложение Meta Networks Connector поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Meta Networks Connector (если он еще не создан).

>[!Note]
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки клиентов Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Meta Networks Connector.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon приложению Meta Networks Connector, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **Meta Networks Connector**.

    ![Ссылка на Meta Networks Connector в списке "Приложения"](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Meta Networks Connector" на панели доступа, вы автоматически войдете в приложение Meta Networks Connector.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png

