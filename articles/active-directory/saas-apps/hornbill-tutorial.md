---
title: Руководство по интеграции Azure Active Directory с Hornbill | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Hornbill.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 173061e4-ac1d-458f-bb9b-e9a2493aab0e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: jeedes
ms.openlocfilehash: 30fdb55758d5fbac41452236ebaa9f96ab9bba6b
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163771"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>Руководство по интеграции Azure Active Directory с Hornbill

Цель этого руководства — показать, как интегрировать Azure Active Directory (Azure AD) с приложением Hornbill.

Интеграция Azure AD с приложением Hornbill обеспечивает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к Hornbill.
- Вы можете включить автоматический вход пользователей в Hornbill (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Hornbill, вам потребуются:

- подписка Azure AD;
- подписка с поддержкой единого входа Hornbill.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Hornbill из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-hornbill-from-the-gallery"></a>Добавление Hornbill из коллекции
Чтобы настроить интеграцию Hornbill с Azure AD, необходимо добавить Hornbill из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Hornbill из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Hornbill**, выберите **Hornbill** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Hornbill в списке результатов](./media/hornbill-tutorial/tutorial_hornbill_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Hornbill с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Hornbill соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Hornbill.

Чтобы настроить и проверить единый вход Azure AD в Hornbill, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя приложения Hornbill](#create-a-hornbill-test-user)** требуется для того, чтобы в Hornbill существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Hornbill.

**Чтобы настроить единый вход Azure AD в Hornbill, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Hornbill** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/hornbill-tutorial/tutorial_hornbill_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Hornbill** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Hornbill](./media/hornbill-tutorial/tutorial_hornbill_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить их, обратитесь к [группе поддержки клиентов Hornbill](https://www.hornbill.com/support/?request/). 

4. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/hornbill-tutorial/tutorial_hornbill_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/hornbill-tutorial/tutorial_general_400.png)
 
6. В другом окне веб-браузера войдите в Hornbill в качестве администратора безопасности.

7. На домашней странице щелкните **System** (Система).

    ![Hornbill: система](./media/hornbill-tutorial/tutorial_hornbill_system.png)

8. Перейдите в раздел **Security** (Безопасность).

    ![Hornbill: безопасность](./media/hornbill-tutorial/tutorial_hornbill_security.png)

9. Щелкните **SSO Profiles** (Профили единого входа).

    ![Hornbill: профили единого входа](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

10. В правой части страницы щелкните **Add logo** (Добавить логотип).

    ![Hornbill: добавление логотипа](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

11. На панели **Profile Details** (Сведения о профиле) щелкните **Import SAML Meta logo** (Импорт логотипа метаданных SAML).

    ![Hornbill: логотип](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

12. На открывшейся странице в текстовое поле **URL** (URL-адрес) вставьте значение **URL-адреса метаданных федерации приложения**, скопированное на портале Azure, а затем нажмите кнопку **Process** (Обработать).

    ![Hornbill: обработка](./media/hornbill-tutorial/tutorial_hornbill_process.png)

13. После нажатия кнопки "Process" (Обработать) значения в разделе **Profile Details** (Сведения о профиле) заполнятся автоматически.

    ![Hornbill: страница 1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Hornbill: страница 2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Hornbill: страница 3](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

14. Нажмите кнопку **Сохранить изменения**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/hornbill-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/hornbill-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/hornbill-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/hornbill-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-hornbill-test-user"></a>Создание тестового пользователя Hornbill

Цель этого раздела — создать пользователя с именем Britta Simon в Hornbill. Приложение Hornbill поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. При попытке получить доступ к приложению Hornbill создается учетная запись пользователя (если она еще не создана).

> [!Note]
> Чтобы создать пользователя вручную, обратитесь к [группе поддержки Hornbill](https://www.hornbill.com/support/?request/).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Hornbill.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Hornbill, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Hornbill**.

    ![Ссылка на Hornbill в списке "Приложения"](./media/hornbill-tutorial/tutorial_hornbill_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Hornbill" на панели доступа, вы автоматически войдете в приложение Hornbill.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hornbill-tutorial/tutorial_general_01.png
[2]: ./media/hornbill-tutorial/tutorial_general_02.png
[3]: ./media/hornbill-tutorial/tutorial_general_03.png
[4]: ./media/hornbill-tutorial/tutorial_general_04.png

[100]: ./media/hornbill-tutorial/tutorial_general_100.png

[200]: ./media/hornbill-tutorial/tutorial_general_200.png
[201]: ./media/hornbill-tutorial/tutorial_general_201.png
[202]: ./media/hornbill-tutorial/tutorial_general_202.png
[203]: ./media/hornbill-tutorial/tutorial_general_203.png

