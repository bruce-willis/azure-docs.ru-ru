---
title: Руководство по интеграции Azure Active Directory с Land Gorilla Client | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Land Gorilla.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.openlocfilehash: 6712ecbc81abdf4756cb72f4949f51d2ffca4142
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39435851"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Руководство по интеграции Azure Active Directory с Land Gorilla Client

В этом руководстве описано, как интегрировать приложение Land Gorilla Client с Azure Active Directory (Azure AD).

Интеграция Azure AD с Land Gorilla Client обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Land Gorilla Client.
- Вы можете включить автоматический вход пользователей в Land Gorilla Client (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — через портал управления Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Land Gorilla Client, вам потребуется:

- подписка Azure AD;
- подписка Land Gorilla Client с поддержкой единого входа.


> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.


При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не следует использовать рабочую среду при отсутствии необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Land Gorilla Client из коллекции
1. настройка и проверка единого входа в Azure AD.


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Добавление Land Gorilla Client из коллекции
Чтобы настроить интеграцию Land Gorilla Client с Azure AD, необходимо добавить Land Gorilla Client из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Land Gorilla Client из коллекции, выполните следующие действия:**

1. На **[портале управления Azure](https://portal.azure.com)** в левой области навигации нажмите значок **Azure Active Directory**. 

    ![Active Directory][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
1. Нажмите кнопку **Добавить** в верхней части диалогового окна.

    ![ПРИЛОЖЕНИЯ][3]

1. В поле поиска введите **Land Gorilla Client**.

    ![Создание тестового пользователя Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. На панели результатов выберите **Land Gorilla Client** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение Land Gorilla Client с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Land Gorilla Client соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Land Gorilla Client.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Land Gorilla Client.

Чтобы настроить и проверить единый вход Azure AD в Land Gorilla Client, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD с ограниченной группой.
1. **[Создание тестового пользователя Land Gorilla](#creating-a-land-gorilla-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
1. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале управления Azure и настроить его в приложении Land Gorilla Client.

**Чтобы настроить единый вход Azure AD в Land Gorilla Client, выполните следующие действия:**

1. На портале управления Azure на странице интеграции с приложением **Land Gorilla Client** щелкните **Единый вход**.

    ![Настройка единого входа][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. В разделе **Домены и URL-адреса приложения Land Gorilla Client** выполните следующие действия:

    ![Настройка единого входа](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. В текстовом поле **Идентификатор** введите значение, используя один из следующих форматов: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. В текстовое поле **URL-адрес ответа** введите URL-адрес, используя один из следующих форматов:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Обратите внимание, что значения, указанные выше, используются в качестве примера. Необходимо указать фактические значения идентификатора и URL-адреса ответа. Мы рекомендуем использовать уникальное значение строки идентификатора. Чтобы получить эти значения, обратитесь в [службу поддержки Land Gorilla Client](https://www.landgorilla.com/support/). 

1. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Чтобы настроить единый вход для своего приложения на стороне Land Gorilla, обратитесь в [службу поддержки Land Gorilla Client](https://www.landgorilla.com/support/) и предоставьте скачанный **XML-файл метаданных**.


### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале управления Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале управления Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**, чтобы отобразить список пользователей.
    
    ![Создание тестового пользователя Azure AD](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**. 

### <a name="creating-a-land-gorilla-test-user"></a>Создание тестового пользователя Land Gorilla

Обратитесь в [службу поддержки Land Gorilla](https://www.landgorilla.com/support/), чтобы добавить пользователей на платформу Land Gorilla.
    
### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure путем предоставления доступа к Land Gorilla Client.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Land Gorilla Client, выполните следующие действия:**

1. На портале управления Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **Land Gorilla Client**.

    ![Настройка единого входа](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    


### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Land Gorilla Client на панели доступа, вы автоматически войдете в приложение Land Gorilla Client.


## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
