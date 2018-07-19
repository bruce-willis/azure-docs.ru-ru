---
title: Руководство по интеграции Azure Active Directory с SecureW2 JoinNow Connector | Документы Майкрософт
description: Сведения о том, как настроить единый вход Azure Active Directory в SecureW2 JoinNow Connector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.openlocfilehash: 283f8c935556006a21812578d0638b72adb6eed0
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37906320"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>Руководство по интеграции Azure Active Directory с SecureW2 JoinNow Connector

В этом руководстве описано, как интегрировать SecureW2 JoinNow Connector с Azure Active Directory (Azure AD).

Интеграция SecureW2 JoinNow Connector с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к SecureW2 JoinNow Connector.
- Вы можете включить автоматический вход пользователей в SecureW2 JoinNow Connector (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с SecureW2 JoinNow Connector, вам потребуется:

- подписка Azure AD;
- подписка с поддержкой единого входа SecureW2 JoinNow Connector.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление SecureW2 JoinNow Connector из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Добавление SecureW2 JoinNow Connector из коллекции
Чтобы настроить интеграцию SecureW2 JoinNow Connector в Azure AD, нужно добавить SecureW2 JoinNow Connector из коллекции в список управляемых приложений SaaS.

**Чтобы добавить SecureW2 JoinNow Connector из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SecureW2 JoinNow Connector**, выберите **SecureW2 JoinNow Connector** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![SecureW2 JoinNow Connector в списке результатов](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в SecureW2 JoinNow Connector с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD нужно знать, какой пользователь в SecureW2 JoinNow Connector соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в SecureW2 JoinNow Connector.

Чтобы настроить и проверить единый вход Azure AD в SecureW2 JoinNow Connector, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя SecureW2 JoinNow Connector](#create-a-securew2-joinnow-connector-test-user)** требуется для того, чтобы в SecureW2 JoinNow Connector существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении SecureW2 JoinNow Connector.

**Чтобы настроить единый вход Azure AD в SecureW2 JoinNow Connector, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **SecureW2 JoinNow Connector** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. В разделе **Домены и URL-адреса приложения SecureW2 JoinNow Connector** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа для приложения SecureW2 JoinNow Connector](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов SecureW2 JoinNow Connector](mailto:support@securew2.com). 

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. Чтобы настроить единый вход на стороне **SecureW2 JoinNow Connector**, отправьте в [службу поддержки SecureW2 JoinNow Connector](mailto:support@securew2.com) скачанный **XML-файл метаданных**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>Создание тестового пользователя SecureW2 JoinNow Connector

В этом разделе описано, как создать пользователя Britta Simon в приложении SecureW2 JoinNow Connector. Обратитесь в [службу поддержки клиентов SecureW2 JoinNow Connector](mailto:support@securew2.com), чтобы добавить пользователей на платформу SecureW2 JoinNow Connector. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к SecureW2 JoinNow Connector.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению SecureW2 JoinNow Connector, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **SecureW2 JoinNow Connector**.

    ![Ссылка на SecureW2 JoinNow Connector в списке "Приложения"](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

**Чтобы протестировать приложение, сделайте следующее:** 

a. Откройте клиент SecureW2 JoinNow Connector, выберите соответствующее устройство в списке и нажмите кнопку **Вход**.

Б. Открывается браузер по умолчанию, и выполняется переход на портал Azure для проверки подлинности.

c. После прохождения проверки должен быть выполнен возврат на начальную целевую страницу SecureW2 JoinNow Connector.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png

