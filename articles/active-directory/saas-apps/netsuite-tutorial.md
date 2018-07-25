---
title: Руководство по интеграции Azure Active Directory с NetSuite | Документы Майкрософт
description: Сведения о том, как настроить единый вход Azure Active Directory в NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: jeedes
ms.openlocfilehash: 5a20af1130d50209b29ad44195c14f30cba30c43
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051859"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Руководство по интеграции Azure Active Directory с NetSuite

В этом руководстве описано, как интегрировать NetSuite с Azure Active Directory (Azure AD).

Интеграция Azure AD с NetSuite обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к NetSuite.
- Вы можете включить автоматический вход пользователей в NetSuite (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с NetSuite, вам потребуется:

- подписка Azure AD;
- подписка NetSuite с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление NetSuite из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-netsuite-from-the-gallery"></a>Добавление NetSuite из коллекции
Чтобы настроить интеграцию NetSuite с Azure AD, нужно добавить NetSuite из коллекции в список управляемых приложений SaaS.

**Чтобы добавить NetSuite из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]

3. В верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **NetSuite**, выберите **NetSuite** на панели результатов и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![NetSuite в списке результатов](./media/netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в приложение NetSuite с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD нужна информация о том, какой пользователь в NetSuite соответствует пользователю в Azure AD. Иными словами, нужно установить связь между пользователем Azure AD и соответствующим пользователем в NetSuite.

Чтобы установить эту связь, следует назначить **имя пользователя** в Azure AD в качестве значения **имени пользователя** в NetSuite.

Чтобы настроить и проверить единый вход Azure AD в NetSuite, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя NetSuite](#creating-a-netsuite-test-user)** требуется для того, чтобы в NetSuite существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении NetSuite.

**Чтобы настроить единый вход Azure AD в NetSuite, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **NetSuite** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Настройка единого входа](./media/NetSuite-tutorial/tutorial_NetSuite_samlbase.png)

3. В разделе **Домены и URL-адреса приложения NetSuite** сделайте следующее:

    ![Настройка единого входа](./media/NetSuite-tutorial/tutorial_NetSuite_url.png)

    В текстовом поле **URL-адрес ответа** введите URL-адрес в следующем формате:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`
    
    > [!NOTE]
    > Значения, указанные выше, приведены в качестве примера. Измените эти значения, указав фактический URL-адрес ответа. Чтобы получить эти значения, обратитесь к [группе поддержки NetSuite](http://www.NetSuite.com/portal/services/support.shtml).

4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните XML-файл на компьютере.

    ![Настройка единого входа](./media/NetSuite-tutorial/tutorial_NetSuite_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/NetSuite-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация NetSuite** щелкните **Настроить NetSuite**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/NetSuite-tutorial/tutorial_NetSuite_configure.png)

7. Откройте новую вкладку в браузере и войдите как администратор корпоративного сайта NetSuite.

8. На панели инструментов в верхней части страницы щелкните **Настройка**, перейдите в раздел **Компания** и щелкните **Включить компоненты**.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-setupsaml.png)

9. На панели инструментов в средней части страницы щелкните **SuiteCloud**.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-suitecloud.png)

10. В разделе **Manage Authentication** (Управление проверкой подлинности) выберите **Единый вход SAML** для включения единого входа SAML в NetSuite.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-ticksaml.png)

11. На панели инструментов в верхней части страницы щелкните **Настройка**.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-setup.png)

12. В списке **SETUP TASKS** (Задачи настройки) выберите пункт **Интеграция**.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-integration.png)

13. В разделе **Manage Authentication** (Управление проверкой подлинности) щелкните **Единый вход SAML**.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-saml.png)

14. На странице **SAML Setup** (Настройка SAML) в разделе **NetSuite Configuration** (Конфигурация NetSuite) выполните следующие действия:

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Выберите **PRIMARY AUTHENTICATION METHOD** (Основной способ проверки подлинности).

    b. В поле **SAMLV2 IDENTITY PROVIDER METADATA** (Метаданные поставщика удостоверений SAMLV2) выберите **UPLOAD IDP METADATA FILE** (Отправить файл метаданных поставщика удостоверений). Затем нажмите кнопку **Обзор**, чтобы добавить файл метаданных, скачанный с портала Azure.

    c. Нажмите кнопку **Submit**(Отправить).

15. В Azure AD установите флажок **Просмотреть и изменить все другие атрибуты пользователей** и добавьте атрибут.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-attributes.png)

16. В поле **Имя атрибута** введите `account`. В поле **Значение атрибута** введите идентификатор учетной записи NetSuite. Это значение является константой и изменяется вместе с учетной записью. Инструкции по поиску идентификатора учетной записи приведены ниже.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-add-attribute.png)

    a. В NetSuite выберите **Настройка**, перейдите в раздел **Компания** и выберите пункт **Сведения о компании** в меню навигации сверху.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-com.png)

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-account-id.png)

    b. На странице **Сведения о компании** в правом столбце скопируйте **идентификатор учетной записи**.

    c. Вставьте **идентификатор учетной записи**, скопированный из учетной записи NetSuite, в поле **Значение атрибута** в Azure AD. 

17. Прежде чем пользователи смогут выполнять единый вход в NetSuite, необходимо назначить им соответствующие разрешения в NetSuite. Чтобы предоставить необходимые разрешения, выполните следующие инструкции.

    a. В верхнем меню навигации выберите **Настройка**.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-setup.png)

    b. В меню навигации слева выберите **Users/Roles** (Пользователи и роли), а затем — **Manage Roles** (Управление ролями).

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Нажмите кнопку **Создать роль**.

    d. Введите **имя** для новой роли.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-new-role.png)

    д. Выберите команду **Сохранить**.

    f. В верхнем меню щелкните **Разрешения**. Затем щелкните **Настройка**.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-sso.png)

    ж. Выберите **Единый вход SAML** и нажмите кнопку **Добавить**.

    h. Выберите команду **Сохранить**.

    i. В верхнем меню навигации выберите **Setup** (Настройка) и щелкните **Setup Manager** (Диспетчер настройки).

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-setup.png)

    j. В меню навигации слева выберите **Users/Roles** (Пользователи и роли), а затем — **Manage Users** (Управление пользователями).

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Выберите тестового пользователя. Затем нажмите кнопку **Изменить** и перейдите на вкладку **Доступ**.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-edit-user.png)

    l. В диалоговом окне "Роли" назначьте подходящую роль, которую вы создали.

    ![Настройка единого входа](./media/NetSuite-tutorial/ns-add-role.png)

    m. Выберите команду **Сохранить**.
 
### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD
Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/NetSuite-tutorial/create_aaduser_01.png) 

2.  Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/NetSuite-tutorial/create_aaduser_02.png) 

3. В верхней части диалогового окна щелкните **Добавить**, чтобы открыть диалоговое окно **Пользователь**.
 
    ![Создание тестового пользователя Azure AD](./media/NetSuite-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/NetSuite-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**. 

### <a name="creating-a-netsuite-test-user"></a>Создание тестового пользователя NetSuite

В этом разделе вы создадите в NetSuite пользователя с именем Britta Simon. NetSuite поддерживает JIT-подготовку. Эта функция включена по умолчанию.
В этом разделе никакие действия с вашей стороны не требуются. Если пользователь еще не существует в NetSuite, то он создается при попытке доступа к приложению NetSuite.


### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к NetSuite.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в NetSuite, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. В списке приложений выберите **NetSuite**.

    ![Настройка единого входа](./media/NetSuite-tutorial/tutorial_NetSuite_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Чтобы проверить параметры единого входа, откройте панель доступа по адресу [https://myapps.microsoft.com](https://myapps.microsoft.com/), выполните вход с тестовой учетной записью и щелкните **NetSuite**.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Руководство по настройке Google Apps для автоматической подготовки пользователей](NetSuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/NetSuite-tutorial/tutorial_general_01.png
[2]: ./media/NetSuite-tutorial/tutorial_general_02.png
[3]: ./media/NetSuite-tutorial/tutorial_general_03.png
[4]: ./media/NetSuite-tutorial/tutorial_general_04.png

[100]: ./media/NetSuite-tutorial/tutorial_general_100.png

[200]: ./media/NetSuite-tutorial/tutorial_general_200.png
[201]: ./media/NetSuite-tutorial/tutorial_general_201.png
[202]: ./media/NetSuite-tutorial/tutorial_general_202.png
[203]: ./media/NetSuite-tutorial/tutorial_general_203.png

