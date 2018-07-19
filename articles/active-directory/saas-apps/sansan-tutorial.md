---
title: Руководство по интеграции Azure Active Directory с Sansan | Документация Майкрософт
description: Узнайте, как настроить единый вход Azure Active Directory в Sansan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f653a0f2-c44a-4670-b936-68c136b578ea
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: cc070f7c4cb201e68c93b0b1337982325df74663
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051267"
---
# <a name="tutorial-azure-active-directory-integration-with-sansan"></a>Руководство по интеграции Azure Active Directory с Sansan

В этом руководстве описано, как интегрировать Sansan с Azure Active Directory (Azure AD).

Интеграция Azure AD с приложением Sansan обеспечивает следующие преимущества:

- С помощью Azure AD вы можете контролировать доступ к Sansan.
- Вы можете включить автоматический вход пользователей в Sansan (единый вход) с учетной записью Azure AD.
- Вы можете управлять учетными записями централизованно — через портал Azure.

Подробнее узнать об интеграции приложений SaaS с Azure AD можно в разделе [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Предварительные требования

Чтобы настроить интеграцию Azure AD с Sansan, вам потребуется:

- подписка Azure AD;
- подписка Sansan с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом учебнике.

При проверке действий в этом учебнике соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете получить пробную версию на один месяц по [этой ссылке](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. Добавление Sansan из коллекции.
2. настройка и проверка единого входа в Azure AD.

## <a name="adding-sansan-from-the-gallery"></a>Добавление Sansan из коллекции
Чтобы настроить интеграцию Sansan с Azure AD, необходимо добавить Sansan из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Sansan из коллекции, сделайте следующее.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**. 

    ![Active Directory][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![ПРИЛОЖЕНИЯ][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![ПРИЛОЖЕНИЯ][3]

4. В поле поиска введите **Sansan**.

    ![Создание тестового пользователя Azure AD](./media/sansan-tutorial/tutorial_sansan_search.png)

5. На панели результатов выберите **Sansan** и нажмите кнопку **Добавить**, чтобы добавить это приложение.

    ![Создание тестового пользователя Azure AD](./media/sansan-tutorial/tutorial_sansan_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>настройка и проверка единого входа в Azure AD.
В этом разделе описана настройка и проверка единого входа Azure AD в Sansan с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходима информация о том, какой пользователь в Sansan соответствует пользователю в Azure AD. Иными словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Sansan.

Чтобы установить эту связь, назначьте **имя пользователя** в Azure AD в качестве значения **имени пользователя** в Sansan.

Чтобы настроить и проверить единый вход Azure AD в Sansan, вам потребуется выполнить действия в следующих стандартных блоках.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** необходима, чтобы пользователи могли использовать эту функцию.
2. **[Создание тестового пользователя Azure AD](#creating-an-azure-ad-test-user)** требуется для проверки работы единого входа в Azure AD от имени пользователя Britta Simon.
3. **[Создание тестового пользователя Sansan](#creating-a-sansan-test-user)** требуется для того, чтобы в Sansan существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. **[Назначение тестового пользователя Azure AD](#assigning-the-azure-ad-test-user)** необходимо, чтобы позволить Britta Simon использовать единый вход Azure AD;
5. **[Проверка единого входа](#testing-single-sign-on)** необходима, чтобы убедиться в корректной работе конфигурации.

### <a name="configuring-azure-ad-single-sign-on"></a>Настройка единого входа в Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Sansan.

**Чтобы настроить единый вход Azure AD в Sansan, сделайте следующее.**

1. На портале Azure на странице интеграции с приложением **Sansan** щелкните **Единый вход**.

    ![Настройка единого входа][4]

2. В диалоговом окне **Единый вход** в разделе **Режим** выберите **Вход на основе SAML**, чтобы включить функцию единого входа.
 
    ![Настройка единого входа](./media/sansan-tutorial/tutorial_sansan_samlbase.png)

3. В разделе **Домены и URL-адреса приложения Sansan** выполните следующие действия.

    ![Настройка единого входа](./media/sansan-tutorial/tutorial_sansan_url.png)

    В текстовом поле **URL-адрес для входа** введите URL-адрес в следующем формате: 
    
    | Среда | URL-адрес |
    |:--- |:--- |
    | Компьютерная сеть |`https://ap.sansan.com/v/saml2/<company name>/acs` |
    | Собственное мобильное приложение |`https://internal.api.sansan.com/saml2/<company name>/acs` |
    | Параметры браузера для мобильных устройств |`https://ap.sansan.com/s/saml2/<company name>/acs` |  

    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом для входа. Чтобы получить эти значения, обратитесь к [группе поддержки клиентов Sansan](https://www.sansan.com/form/contact). 
     
4. В разделе **Сертификат подписи SAML** щелкните **Сертификат (Base64)**, а затем сохраните файл сертификата на компьютере.

    ![Настройка единого входа](./media/sansan-tutorial/tutorial_sansan_certificate.png) 

5. Нажмите кнопку **Сохранить** .

    ![Настройка единого входа](./media/sansan-tutorial/tutorial_general_400.png)

6. Приложение Sansan ожидает настройки нескольких **идентификаторов** и **URL-адресов ответа** для поддержки нескольких сред (веб-приложения для настольных ПК, собственные мобильные приложения, параметры браузера мобильного устройства), которые можно настроить с помощью сценария PowerShell. Подробные действия описаны ниже.

7. Чтобы настроить несколько **идентификаторов** и **URL-адресов ответа** для приложения Sansan с помощью скрипта PowerShell, выполните следующие действия:

    ![Настройка объекта единого входа](./media/sansan-tutorial/tutorial_sansan_objid.png)  

    a. Перейдите на страницу **Свойства** приложения **Sansan**, скопируйте **идентификатор объекта** с помощью кнопки **Копировать** и вставьте его в Блокнот.

    b. **Идентификатор объекта**, который вы скопировали на портале Azure, будет использоваться в качестве **ServicePrincipalObjectId** в скрипте PowerShell, используемом далее в этом руководстве. 

    c. Откройте окно командной строки Windows PowerShell с повышенными привилегиями.
    
    >[!NOTE] 
    > Вам потребуется установить модуль Azure AD (с помощью команды `Install-Module -Name AzureAD`). Если будет предложено установить модуль NuGet или новый модуль PowerShell Azure для Active Directory версии 2, введите Y и нажмите клавишу ВВОД.

    d. Выполните команду `Connect-AzureAD` и войдите в систему с помощью учетных данных глобального администратора.

    д. Чтобы обновить несколько URL-адресов для приложения, используйте следующий скрипт:

    ```poweshell
     Param(
    [Parameter(Mandatory=$true)][guid]$ServicePrincipalObjectId,
    [Parameter(Mandatory=$false)][string[]]$ReplyUrls,
    [Parameter(Mandatory=$false)][string[]]$IdentifierUrls
    )

    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId

    if($ReplyUrls.Length)
    {
    echo "Updating Reply urls"
    Set-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId -ReplyUrls $ReplyUrls
    echo "updated"
    }
    if($IdentifierUrls.Length)
    {
    echo "Updating Identifier urls"
    $applications = Get-AzureADApplication -SearchString $servicePrincipal.AppDisplayName 
    echo "Found Applications =" $applications.Length
    $i = 0;
    do
    {  
    $application = $applications[$i];
    if($application.AppId -eq $servicePrincipal.AppId){
    Set-AzureADApplication -ObjectId $application.ObjectId -IdentifierUris $IdentifierUrls
    $servicePrincipal = Get-AzureADServicePrincipal -ObjectId $ServicePrincipalObjectId
    echo "Updated"
    return;
    }
    $i++;
    }while($i -lt $applications.Length);
    echo "Not able to find the matched application with this service principal"
    }
    ```

8. Когда скрипт PowerShell будет успешно выполнен, отобразится результат, как на приведенном ниже снимке экрана. Значения URL-адресов обновятся, но они не отражаются на портале Azure. 

    ![Скрипт настройки единого входа](./media/sansan-tutorial/tutorial_sansan_powershell.png)


9. В разделе **Конфигурация Sansan** щелкните **Настроить Sansan**, чтобы открыть окно **Настройка единого входа**. Скопируйте **URL-адрес выхода, идентификатор сущности SAML и URL-адрес службы единого входа SAML** из раздела **Краткий справочник**.

    ![Настройка единого входа](./media/sansan-tutorial/tutorial_sansan_configure.png) 

10. Чтобы настроить единый вход на стороне **Sansan**, нужно отправить скачанный **сертификат**, **URL-адрес выхода**, **идентификатор сущности SAML** и **URL-адрес службы единого входа SAML** [группе поддержки Sansan](https://www.sansan.com/form/contact). Специалисты службы поддержки настроят подключение единого входа SAML на обеих сторонах.

>[!NOTE]
>Настройка браузера ПК подходит как для мобильного приложения и браузера для мобильных устройств, так и для компьютерной сети. 

### <a name="creating-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

![Создание пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На **портале Azure** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Создание тестового пользователя Azure AD](./media/sansan-tutorial/create_aaduser_01.png) 

2. Чтобы отобразить список пользователей, перейдите в раздел **Пользователи и группы** и щелкните **Все пользователи**.
    
    ![Создание тестового пользователя Azure AD](./media/sansan-tutorial/create_aaduser_02.png) 

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна щелкните **Добавить**.
 
    ![Создание тестового пользователя Azure AD](./media/sansan-tutorial/create_aaduser_03.png) 

4. На странице диалогового окна **Пользователь** выполните следующие действия.
 
    ![Создание тестового пользователя Azure AD](./media/sansan-tutorial/create_aaduser_04.png) 

    a. В текстовом поле **Имя** введите **BrittaSimon**.

    b. В текстовом поле **Имя пользователя** введите **адрес электронной почты** учетной записи BrittaSimon.

    c. Выберите **Показать пароль** и запишите значение поля **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="creating-a-sansan-test-user"></a>Создание тестового пользователя Sansan

В этом разделе объясняется, как создать пользователя Britta Simon в приложении Sansan. Перед выполнением единого входа пользователь должен быть подготовлен в приложении Sansan. 

>[!NOTE]
>Чтобы создать пользователя или несколько пользователей вручную, обратитесь к [группе поддержки Sansan](https://www.sansan.com/form/contact). 

### <a name="assigning-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Sansan.

![Назначение пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Sansan, выполните следующие действия.**

1. На портале Azure откройте представление приложений, перейдите к представлению каталога, а затем выберите **Корпоративные приложения** и щелкните **Все приложения**.

    ![Назначение пользователя][201] 

2. Из списка приложений выберите **Sansan**.

    ![Настройка единого входа](./media/sansan-tutorial/tutorial_sansan_app.png) 

3. В меню слева выберите **Пользователи и группы**.

    ![Назначение пользователя][202] 

4. Нажмите кнопку **Добавить**. Затем в диалоговом окне **Добавление назначения** выберите **Пользователи и группы**.

    ![Назначение пользователя][203]

5. В диалоговом окне **Пользователи и группы** в списке пользователей выберите **Britta Simon**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. В диалоговом окне **Добавление назначения** нажмите кнопку **Назначить**.
    
### <a name="testing-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув элемент "Sansan" на панели доступа, вы автоматически войдете в приложение Sansan.
Дополнительные сведения о панели доступа см. в статье [Общие сведения о панели доступа](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sansan-tutorial/tutorial_general_01.png
[2]: ./media/sansan-tutorial/tutorial_general_02.png
[3]: ./media/sansan-tutorial/tutorial_general_03.png
[4]: ./media/sansan-tutorial/tutorial_general_04.png

[100]: ./media/sansan-tutorial/tutorial_general_100.png

[200]: ./media/sansan-tutorial/tutorial_general_200.png
[201]: ./media/sansan-tutorial/tutorial_general_201.png
[202]: ./media/sansan-tutorial/tutorial_general_202.png
[203]: ./media/sansan-tutorial/tutorial_general_203.png

