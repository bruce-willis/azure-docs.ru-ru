---
title: Руководство по интеграции Azure Active Directory со Skills Base | Документы Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Skills Base.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 237d90c4-8243-4f80-a305-b5ad9204159e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 84aac0017496c50f0006fd6e184537e4c14f10c7
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057616"
---
# <a name="tutorial-azure-active-directory-integration-with-skills-base"></a>Руководство. Интеграция Azure Active Directory со Skills Base

В этом руководстве описано, как интегрировать Skills Base с Azure Active Directory (Azure AD).

Интеграция Azure AD с Skills Base обеспечивает следующие преимущества:

- С помощью Azure AD можно контролировать доступ к Skills Base.
- Вы можете включить автоматический вход пользователей в Skills Base (единый вход) с использованием учетной записи Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с приложением Skills Base, вам потребуется следующее:

- подписка Azure AD;
- подписка Skills Base с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Skills Base из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-skills-base-from-the-gallery"></a>Добавление Skills Base из коллекции
Чтобы настроить интеграцию Skills Base с Azure AD, необходимо добавить Skills Base из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Skills Base из коллекции, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Skills Base** и выберите **Skills Base** на панели результатов, а затем нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Skills Base в списке результатов](./media/skillsbase-tutorial/tutorial_skillsbase_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Skills Base с использованием тестового пользователя Britta Simon.

Для работы единого входа в Azure AD необходимо знать, какой пользователь в Skills Base соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Skills Base.

Чтобы настроить и проверить единый вход Azure AD в Skills Base, потребуется выполнить действия в следующих стандартных блоках.

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Skills Base](#create-a-skills-base-test-user)** требуется для того, чтобы в Skills Base существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Skills Base.

**Чтобы настроить единый вход Azure AD в Skills Base, выполните следующие действия.**

1. На портале Azure на странице интеграции с приложением **Skills Base** щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Диалоговое окно "Единый вход"](./media/skillsbase-tutorial/tutorial_skillsbase_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Skills Base** сделайте следующее:

    ![Сведения о домене и URL-адресах единого входа приложения Skills Base](./media/skillsbase-tutorial/tutorial_skillsbase_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://app.skills-base.com/o/<customer-unique-key>`

    > [!NOTE] 
    > Значение URL-адреса входа приведено для примера. Вместо него необходимо указать фактический URL-адрес входа. Чтобы получить это значение, обратитесь в [службу поддержки клиентов Skills Base](mailto:support@skills-base.com).

4. В разделе **Сертификат подписи SAML** щелкните **Metadata XML** (Метаданные XML) и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/skillsbase-tutorial/tutorial_skillsbase_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/skillsbase-tutorial/tutorial_general_400.png)

6. В другом окне браузера войдите в приложение Skills Base с правами администратора безопасности.

7. В левой части меню в разделе **АДМИНИСТРАТОР** щелкните **Проверка подлинности**.

    ![Администратор](./media/skillsbase-tutorial/tutorial_skillsbase_auth.png)

8. На странице **Проверка подлинности** в качестве единого входа выберите **SAML 2**.

    ![Единый вход](./media/skillsbase-tutorial/tutorial_skillsbase_single.png)

9. На странице **Проверка подлинности** выполните следующие действия.

    ![Единый вход](./media/skillsbase-tutorial/tutorial_skillsbase_save.png)

    a. Нажмите кнопку **Update IdP metadata** (Обновить метаданные поставщика удостоверений) рядом с параметром **Состояние** и в указанное поле вставьте содержимое XML-файла метаданных, скачанного с портала Azure.

    > [!Note]
    > Метаданные поставщика удостоверений можно также проверить с помощью средства **проверки метаданных**, как показано на снимке экрана выше.

    b. Выберите команду **Сохранить**.
    

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/skillsbase-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/skillsbase-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/skillsbase-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/skillsbase-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-skills-base-test-user"></a>Создание тестового пользователя Skills Base

Цель этого раздела — создать пользователя с именем Britta Simon в Skills Base. Приложение Skills Base поддерживает JIT-подготовку. Эта функция включена по умолчанию. В этом разделе никакие действия с вашей стороны не требуются. Пользователь будет создан при попытке получить доступ к Skills Base (если он еще не создан).

>[!Note]
>Чтобы создать учетную запись пользователя вручную, обратитесь в [службу поддержки клиентов Skills Base](mailto:support@skills-base.com).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Skills Base.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon приложению Skills Base, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **Skills Base**.

    ![Ссылка на Skills Base в списке приложений](./media/skillsbase-tutorial/tutorial_skillsbase_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Skills Base на панели доступа, вы автоматически войдете в приложение Skills Base.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/skillsbase-tutorial/tutorial_general_01.png
[2]: ./media/skillsbase-tutorial/tutorial_general_02.png
[3]: ./media/skillsbase-tutorial/tutorial_general_03.png
[4]: ./media/skillsbase-tutorial/tutorial_general_04.png

[100]: ./media/skillsbase-tutorial/tutorial_general_100.png

[200]: ./media/skillsbase-tutorial/tutorial_general_200.png
[201]: ./media/skillsbase-tutorial/tutorial_general_201.png
[202]: ./media/skillsbase-tutorial/tutorial_general_202.png
[203]: ./media/skillsbase-tutorial/tutorial_general_203.png

