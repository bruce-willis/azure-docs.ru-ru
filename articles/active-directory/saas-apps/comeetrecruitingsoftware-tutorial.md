---
title: Руководство по интеграции Azure Active Directory с Comeet Recruiting Software | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Comeet Recruiting Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 75f51dc9-9525-4ec6-80bf-28374f0c8adf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2018
ms.author: jeedes
ms.openlocfilehash: 137ba7a7e82ff3e57d862868859e8049838701a3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43310463"
---
# <a name="tutorial-azure-active-directory-integration-with-comeet-recruiting-software"></a>Руководство по интеграции Azure Active Directory с Comeet Recruiting Software

В этом руководстве описано, как интегрировать Comeet Recruiting Software с Azure Active Directory (Azure AD).

Интеграция Comeet Recruiting Software с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD можно контролировать доступ к Comeet Recruiting Software.
- Вы можете включить автоматический вход пользователей в Comeet Recruiting Software (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Comeet Recruiting Software, вам потребуется следующее:

- подписка Azure AD;
- подписка Comeet Recruiting Software с поддержкой единого входа.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Comeet Recruiting Software из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-comeet-recruiting-software-from-the-gallery"></a>Добавление Comeet Recruiting Software из коллекции

Чтобы настроить интеграцию Comeet Recruiting Software с Azure AD, вам потребуется добавить Comeet Recruiting Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Comeet Recruiting Software из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Comeet Recruiting Software**, выберите **Comeet Recruiting Software** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Comeet Recruiting Software в списке результатов](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Comeet Recruiting Software с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Comeet Recruiting Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Comeet Recruiting Software.

Чтобы настроить и проверить единый вход в Azure AD в Comeet Recruiting Software, вам потребуется выполнить действия в следующих блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Comeet Recruiting Software](#create-a-comeet-recruiting-software-test-user)** требуется для создания пользователя Britta Simon в Comeet Recruiting Software, связанного с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Comeet Recruiting Software.

**Чтобы настроить единый вход Azure AD в Comeet Recruiting Software, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Comeet Recruiting Software** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_samlbase.png)

3. Если вы хотите настроить приложение в режиме, инициируемом **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Comeet Recruiting Software** сделайте следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения Comeet Recruiting Software](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url1.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://app.comeet.co/adfs_auth/acs/<UNIQUEID>/`.

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора и URL-адреса ответа. Эти значения можно получить на портале Comeet Recruiting Software, как показано на [странице службы поддержки](https://support.comeet.co/knowledgebase/adfs-single-sign-on/).

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Comeet Recruiting Software](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_url2.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес: `https://app.comeet.co`

5. Приложение Comeet Recruiting Software ожидает проверочные утверждения SAML в определенном формате, который требует добавить настраиваемые сопоставления атрибутов в вашу конфигурацию атрибутов токена SAML. На следующем снимке экрана приведен пример. По умолчанию **идентификатор пользователя** имеет значение **user.userprincipalname**, но для **Comeet Recruiting Software** требуется сопоставить это значение с адресом электронной почты пользователя. Для этого можно использовать атрибут **user.mail** из списка или соответствующее значение атрибута, основанное на конфигурации организации.

    ![Настройка единого входа](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_attribute.png)

6. В разделе **Атрибуты пользователя** установите флажок **Просмотреть и изменить все другие атрибуты пользователей**, чтобы развернуть атрибуты. Выполните следующие действия для каждого отображаемого атрибута.

    | Имя атрибута | Значение атрибута |
    | ---------------| --------------- |
    | comeet_id | user.userprincipalname |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/comeetrecruitingsoftware-tutorial/tutorial_attribute_05.png)

    b. В текстовом поле **Имя** введите **имя атрибута**, отображаемое для этой записи.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Нажмите кнопку **ОК**.

7. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_certificate.png)

8. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/comeetrecruitingsoftware-tutorial/tutorial_general_400.png)

9. Чтобы настроить единый вход на стороне **Comeet Recruiting Software**, вставьте содержимое скачанного XML-файла метаданных в Comeet Recruiting Software, как показано на [странице службы поддержки](https://support.comeet.co/knowledgebase/adfs-single-sign-on/).

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/comeetrecruitingsoftware-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/comeetrecruitingsoftware-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/comeetrecruitingsoftware-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/comeetrecruitingsoftware-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-comeet-recruiting-software-test-user"></a>Создание тестового пользователя Comeet Recruiting Software

В этом разделе описано, как создать пользователя Britta Simon в приложении Comeet Recruiting Software. Обратитесь к [группе поддержки Comeet Recruiting Software](mailto:support@comeet.co), чтобы добавить пользователей на платформу Comeet Recruiting Software. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon, предоставив этому пользователю доступ к Comeet Recruiting Software.

![Назначение роли пользователя][200]

**Чтобы назначить Britta Simon в Comeet Recruiting Software, сделайте следующее.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. Из списка приложений выберите **Comeet Recruiting Software**.

    ![Ссылка на Comeet Recruiting Software в списке "Приложения"](./media/comeetrecruitingsoftware-tutorial/tutorial_comeetrecruitingsoftware_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Comeet Recruiting Software" на панели доступа, вы автоматически войдете в приложение Comeet Recruiting Software.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_01.png
[2]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_02.png
[3]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_03.png
[4]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_04.png

[100]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_100.png

[200]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_200.png
[201]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_201.png
[202]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_202.png
[203]: ./media/comeetrecruitingsoftware-tutorial/tutorial_general_203.png