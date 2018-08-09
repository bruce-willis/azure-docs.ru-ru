---
title: Учебник. Интеграция Azure Active Directory с Igloo Software | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Igloo Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: d49a08c6f57f5248f17539cd9d0467d132f7a63d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447413"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Учебник. Интеграция Azure Active Directory с Igloo Software

В этом руководстве описано, как интегрировать Igloo Software с Azure Active Directory (Azure AD).

Интеграция Igloo Software с Azure AD обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Igloo Software.
- Вы можете включить автоматический вход пользователей в Igloo Software (единый вход) под учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Igloo Software, вам потребуется следующее:

- подписка Azure AD;
- подписка Igloo Software с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Igloo Software из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-igloo-software-from-the-gallery"></a>Добавление Igloo Software из коллекции
Чтобы настроить интеграцию Igloo Software с Azure AD, вам потребуется добавить Igloo Software из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Igloo Software из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

1. В поле поиска введите **Igloo Software**.

    ![Создание тестового пользователя Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

1. В области результатов выберите **Igloo Software** и нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Создание тестового пользователя Azure AD](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Igloo Software для тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Igloo Software соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователям Azure AD и соответствующим пользователем в Igloo Software.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Igloo Software.

Чтобы настроить и проверить единый вход в Azure AD в Igloo Software, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Igloo Software](#creating-an-igloo-software-test-user)** требуется для создания дублирующего Britta Simon пользователя в Igloo Software, связанного с представлением этого пользователя в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
1. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Igloo Software.

**Чтобы настроить единый вход в Azure AD для Igloo Software, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Igloo Software** щелкните **Единый вход**.

    ![Настройка единого входа][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

1. В разделе **Домены и URL-адреса Igloo Software** выполните следующие действия:

    ![Настройка единого входа](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<company name>.igloocommmunities.com`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `https://<company name>.igloocommmunities.com/saml.digest`

    c. В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://<company name>.igloocommmunities.com/saml.digest`.

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Igloo Software](https://www.igloosoftware.com/services/support). 

1. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/igloo-software-tutorial/tutorial_general_400.png)
    
1. В разделе **Настройка Igloo Software** щелкните **Настроить Igloo Software**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

1. В другом окне веб-браузера войдите на веб-сайт Igloo Software организации в качестве администратора.

1. Перейдите в раздел **Панель управления**.
   
     ![Панель управления](./media/igloo-software-tutorial/ic799949.png "Панель управления")

1. На вкладке **Членство** щелкните **Параметры входа**.
   
    ![Параметры входа](./media/igloo-software-tutorial/ic783968.png "Параметры входа")

1. В разделе настройки SAML нажмите **Настройка проверки подлинности SAML**.
   
    ![Настройка SAML](./media/igloo-software-tutorial/ic783969.png "Настройка SAML")
   
1. В разделе **Общая конфигурация** выполните следующие действия.
   
    ![Общая конфигурация](./media/igloo-software-tutorial/ic783970.png "Общая конфигурация")

    a. Придумайте имя для конфигурации и введите его в текстовое поле **Имя подключения** .
   
    b. В текстовое поле **URL-адрес входа поставщика удостоверений** вставьте значение **URL-адреса службы единого входа SAML**, скопированное на портале Azure.
   
    c. В текстовое поле **URL-адрес выхода поставщика удостоверений** вставьте значение **URL-адреса выхода**, скопированное на портале Azure.
    
    d. Для параметра **Тип HTTP запроса и ответа о выходе** укажите значение **POST**.
   
    д. Откройте в блокноте сертификат в кодировке **Base-64**, скачанный с портала Azure, скопируйте его в буфер обмена и вставьте в текстовое поле **Открытый сертификат**.
    
1. В области **Конфигурация ответа и проверки подлинности**выполните следующие действия.
    
    ![Конфигурация ответа и проверки подлинности](./media/igloo-software-tutorial/IC783971.png "Конфигурация ответа и проверки подлинности")
  
      a. Выберите для параметра **Поставщик удостоверений** значение **Microsoft ADFS**.
      
      b. Выберите для параметра **Тип идентификатора** значение **Адрес электронной почты**. 

      c. В текстовом поле **Email Attribute** (Атрибут электронной почты) введите значение **emailaddress**.

      d. В текстовое поле **Атрибут имени** введите значение **givenname**.

      д. В текстовое поле **Атрибут фамилии** введите значение **surname**.

1. Выполните следующие действия для завершения настройки:
    
    ![Создание пользователя при входе](./media/igloo-software-tutorial/IC783972.png "Создание пользователя при входе") 

     a. Выберите для параметра **Создание пользователя при входе** значение **Создать нового пользователя на веб-сайте при входе**.

     b. Выберите для параметра **Параметры входа** значение **Использовать кнопку SAML на экране входа**.

     c. Выберите команду **Сохранить**.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/igloo-software-tutorial/create_aaduser_01.png) 

1. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/igloo-software-tutorial/create_aaduser_02.png) 

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/igloo-software-tutorial/create_aaduser_03.png) 

1. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-an-igloo-software-test-user"></a>Создание тестового пользователя Igloo Software

Элемент действия для настройки подготовки пользователей в Igloo Software отсутствует.  

Когда назначенный пользователь пытается войти в Igloo Software с помощью панели доступа, Igloo Software проверяет, существует ли данный пользователь.  Если учетная запись пользователя отсутствует, Igloo Software автоматически создает ее.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как включить единый вход Azure для пользователя Britta Simon и предоставить этому пользователю доступ к Igloo Software.

![Назначение пользователя][200] 

**Чтобы назначить Britta Simon для Igloo Software, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **Igloo Software**.

    ![Настройка единого входа](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

1. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Igloo Software на панели доступа, вы автоматически войдете в приложение Igloo Software.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/igloo-software-tutorial/tutorial_general_203.png

