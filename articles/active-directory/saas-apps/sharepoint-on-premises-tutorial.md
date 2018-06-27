---
title: Руководство по интеграции Azure Active Directory с локальной версией SharePoint | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и локальной версией SharePoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 8cae1e2670ba449c0db28209c54b740e927bbb73
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210383"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Руководство по интеграции Azure Active Directory с локальной версией SharePoint

В этом руководстве описано, как интегрировать локальную версию SharePoint с Azure Active Directory (Azure AD).

Интеграция локальной версии SharePoint с Azure AD дает указанные ниже преимущества:

- Вы можете управлять доступом пользователей к локальной версии SharePoint в Azure AD.
- Вы можете включить автоматический вход (единый вход) пользователей в локальную версию SharePoint с учетными записями Azure AD.
- Вы можете управлять учетными записями централизованно — на портале Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с локальной версией SharePoint, вам потребуется следующее:

- подписка Azure AD;
- подписка на локальную версию SharePoint с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление локальной версии SharePoint из коллекции
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Добавление локальной версии SharePoint из коллекции
Чтобы настроить интеграцию локальной версии SharePoint с Azure AD, вам потребуется добавить локальную версию SharePoint из коллекции в ваш список управляемых приложений SaaS.

**Чтобы добавить локальную версию SharePoint из коллекции, выполните следующие действия:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]

3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **SharePoint (локальная версия)**, на панели результатов выберите **Локальная версия SharePoint** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Локальная версия SharePoint в списке результатов](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

Работая с этим разделом, вы настроите и протестируете единый вход Azure AD для локальной версии SharePoint с помощью тестового пользователя Britta Simon.

Чтобы настроить единый вход в Azure AD, необходимо знать о том, какой пользователь в локальной версии SharePoint соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в локальной версии SharePoint.

Чтобы настроить и протестировать единый вход Azure AD в локальной версии SharePoint, вам потребуется выполнить действия в следующих стандартных блоках:

1. **[Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user)** требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя в локальной версии SharePoint](#create-a-sharePoint-on-premises-test-user)**. Это необходимо, чтобы в локальной версии SharePoint существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD.
5. **[Проверка единого входа](#test-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

Изучая этот раздел, вы включите функцию единого входа Azure AD на портале Azure и настроите единый вход в локальном приложении SharePoint.

**Чтобы настроить единый вход Azure AD для локальной версии SharePoint, выполните следующие действия**

1. На портале Azure откройте страницу интеграции с **локальным приложением SharePoint** и щелкните **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.

    ![Диалоговое окно "Единый вход"](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. В разделе **Домен и URL-адреса для локальной версии SharePoint** выполните следующие действия:

    ![Сведения о домене и URL-адресах единого входа для локальной версии SharePoint](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<YourSharePointServerURL>/_trust/default.aspx`

    Б. В текстовом поле **Идентификатор** введите URL-адрес в следующем формате: `urn:sharepoint:<YourSharePointServerURL>`

    > [!NOTE]
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа и идентификатором. Чтобы получить эти значения, обратитесь в [службу поддержки клиентов локальной версии SharePoint](https://support.office.com/).

4. В разделе **Сертификат подписи SAML** щелкните **XML метаданных** и сохраните CER-файл метаданных на компьютере. Скопируйте и вставьте полный путь скачанного файла метаданных в Блокнот.

    ![Ссылка для скачивания сертификата](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

5. Нажмите кнопку **Сохранить** .

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. В разделе **Конфигурация локальной версии SharePoint** щелкните **Настроить локальную версию SharePoint**, чтобы открыть окно **Настройка входа**. Скопируйте **URL-адрес службы единого входа** из раздела **Краткий справочник**.

    ![Конфигурация локальной версии SharePoint](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > В локальном приложении SharePoint используется токен SAML 1.1, поэтому Azure AD ожидает запроса WS Fed от сервера SharePoint и после аутентификации создает токен SAML 1.1.

7. В другом окне веб-браузера выполните вход на корпоративном сайте локальной версии SharePoint в качестве администратора.

8. **Настройка нового доверенного поставщика удостоверений в SharePoint Server 2016**

    Войдите в систему на сервере SharePoint Server 2016 и откройте командную консоль SharePoint 2016. Укажите значения $realm, $wsfedurl и $filepath с портала Azure и настройте новый доверенный поставщик удостоверений, выполнив следующие команды.

    > [!TIP]
    > Если вы не знаете, как использовать PowerShell, или хотите получить дополнительные сведения о том, как работает PowerShell, см. статью [SharePoint PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Далее включите доверенный поставщик удостоверений для вашего приложения, выполнив следующие действия:

    a. В центре администрирования перейдите в раздел **Управление веб-приложениями** и выберите веб-приложение, которое необходимо защитить с помощью Azure AD.

    Б. На ленте щелкните **Поставщики проверки подлинности** и выберите зону, которую нужно использовать.

    c. Щелкните пункт **Доверенный поставщик удостоверений** и выберите поставщика, которого вы только что зарегистрировали под именем *AzureAD*.

    d. В разделе параметра URL-адреса страницы входа щелкните **Настраиваемая страница входа** и укажите значение /_trust/.

    д. Последовательно выберите **ОК**.

    ![Настройка поставщика проверки подлинности](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее.

    ![Диалоговое окно "Пользователь"](./media/sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.

### <a name="create-a-sharepoint-on-premises-test-user"></a>Создание тестового пользователя в локальной версии SharePoint

1. В центре администрирования щелкните **Управление приложениями**.

2. На странице **Управление приложениями** щелкните **Управление веб-приложениями** в разделе **Веб-приложения**.

3. Выберите необходимое веб-приложение и щелкните **Политика пользователя**.

4. В политике для веб-приложения нажмите кнопку **Добавить пользователей**.

    ![Поиск пользователя по утверждению имени](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. В диалоговом окне **Добавление пользователей** щелкните соответствующую зону в разделе **Зоны** и нажмите кнопку **Далее**.

6. В диалоговом окне **Политика для веб-приложения** щелкните значок **Обзор** в разделе **Выбор пользователей**.

7. В текстовом поле **Поиск** введите имя пользователя, используемое для входа, из каталога и нажмите кнопку **Поиск**. </br>Пример: *demouser@blueskyabove.onmicrosoft.com*.

8. Под заголовком AzureAD в представлении списка выберите свойство name и нажмите кнопку **Добавить**. После этого закройте диалоговое окно, нажав кнопку **ОК**.

9. В разделе "Разрешения" щелкните **Полный доступ**.

    ![Предоставление полного доступа пользователю утверждений](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Нажмите кнопку **Готово**, а затем — кнопку **ОК**.

### <a name="fixing-people-picker"></a>Исправление средства выбора людей

Теперь пользователи могут входить в SharePoint 2016, используя удостоверения из Azure AD. Но пользовательский интерфейс можно сделать еще лучше. Например, при поиске пользователя в средстве выбора людей система возвращает несколько результатов поиска. Для каждого из трех типов утверждений, созданных в сопоставлении утверждений, предусмотрен отдельный результат поиска. Чтобы выбрать пользователя с помощью средства выбора, вам потребуется ввести точное имя пользователя и выбрать результат утверждения **name**.

![Результаты поиска утверждений](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Система не проверяет значения, по которым вы выполняете поиск. Это может привести к опечаткам и к тому, что пользователи могут случайно выбрать неправильный тип утверждения для назначения, например утверждение **SurName**. Это может помешать пользователям получить доступ к ресурсам.

Для этого сценария можно использовать решение с открытым кодом [AzureCP](https://yvand.github.io/AzureCP/), в котором есть настраиваемый поставщик утверждений для SharePoint 2016. С помощью Azure AD Graph он разрешает и проверяет вводимые пользователями данные. Дополнительные сведения см. в статье об [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к локальной версии SharePoint.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в локальной версии SharePoint, выполните следующие действия:**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201]

2. В списке приложений выберите **Локальная версия SharePoint**.

    ![Ссылка на SharePoint в списке "Приложения"](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.

### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку локальной версии SharePoint на панели доступа, вы автоматически войдете в локальное приложение SharePoint.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/sharepoint-on-premises-tutorial/tutorial_general_203.png

