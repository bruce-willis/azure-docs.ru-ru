---
title: Руководство по интеграции Azure Active Directory с BenSelect | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и BenSelect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ffa17478-3ea1-4356-a289-545b5b9a4494
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 9af2de667085ef736c3ae37b75e3c67981161267
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39434284"
---
# <a name="tutorial-azure-active-directory-integration-with-benselect"></a>Руководство. Интеграция Azure Active Directory с BenSelect

В этом руководстве описано, как интегрировать BenSelect с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением BenSelect дает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к BenSelect.
- Вы можете включить автоматический вход пользователей в BenSelect (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с BenSelect, вам потребуется:

- подписка Azure AD;
- подписка BenSelect с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление BenSelect из коллекции.
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-benselect-from-the-gallery"></a>Добавление BenSelect из коллекции.
Чтобы настроить интеграцию BenSelect с Azure AD, необходимо добавить BenSelect из коллекции в список управляемых приложений SaaS.

**Чтобы добавить BenSelect из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

1. В поле поиска введите **BenSelect**.

    ![Создание тестового пользователя Azure AD](./media/benselect-tutorial/tutorial_benselect_search.png)

1. На панели результатов выберите **BenSelect** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/benselect-tutorial/tutorial_benselect_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в BenSelect с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в BenSelect соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в BenSelect.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в BenSelect.

Чтобы настроить и проверить единый вход Azure AD в BenSelect, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя BenSelect](#creating-a-benselect-test-user)** нужно для того, чтобы в BenSelect также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
1. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении BenSelect.

**Чтобы настроить единый вход Azure AD в BenSelect, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **BenSelect** щелкните **Единый вход**.

    ![Настройка единого входа][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/benselect-tutorial/tutorial_benselect_samlbase.png)

1. В разделе **Домены и URL-адреса приложения BenSelect** сделайте следующее.

    ![Настройка единого входа](./media/benselect-tutorial/tutorial_benselect_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате: `https://www.benselect.com/enroll/login.aspx?Path=<tenant name>`.

    > [!NOTE] 
    > Это значение приведено для справки. Вместо него нужно указать фактический URL-адрес ответа. Чтобы получить это значение, обратитесь к [группе поддержки BenSelect](mailto:support@selerix.com).
 
1. В разделе **Сертификат подписи SAML** щелкните **Сертификат (необработанный)**, а затем сохраните файл сертификата на компьютер.

    ![Настройка единого входа](./media/benselect-tutorial/tutorial_benselect_certificate.png) 

1. Приложение BenSelect ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/benselect-tutorial/tutorial_benselect_06.png)

1. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** выполните следующие действия.

    a. Из раскрывающегося списка **Идентификатор пользователя** выберите **ExtractMailPrefix**.

    b. Из раскрывающегося списка **Электронная почта** выберите **user.userprincipalname**.

1. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/benselect-tutorial/tutorial_general_400.png)

1. В разделе **Конфигурация BenSelect** щелкните **Настроить BenSelect**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/benselect-tutorial/tutorial_benselect_configure.png) 

1. Чтобы настроить единый вход на стороне **BenSelect**, нужно отправить скачанный **сертификат (необработанный)**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** [группе поддержки BenSelect](mailto:support@selerix.com).

   >[!NOTE]
   >Необходимо отметить, что для этой интеграции требуется алгоритм SHA256 (SHA1 не поддерживается), чтобы настроить единый вход на соответствующем сервере, например app2101 и т. п. 
   
> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/benselect-tutorial/create_aaduser_01.png) 

1. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/benselect-tutorial/create_aaduser_02.png) 

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/benselect-tutorial/create_aaduser_03.png) 

1. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/benselect-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-benselect-test-user"></a>Создание тестового пользователя BenSelect

Цель этого раздела — создать пользователя с именем Britta Simon в BenSelect. Обратитесь к [группе поддержки BenSelect](mailto:support@selerix.com), чтобы добавить пользователей в учетную запись BenSelect.

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к BenSelect.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в BenSelect, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **BenSelect**.

    ![Настройка единого входа](./media/benselect-tutorial/tutorial_benselect_app.png) 

1. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент BenSelect на панели доступа, вы автоматически войдете в приложение BenSelect.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/benselect-tutorial/tutorial_general_01.png
[2]: ./media/benselect-tutorial/tutorial_general_02.png
[3]: ./media/benselect-tutorial/tutorial_general_03.png
[4]: ./media/benselect-tutorial/tutorial_general_04.png

[100]: ./media/benselect-tutorial/tutorial_general_100.png

[200]: ./media/benselect-tutorial/tutorial_general_200.png
[201]: ./media/benselect-tutorial/tutorial_general_201.png
[202]: ./media/benselect-tutorial/tutorial_general_202.png
[203]: ./media/benselect-tutorial/tutorial_general_203.png

