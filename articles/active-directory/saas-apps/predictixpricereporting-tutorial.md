---
title: Руководство по интеграции Azure Active Directory с Predictix Price Reporting | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Predictix Price Reporting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: 3686a90cb088dae99d20df619c161251b5bdfd60
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438949"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Учебник. Интеграция Azure Active Directory с Predictix Price Reporting

В этом учебнике описано, как интегрировать Predictix Price Reporting с Azure Active Directory (Azure AD).

Интеграция Azure AD с Predictix Price Reporting обеспечивает перечисленные ниже преимущества.

- С помощью Azure AD вы можете контролировать доступ к Predictix Price Reporting.
- Вы можете включить автоматический вход пользователей в Predictix Price Reporting (единый вход) с использованием учетных записей Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Predictix Price Reporting, вам потребуется:

- подписка Azure AD;
- подписка Predictix Price Reporting с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление приложения Predictix Price Reporting из коллекции
1. настройка и проверка единого входа в Azure AD.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Добавление приложения Predictix Price Reporting из коллекции
Чтобы настроить интеграцию Predictix Price Reporting с Azure AD, необходимо добавить приложение Predictix Price Reporting из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Predictix Price Reporting из коллекции, выполните указанные ниже действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

1. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
1. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

1. В поле поиска введите **Predictix Price Reporting**, выберите **Predictix Price Reporting** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Predictix Price Reporting в списке результатов](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Predictix Price Reporting с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Predictix Price Reporting соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Predictix Price Reporting.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Predictix Price Reporting.

Чтобы настроить и проверить единый вход Azure AD в Predictix Price Reporting, вам потребуется выполнить действия в приведенных ниже стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
1. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
1. **[Создание тестового пользователя Predictix Price Reporting](#create-a-predictix-price-reporting-test-user)** требуется для создания в Predictix Price Reporting пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
1. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
1. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Predictix Price Reporting.

**Чтобы настроить единый вход Azure AD в Predictix Price Reporting, выполните указанные ниже действия.**

1. На портале Azure на странице интеграции с приложением **Predictix Price Reporting** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

1. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_samlbase.png)

1. В разделе **Домены и URL-адреса приложения Predictix Price Reporting** выполните следующие действия.

    ![Сведения о домене и URL-адресах единого входа приложения Predictix Price Reporting](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_url.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<companyname-pricing>.predictix.com/sso/request`

    b. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате:
    | |
    |--|
    | `https://<companyname-pricing>.predictix.com` |
    | `https://<companyname-pricing>.dev.predictix.com` |

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов Predictix Price Reporting](http://www.infor.com/company/customer-center/). 
 
1. В разделе **Сертификат для подписи токена SAML** щелкните **Certificate (Base64)** (Сертификат (Base64)), а затем сохраните файл сертификата на компьютере.

    ![Ссылка для скачивания сертификата](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_certificate.png) 

1. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/predictixpricereporting-tutorial/tutorial_general_400.png)

1. В разделе **Конфигурация Predictix Price Reporting** щелкните **Настроить Predictix Price Reporting**, чтобы открыть окно **Настройка входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка Predictix Price Reporting](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_configure.png) 

1. Чтобы настроить единый вход на стороне **Predictix Price Reporting**, нужно передать скачанный **сертификат (Base64)**, **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** в [службу поддержки Predictix Price Reporting](http://www.infor.com/company/customer-center/). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

> [!TIP]
> Краткую версию этих инструкций теперь можно также прочитать на [портале Azure](https://portal.azure.com) во время настройки приложения.  После добавления этого приложения из раздела **Active Directory > Корпоративные приложения** просто выберите вкладку **Единый вход** и откройте встроенную документацию через раздел **Настройка** в нижней части страницы. Дополнительные сведения о встроенной документации см. в разделе [Встроенная документация Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/predictixpricereporting-tutorial/create_aaduser_01.png)

1. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/predictixpricereporting-tutorial/create_aaduser_02.png)

1. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/predictixpricereporting-tutorial/create_aaduser_03.png)

1. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/predictixpricereporting-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-predictix-price-reporting-test-user"></a>Создание тестового пользователя Predictix Price Reporting

В этом разделе описано, как создать пользователя Britta Simon в приложении Predictix Price Reporting. Чтобы добавить пользователей в платформу Predictix Price Reporting, обратитесь в [службу поддержки Predictix Price Reporting](http://www.infor.com/company/customer-center/).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как позволить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Predictix Price Reporting.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Predictix Price Reporting, выполните указанные ниже действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

1. В списке приложений выберите **Predictix Price Reporting**.

    ![Ссылка на Predictix Price Reporting в списке приложений](./media/predictixpricereporting-tutorial/tutorial_predictixpricereporting_app.png)  

1. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

1. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

1. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

1. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

1. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент Predictix Price Reporting на панели доступа, вы автоматически войдете в приложение Predictix Price Reporting.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/predictixpricereporting-tutorial/tutorial_general_01.png
[2]: ./media/predictixpricereporting-tutorial/tutorial_general_02.png
[3]: ./media/predictixpricereporting-tutorial/tutorial_general_03.png
[4]: ./media/predictixpricereporting-tutorial/tutorial_general_04.png

[100]: ./media/predictixpricereporting-tutorial/tutorial_general_100.png

[200]: ./media/predictixpricereporting-tutorial/tutorial_general_200.png
[201]: ./media/predictixpricereporting-tutorial/tutorial_general_201.png
[202]: ./media/predictixpricereporting-tutorial/tutorial_general_202.png
[203]: ./media/predictixpricereporting-tutorial/tutorial_general_203.png

