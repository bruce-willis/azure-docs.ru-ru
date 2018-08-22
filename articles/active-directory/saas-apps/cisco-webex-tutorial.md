---
title: Руководство. Интеграция Azure Active Directory с Cisco Webex | Документация Майкрософт
description: Узнайте, как настроить единый вход между Azure Active Directory и Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005531"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Руководство. Интеграция Azure Active Directory с Cisco Webex

В этом руководстве описано, как интегрировать Cisco Webex с Azure Active Directory (Azure AD).

Интеграция Azure AD с Cisco Webex обеспечивает следующие преимущества:

- с помощью Azure AD вы можете контролировать доступ к Cisco Webex;
- вы можете включить автоматический вход пользователей в Cisco Webex с помощью учетной записи Azure AD;
- Вы можете управлять учетными записями централизованно — через портал Azure.

Дополнительные сведения об интеграции приложений SaaS с Azure AD см в статье [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>предварительным требованиям

Чтобы настроить интеграцию Azure AD с Cisco Webex, вам потребуется:

- подписка Azure AD;
- подписка Cisco Webex с поддержкой единого входа.

> [!NOTE]
> Мы не рекомендуем использовать рабочую среду для проверки действий в этом руководстве.

При проверке действий в этом руководстве соблюдайте следующие рекомендации:

- Не используйте рабочую среду без необходимости.
- Если у вас нет пробной среды Azure AD, вы можете [получить бесплатную пробную версию на один месяц](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Описание сценария
В рамках этого руководства проводится проверка единого входа Azure AD в тестовой среде. Сценарий, описанный в этом учебнике, состоит из двух стандартных блоков.

1. добавление Cisco Webex из коллекции;
2. настройка и проверка единого входа в Azure AD.

## <a name="add-cisco-webex-from-the-gallery"></a>Добавление Cisco Webex из коллекции
Чтобы настроить интеграцию Cisco Webex с Azure AD, необходимо добавить Cisco Webex из коллекции в список управляемых приложений SaaS.

**Чтобы добавить Cisco Webex из коллекции, выполните следующие действия:**

1. На [портале Azure](https://portal.azure.com) в области слева щелкните значок **Azure Active Directory**. 

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к разделу **Корпоративные приложения**. Затем выберите **Все приложения**.

    ![Колонка "Корпоративные приложения"][2]
    
3. Чтобы добавить новое приложение, в верхней части диалогового окна нажмите кнопку **Создать приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cisco Webex**. 

5. Выберите **Cisco Webex** в области результатов. Нажмите кнопку **Добавить**, чтобы добавить приложение.

    ![Cisco Webex в списке результатов](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Настройка и проверка единого входа в Azure AD

В этом разделе описана настройка и проверка единого входа Azure AD в Cisco Webex с использованием тестового пользователя Britta Simon.

Чтобы единый вход работал, Azure AD необходимо знать, какой пользователь в Cisco Webex соответствует пользователю в Azure AD. Другими словами, необходимо установить связь между пользователем Azure AD и соответствующим пользователем в Cisco Webex.

Значение **имени пользователя** в Cisco Webex должно соответствовать **имени пользователя** в Azure AD. Это действие устанавливает связь между двумя пользователями. 

Чтобы настроить и проверить единый вход Azure AD в Cisco Webex, выполните инструкции ниже:

1. [Настройка единого входа Azure AD](#configure-azure-ad-single-sign-on) необходима, чтобы пользователи могли использовать эту функцию.
2. [Создание тестового пользователя Azure AD](#create-an-azure-ad-test-user) требуется для проверки работы единого входа Azure AD от имени пользователя Britta Simon.
3. [Создание тестового пользователя Cisco Webex](#create-a-cisco-webex-test-user) нужно для того, чтобы в Cisco Webex также существовал пользователь Britta Simon, связанный с одноименным пользователем в Azure AD.
4. [Назначение тестового пользователя Azure AD](#assign-the-azure-ad-test-user) необходимо, чтобы разрешить Britta Simon использовать единый вход Azure AD.
5. [Проверьте единый вход](#test-single-sign-on), чтобы убедиться, что конфигурация работает правильно.

### <a name="configure-azure-ad-single-sign-on"></a>Настройка единого входа Azure AD

В этом разделе описано, как включить единый вход Azure AD на портале Azure и настроить его в приложении Cisco Webex.

**Чтобы настроить единый вход Azure AD в Cisco Webex, выполните следующие действия:**

1. На портале Azure на странице интеграции с приложением **Cisco Webex** выберите **Единый вход**.

    ![Ссылка "Настройка единого входа"][4]

2. Чтобы включить функцию единого входа, в диалоговом окне **Единый вход** из раскрывающегося списка **Режим** выберите пункт **Вход на основе SAML**.
 
    ![Диалоговое окно "Единый вход"](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. В другом окне веб-браузера войдите на свой корпоративный веб-сайт Cisco Webex в качестве администратора.

4. Нажмите кнопку **Параметры** в левой части меню.

    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. На странице параметров прокрутите вниз и в разделе **Аутентификация** нажмите кнопку **Изменить**.

    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Выберите **Integrate a 3rd-party identity provider. (Advanced)** (Интеграция стороннего поставщика удостоверений. (Дополнительно)) и перейдите к следующему экрану.

    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. На странице**Export Directory Metadata** (Экспорт метаданных каталога) щелкните **Download Metadata File** (Скачать файл метаданных), чтобы скачать файл метаданных.

    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. На портале Azure в разделе **Домены и URL-адреса приложения Cisco Webex** отправьте скачанный **файл метаданных поставщика службы** и настройте приложение, выполнив следующие действия:

    a. Щелкните **Отправить файл метаданных**.

    ![Сведения о домене и URL-адресах единого входа для приложения Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. Щелкните **значок папки**, выберите файл метаданных и нажмите кнопку **Отправить**.

    ![Сведения о домене и URL-адресах единого входа для приложения Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. После успешной отправки **файла метаданных поставщика службы** значения **идентификатора** и **URL-адреса ответа** автоматически добавятся в текстовое поле раздела **Домены и URL-адреса приложения Cisco Webex**, как показано ниже:

    ![Сведения о домене и URL-адресах единого входа для приложения Cisco Webex](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. В поле **URL-адрес для входа** введите URL-адрес в следующем формате: `https://<SUBDOMAIN>.webex.com/`.
     
    > [!NOTE] 
    > Эти значения приведены в качестве примера. Замените эти значения фактическим URL-адресом входа. Чтобы получить их, обратитесь в [службу поддержки клиентов Cisco Webex](https://www.webex.co.in/support/support-overview.html).

9. Приложение Cisco Webex ожидает утверждений SAML, которые содержат определенные атрибуты. Настройте приведенные ниже атрибуты для этого приложения. Управлять значениями этих атрибутов можно в разделе **Атрибуты пользователя** на странице интеграции приложения. На следующем снимке экрана приведен пример.
    
    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. В разделе **Атрибуты пользователя** диалогового окна **Единый вход** настройте атрибут токена SAML, как показано на рисунке выше, и выполните следующие действия:
    
    |  Имя атрибута  | Значение атрибута |
    | --------------- | -------------------- |    
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   uid    | user.mail |

    a. Щелкните **Добавить атрибут**, чтобы открыть диалоговое окно **Добавление атрибута**.

    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. В текстовом поле **Имя** введите имя атрибута, отображаемое для этой строки.
    
    c. В списке **Значение** выберите значение атрибута, отображаемое для этой строки.
    
    d. Нажмите кнопку **ОК**.

11. В разделе **Сертификат подписи SAML** выберите **XML метаданных** и сохраните файл метаданных на компьютере.

    ![Ссылка для скачивания сертификата](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. Щелкните **Сохранить**.

    ![Кнопка "Сохранить" в окне настройки единого входа](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. На странице администратора сайта компании Cisco Webex используйте параметр обозревателя файлов для поиска и отправки файла метаданных Azure AD. Затем выберите **Require certificate signed by a certificate authority in Metadata (more secure)** (Требовать наличия в метаданных сертификата, подписанного центром сертификации (безопасный способ)) и перейдите к следующему экрану. 

    ![Настройка единого входа](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. Выберите **Test SSO Connection** (Проверить подключение для единого входа) и, когда откроется новая вкладка браузера, пройдите аутентификацию Azure AD, выполнив вход.

15. Вернитесь к на вкладку браузера **Служба Cisco для управления совместной работой в облаке**. Если проверка прошла успешно, выберите **This test was successful. Enable Single Sign-On** (Этот тест прошел успешно. Включить единый вход) и нажмите кнопку **Сохранить**.

### <a name="create-an-azure-ad-test-user"></a>Создание тестового пользователя Azure AD

Цель этого раздела — создать на портале Azure тестового пользователя с именем Britta Simon.

   ![Создание тестового пользователя Azure AD][100]

**Чтобы создать тестового пользователя в Azure AD, выполните следующие действия:**

1. На портале Azure в области слева нажмите кнопку **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. Чтобы открыть список пользователей, перейдите в раздел **Пользователи и группы** и выберите **Все пользователи**.

    ![Ссылки "Пользователи и группы" и "Все пользователи"](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Чтобы открыть диалоговое окно **Пользователь**, в верхней части диалогового окна **Все пользователи** щелкните **Добавить**.

    ![Кнопка "Добавить"](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. В диалоговом окне **Пользователь** сделайте следующее:

    ![Диалоговое окно "Пользователь"](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. В поле **Имя** введите **BrittaSimon**.

    Б. В поле **Имя пользователя** введите адрес электронной почты для пользователя Britta Simon.

    c. Установите флажок **Показать пароль** и запишите значение, которое отображается в поле **Пароль**.

    d. Нажмите кнопку **Создать**.
 
### <a name="create-a-cisco-webex-test-user"></a>Создание тестового пользователя Cisco Webex

Цель этого раздела — создать пользователя с именем Britta Simon в Cisco Webex. Cisco Webex поддерживает JIT-подготовку и автоматическую подготовку пользователей, которая включена по умолчанию. Дополнительные сведения о настройке автоматической подготовки пользователей можно найти [здесь](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial).

### <a name="assign-the-azure-ad-test-user"></a>Назначение тестового пользователя Azure AD

В этом разделе описано, как разрешить пользователю Britta Simon использовать единый вход Azure, предоставив этому пользователю доступ к Cisco Webex.

![Назначение роли пользователя][200] 

**Чтобы назначить пользователя Britta Simon в Cisco Webex, сделайте следующее:**

1. На портале Azure откройте представление "Приложения". Далее в представлении каталога выберите пункт **Корпоративные приложения**.  

2. Выберите **Все приложения**.

    ![Назначение пользователя][201] 

3. Из списка приложений выберите **Cisco Webex**.

    ![Ссылка на Cisco Webex в списке "Приложения"](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. В меню слева выберите **Пользователи и группы**.

    ![Ссылка "Пользователи и группы"][202]

4. Нажмите кнопку **Добавить**. Теперь выберите **Пользователи и группы** в диалоговом окне **Добавление назначения**.

    ![Область "Добавление назначения"][203]

5. В диалоговом окне **Пользователи и группы** выберите **Britta Simon** из списка **Пользователи**.

6. В диалоговом окне **Пользователи и группы** нажмите кнопку **Выбрать**.

7. Нажмите кнопку **Назначить** в диалоговом окне **Добавление назначения**.
    
### <a name="test-single-sign-on"></a>Проверка единого входа

В этом разделе описано, как проверить конфигурацию единого входа Azure AD с помощью панели доступа.

Щелкнув плитку Cisco Webex на панели доступа, вы автоматически войдете в приложение Cisco Webex.

Дополнительные сведения о панели доступа см. в статье [Что такое панель доступа?](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Список учебников по интеграции приложений SaaS с Azure Active Directory](tutorial-list.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

