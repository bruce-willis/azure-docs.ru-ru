---
title: Руководство по интеграции Azure Active Directory с Infogix Data3Sixty Govern | Документы Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Infogix Data3Sixty Govern.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 3e54ade44828bf1e26c310a14ae401fe8ae33229
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39437669"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Руководство по интеграции Azure Active Directory с Infogix Data3Sixty Govern

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с приложением Infogix Data3Sixty Govern.

Интеграция Infogix Data3Sixty Govern с Azure AD обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Infogix Data3Sixty Govern.
- Вы можете включить автоматический вход пользователей в Infogix Data3Sixty Govern (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Infogix Data3Sixty Govern, вам потребуется:

- подписка Azure AD;
- подписка Infogix Data3Sixty Govern с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Infogix Data3Sixty Govern из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Добавление Infogix Data3Sixty Govern из коллекции
Чтобы настроить интеграцию Infogix Data3Sixty Govern с Azure AD, необходимо добавить Infogix Data3Sixty Govern из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Infogix Data3Sixty Govern из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Infogix Data3Sixty Govern**, выберите **Infogix Data3Sixty Govern** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Infogix Data3Sixty Govern в списке результатов](./media/infogix-tutorial/tutorial_infogix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Из этого раздела вы узнаете, как настроить и проверить единый вход Azure AD в Infogix Data3Sixty Govern с использованием данных тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в Infogix Data3Sixty Govern соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Infogix Data3Sixty Govern.

Чтобы настроить и проверить единый вход Azure AD в Infogix Data3Sixty Govern, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя приложения Infogix Data3Sixty Govern](#create-an-infogix-data3sixty-govern-test-user)** требуется для создания в Infogix Data3Sixty Govern пользователя Britta Simon, связанного с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Infogix Data3Sixty Govern.

**Чтобы настроить единый вход Azure AD в Infogix Data3Sixty Govern, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Infogix Data3Sixty Govern** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/infogix-tutorial/tutorial_infogix_samlbase.png)

1. Если вы хотите настроить приложение в режиме, инициированном **поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Infogix Data3Sixty Govern** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения Infogix Data3Sixty Govern](./media/infogix-tutorial/tutorial_infogix_url.png)

    a. В текстовом поле **Идентификатор** введите URL-адрес: `https://data3sixty.com/ui`

    b. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<subdomain>.data3sixty.com/sso/acs`.

1. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Infogix Data3Sixty Govern](./media/infogix-tutorial/tutorial_infogix_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<subdomain>.data3sixty.com`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Измените их на фактические значения URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com).

1. Приложение Infogix Data3Sixty Govern ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка атрибута единого входа](./media/infogix-tutorial/tutorial_infogix_attribute.png)
    
1. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке, и выполните следующие действия.
    
    | Имя атрибута | Значение атрибута |
    | ------------------- | -------------------- |    
    | firstname           | user.givenname |
    | lastname        | user.surname |
    | Имя пользователя       | user.mail    |
    
    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Добавление атрибута для настройки единого входа](./media/infogix-tutorial/tutorial_attribute_04.png)

    ![Добавление атрибута для настройки единого входа](./media/infogix-tutorial/tutorial_attribute_05.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Оставьте пустым поле **Пространство имен**.
    
    д. Нажмите кнопку **ОК**.

1. В разделе **Сертификат подписи SAML** щелкните **Certificate (Raw)** (Сертификат (необработанный)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/infogix-tutorial/tutorial_infogix_certificate.png)

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/infogix-tutorial/tutorial_general_400.png)
    
1. В разделе **Настройка Infogix Data3Sixty Govern** щелкните **Настроить Infogix Data3Sixty Govern**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Infogix Data3Sixty Govern](./media/infogix-tutorial/tutorial_infogix_configure.png) 

1. Чтобы настроить единый вход на стороне **Infogix Data3Sixty Govern**, нужно отправить скачанный **сертификат (необработанный), URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** [службе поддержки Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/infogix-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/infogix-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/infogix-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/infogix-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-an-infogix-data3sixty-govern-test-user"></a>Создание тестового пользователя Infogix Data3Sixty Govern


Цель этого раздела — создать пользователя с именем Britta Simon в приложении Infogix Data3Sixty Govern. Приложение Infogix Data3Sixty Govern поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к приложению Infogix Data3Sixty Govern (если он еще не создан).

>[!Note]
>Чтобы создать пользователя вручную, обратитесь в [службу Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Infogix Data3Sixty Govern.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Infogix Data3Sixty Govern, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **Infogix Data3Sixty Govern**.

    ![Ссылка на Infogix Data3Sixty Govern в списке приложений](./media/infogix-tutorial/tutorial_infogix_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Infogix Data3Sixty Govern на панели доступа, вы автоматически войдете в приложение Infogix Data3Sixty Govern.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/infogix-tutorial/tutorial_general_01.png
[2]: ./media/infogix-tutorial/tutorial_general_02.png
[3]: ./media/infogix-tutorial/tutorial_general_03.png
[4]: ./media/infogix-tutorial/tutorial_general_04.png

[100]: ./media/infogix-tutorial/tutorial_general_100.png

[200]: ./media/infogix-tutorial/tutorial_general_200.png
[201]: ./media/infogix-tutorial/tutorial_general_201.png
[202]: ./media/infogix-tutorial/tutorial_general_202.png
[203]: ./media/infogix-tutorial/tutorial_general_203.png

