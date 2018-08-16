---
title: Руководство по интеграции Azure Active Directory с ProMaster (от Inlogik) | Документация Майкрософт
description: Сведения о настройке единого входа между Azure Active Directory и ProMaster (от Inlogik).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 348dbd37-dc4f-49df-bb90-53d249d456b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: jeedes
ms.openlocfilehash: 13bb128836590fb43e0c6a2f7131f83a99a23eaf
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39507805"
---
# <a name="tutorial-azure-active-directory-integration-with-promaster-by-inlogik"></a>Руководство. Интеграция Azure Active Directory с ProMaster (от Inlogik)

В этом руководстве описано, как интегрировать Azure Active Directory (Azure AD) с приложением ProMaster (от Inlogik).

Интеграция ProMaster (от Inlogik) с Azure AD дает следующие преимущества.

- С помощью Azure AD вы можете контролировать доступ к ProMaster (от Inlogik).
- Вы можете включить автоматический вход пользователей (единый вход) в ProMaster (от Inlogik) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с ProMaster (от Inlogik), вам потребуется:

- подписка Azure AD;
- подписка ProMaster (от Inlogik) с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария

В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде.
Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление ProMaster (от Inlogik) из коллекции;
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-promaster-by-inlogik-from-the-gallery"></a>Добавление ProMaster (от Inlogik) из коллекции

Чтобы настроить интеграцию ProMaster (от Inlogik) с Azure AD, необходимо добавить ProMaster (от Inlogik) из коллекции в список управляемых приложений SaaS.

**Чтобы добавить ProMaster (от Inlogik) из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **ProMaster (от Inlogik)**, выберите **ProMaster (от Inlogik)** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![ProMaster (от Inlogik) в списке результатов](./media/promaster-tutorial/tutorial_promaster_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описано, как настроить и проверить единый вход Azure AD в ProMaster (от Inlogik) с использованием данных тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в ProMaster (от Inlogik) соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в ProMaster (от Inlogik).

Чтобы настроить и проверить единый вход Azure AD в ProMaster (от Inlogik), вам нужно выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя ProMaster (от Inlogik)](#create-a-promaster-by-inlogik-test-user)** требуется для создания в ProMaster (от Inlogik) пользователя Britta Simon, связанного с соответствующим представлением в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении ProMaster (от Inlogik).

**Чтобы настроить единый вход Azure AD в ProMaster (от Inlogik), выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **ProMaster (от Inlogik)** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/promaster-tutorial/tutorial_promaster_samlbase.png)

3. Если вы хотите настроить приложение в **режиме, инициированном поставщиком удостоверений**, то в разделе **Домены и URL-адреса приложения ProMaster (от Inlogik)** выполните следующее.

    ![Сведения о домене и URL-адресах единого входа для приложения ProMaster (от Inlogik)](./media/promaster-tutorial/tutorial_promaster_url1.png)

    a. В текстовом поле **Идентификатор** введите один из следующих шаблонов URL-адреса:
    | |
    | - |-|
    |  `https://secure.inlogik.com/<COMPANYNAME>`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE`|
    | |

    b. В текстовом поле **URL-адрес ответа** введите один из следующих шаблонов URL-адреса:
    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs`|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

4. Установите флажок **Показать дополнительные параметры URL-адресов**, и выполните следующее действие, если хотите настроить приложение для работы в режиме, инициируемом **поставщиком услуг**:

    ![Сведения о домене и URL-адресах единого входа для приложения ProMaster (от Inlogik)](./media/promaster-tutorial/tutorial_promaster_url2.png)

    В текстовом поле **URL-адрес входа** введите один из следующих шаблонов URL-адреса:
    | |
    | - |-|
    | `https://secure.inlogik.com/<COMPANYNAME>/saml/acs `|
    | `https://<CUSTOMDOMAIN>/SAMLBASE/saml/acs`|
    | |

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените их фактическими значениями идентификатора, URL-адреса ответа и URL-адреса входа. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов ProMaster (от Inlogik)](mailto:michael.boldiston@inlogik.com).

5. В разделе **Сертификат подписи SAML** нажмите кнопку "Копировать", чтобы скопировать **URL-адрес метаданных федерации приложений**. Затем вставьте его в Блокнот.

    ![Ссылка для скачивания сертификата](./media/promaster-tutorial/tutorial_promaster_certificate.png)

6. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/promaster-tutorial/tutorial_general_400.png)

7. Для настройки единого входа на стороне **ProMaster (от Inlogik)** необходимо отправить созданный **URL-адрес метаданных федерации приложений** в [службу поддержки ProMaster (от Inlogik)](mailto:michael.boldiston@inlogik.com). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/promaster-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/promaster-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/promaster-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/promaster-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-promaster-by-inlogik-test-user"></a>Создание тестового пользователя в ProMaster (от Inlogik)

В этом разделе описано, как создать пользователя Britta Simon в приложении ProMaster (от Inlogik). Обратитесь в [службу поддержки ProMaster (от Inlogik)](mailto:michael.boldiston@inlogik.com), чтобы добавить пользователей на платформу ProMaster (от Inlogik). Перед использованием единого входа необходимо создать и активировать пользователей.

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как предоставить пользователю Britta Simon доступ к ProMaster (от Inlogik), чтобы он мог использовать единый вход Azure.

![Назначение роли пользователя][200]

**Чтобы назначить пользователя Britta Simon в ProMaster (от Inlogik), выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **ProMaster (от Inlogik)**.

    ![Ссылка на ProMaster (от Inlogik) в списке приложений](./media/promaster-tutorial/tutorial_promaster_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент ProMaster (от Inlogik) на панели доступа, вы автоматически войдете в приложение ProMaster (от Inlogik).
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/promaster-tutorial/tutorial_general_01.png
[2]: ./media/promaster-tutorial/tutorial_general_02.png
[3]: ./media/promaster-tutorial/tutorial_general_03.png
[4]: ./media/promaster-tutorial/tutorial_general_04.png

[100]: ./media/promaster-tutorial/tutorial_general_100.png

[200]: ./media/promaster-tutorial/tutorial_general_200.png
[201]: ./media/promaster-tutorial/tutorial_general_201.png
[202]: ./media/promaster-tutorial/tutorial_general_202.png
[203]: ./media/promaster-tutorial/tutorial_general_203.png

