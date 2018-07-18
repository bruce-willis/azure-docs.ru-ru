---
title: Руководство по интеграции Azure Active Directory с Arc Publishing — SSO | Документы Майкрософт
description: Сведения о том, как настроить единый вход Azure Active Directory в Arc Publishing — SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ae609583-f875-4cb8-b68e-1b0b7938e9a7
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2018
ms.author: jeedes
ms.openlocfilehash: c1bf5ba68d8242a0ef0831987ac6fd041c62ace9
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969373"
---
# <a name="tutorial-azure-active-directory-integration-with-arc-publishing---sso"></a>Руководство по интеграции Azure Active Directory с Arc Publishing — SSO

В этом руководстве описано, как интегрировать Arc Publishing — SSO с Azure Active Directory (Azure AD).

Интеграция Arc Publishing — SSO с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Arc Publishing — SSO.
- Вы можете включить автоматический вход пользователей в Arc Publishing — SSO (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Arc Publishing — SSO, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа Arc Publishing — SSO.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Arc Publishing — SSO из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-arc-publishing---sso-from-the-gallery"></a>Добавление Arc Publishing — SSO из коллекции
Чтобы настроить интеграцию Arc Publishing — SSO с Azure AD, нужно добавить Arc Publishing — SSO из коллекции в список управляемых приложений SaaS.

**Чтобы добавить приложение Arc Publishing — SSO из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Arc Publishing — SSO**, выберите **Arc Publishing — SSO** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Arc Publishing — SSO в списке результатов](./media/arc-tutorial/tutorial_arc_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Arc Publishing — SSO с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD нужно знать, какой пользователь в Arc Publishing — SSO соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в Arc Publishing — SSO.

Чтобы настроить и проверить единый вход Azure AD в Arc Publishing — SSO, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Arc Publishing — SSO](#create-an-arc-publishing---sso-test-user)** требуется для того, чтобы в Arc Publishing — SSO существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В данном разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Arc Publishing — SSO.

**Чтобы настроить единый вход Azure AD в Arc Publishing — SSO, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **Arc Publishing — SSO** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/arc-tutorial/tutorial_arc_samlbase.png)

1. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения Arc Publishing — SSO** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для приложения Arc Publishing — SSO](./media/arc-tutorial/tutorial_arc_url.png)

    1. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://www.okta.com/saml2/service-provider/<Unique ID>`

    1. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`.

1. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения Arc Publishing — SSO](./media/arc-tutorial/tutorial_arc_url1.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://arcpublishing-<Customer>.okta.com/sso/saml2/<Unique ID>`
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Arc Publishing — SSO](mailto:inf@washpost.com). 

1. Приложение Arc Publishing — SSO ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка единого входа](./media/arc-tutorial/tutorial_arc_attribute.png)

1. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    | Имя атрибута | Значение атрибута |
    | ---------------| --------------- |    
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |
    | groups | user.assignedroles |

    1. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

     ![Настройка единого входа](./media/arc-tutorial/tutorial_attribute_04.png)

     ![Настройка единого входа](./media/arc-tutorial/tutorial_attribute_05.png)
    
    1. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    1. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    1. Оставьте пустым поле **Пространство имен**.
    
    1. Нажмите кнопку **ОК**.

    > [!NOTE]
    > Здесь атрибут **groups** сопоставляется с **user.assignedroles**. Это пользовательские роли, созданные в Azure AD для сопоставления с именами групп в приложении. [Здесь](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) приведены дополнительные рекомендации о создании настраиваемых ролей в Azure AD. 

1. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/arc-tutorial/tutorial_arc_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/arc-tutorial/tutorial_general_400.png)
    
1. В разделе **Конфигурация Arc Publishing — SSO** щелкните **Настроить Arc Publishing — SSO**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Конфигурация Arc Publishing — SSO](./media/arc-tutorial/tutorial_arc_configure.png) 

1. Чтобы настроить единый вход на стороне **Arc Publishing — SSO**, нужно отправить скачанный **сертификат (Base64), URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** [группе поддержки Arc Publishing — SSO](mailto:inf@washpost.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/arc-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/arc-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/arc-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/arc-tutorial/create_aaduser_04.png)

    1. В поле **Имя** введите **BrittaSimon**.

    1. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    1. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    1. Нажмите кнопку **Создать**.
 
### <a name="create-an-arc-publishing---sso-test-user"></a>Создание тестового пользователя приложения Arc Publishing — SSO

Цель этого раздела — создать пользователя с именем Britta Simon в приложении Arc Publishing — SSO. Приложение Arc Publishing — SSO поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к приложению Arc Publishing — SSO создается учетная запись пользователя (если она еще не создана).

>[!Note]
>Чтобы создать пользователя вручную, обратитесь к [группе поддержки Arc Publishing — SSO](mailto:inf@washpost.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к Arc Publishing — SSO, чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Arc Publishing — SSO, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **Arc Publishing — SSO**.

    ![Ссылка на Arc Publishing — SSO в списке приложений](./media/arc-tutorial/tutorial_arc_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Arc Publishing — SSO" на панели доступа, вы автоматически войдете в приложение Arc Publishing — SSO.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/arc-tutorial/tutorial_general_01.png
[2]: ./media/arc-tutorial/tutorial_general_02.png
[3]: ./media/arc-tutorial/tutorial_general_03.png
[4]: ./media/arc-tutorial/tutorial_general_04.png

[100]: ./media/arc-tutorial/tutorial_general_100.png

[200]: ./media/arc-tutorial/tutorial_general_200.png
[201]: ./media/arc-tutorial/tutorial_general_201.png
[202]: ./media/arc-tutorial/tutorial_general_202.png
[203]: ./media/arc-tutorial/tutorial_general_203.png

