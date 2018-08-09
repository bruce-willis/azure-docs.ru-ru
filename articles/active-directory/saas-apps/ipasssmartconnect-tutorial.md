---
title: Учебник. Интеграция Azure Active Directory с iPass SmartConnect | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: ecfdd3fae1d394e3b57fcd325f44cad0d1a98534
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444900"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Учебник. Интеграция Azure Active Directory с iPass SmartConnect

В этом руководстве описано, как интегрировать iPass SmartConnect с Azure Active Directory (Azure AD).

Интеграция iPass SmartConnect с Azure AD позволяет получить следующие преимущества:

- Вы можете контролировать доступ к iPass SmartConnect с помощью Azure AD.
- Вы можете включить автоматический вход пользователей в iPass SmartConnect (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с iPass SmartConnect, вам потребуется:

- подписка Azure AD;
- подписка iPass SmartConnect с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление iPass SmartConnect из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Добавление iPass SmartConnect из коллекции
Чтобы настроить интеграцию iPass SmartConnect с Azure AD, необходимо добавить iPass SmartConnect из коллекции в список управляемых приложений SaaS.

**Чтобы добавить iPass SmartConnect из коллекции, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **iPass SmartConnect**, выберите **iPass SmartConnect** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![iPass SmartConnect в списке результатов](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в iPass SmartConnect с использованием тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать, какой пользователь в iPass SmartConnect соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в iPass SmartConnect.

Чтобы настроить и проверить единый вход Azure AD в iPass SmartConnect, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя iPass SmartConnect](#create-an-ipass-smartconnect-test-user)** требуется для того, чтобы в iPass SmartConnect существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении iPass SmartConnect.

**Чтобы настроить единый вход Azure AD в iPass SmartConnect, сделайте следующее:**

1. На портале Azure на странице интеграции с приложением **iPass SmartConnect** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. В разделе **Домены и URL-адреса приложения iPass SmartConnect** выполнять какие-либо действия не требуется, если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**.

    ![Сведения о домене и URL-адресах единого входа для приложения iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. Установите флажок "Показать дополнительные параметры URL-адресов" и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **SP** (поставщиком услуг):

    ![Сведения о домене и URL-адресах единого входа для приложения iPass SmartConnect](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    В текстовом поле "URL-адрес для входа" введите URL-адрес: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. Приложение iPass SmartConnect ожидает утверждения SAML в определенном формате. Настройте следующие утверждения для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.

    ![Настройка единого входа](./media/ipasssmartconnect-tutorial/attribute.png)

1. В разделе **Атрибуты пользователя** установите флажок **Просмотреть и изменить все другие атрибуты пользователей**, чтобы развернуть атрибуты. Выполните следующие действия для каждого отображаемого атрибута.

    | Имя атрибута | Значение атрибута | Значение пространства имен|
    | ---------------| --------------- |----------------|
    | firstName | user.givenname |   |
    | lastName | user.surname | |
    | email | user.userprincipalname | |
    | Имя пользователя | user.userprincipalname | |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.

    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.

    d. Параметр "Пространство имен" для этой строки оставьте пустым.

    д. Нажмите кнопку **ОК**.

1. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. Чтобы настроить единый вход на стороне **iPass SmartConnect**, отправьте в [службу поддержки iPass SmartConnect](mailto:help@ipass.com) скачанный **XML-файл метаданных** и **Имя домена**. Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Создание тестового пользователя iPass SmartConnect

В этом разделе описано, как создать пользователя Britta Simon в приложении iPass SmartConnect. Обратитесь в [группу поддержки iPass SmartConnect](mailto:help@ipass.com), чтобы добавить домен или пользователей, которых нужно включить в список разрешений на платформе iPass SmartConnect. Если служба поддержки добавит домен, пользователи будут автоматически подготовлены для платформы iPass SmartConnect. Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к iPass SmartConnect.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в iPass SmartConnect, сделайте следующее:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

1. В списке приложений выберите **iPass SmartConnect**.

    ![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

**Чтобы проверить приложение в режиме, инициируемом поставщиком услуг, выполните следующие действия:**

a. Скачайте клиент iPass SmartConnect [здесь](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/testing3.png)

b. Установите и запустите клиент.

c. Нажмите кнопку **Начать работу**.

![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Введите имя пользователя в Azure с доменом. Нажмите кнопку **Продолжить**. Вы перейдете на страницу входа в Azure.

![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/testing2.png) 

д. После проверки подлинности будет запущена активация клиента. Клиент будет активирован.

**Чтобы проверить приложение в режиме, инициируемом поставщиком удостоверений, выполните следующие действия:**

a. Войдите в [https://myapps.microsoft.com](https://myapps.microsoft.com).

b. Щелкните приложение iPass SmartConnect.

c. Будет открыта страница SSA. Нажмите кнопку **Скачать приложение для Windows**, чтобы установить клиент iPass SmartConnect.

![Ссылка iPass SmartConnect в списке "Приложения"](./media/ipasssmartconnect-tutorial/testing4.png)

d. При первом запуске клиента активация будет запущена автоматически после принятия условий использования.

д. Если активация не запускается, нажмите кнопку "Активировать" на странице SSA, чтобы начать активацию.

Е. Клиент будет активирован.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

