---
title: Руководство по настройке Cisco для автоматической подготовки пользователей с помощью Azure Active Directory | Документы Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Cisco Webex.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: v-wingf
ms.openlocfilehash: 76a83ef4f647dcf7d79218cb281f1f976b292870
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42144402"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Руководство по настройке Cisco Webex для автоматической подготовки пользователей


В этом руководстве описаны шаги, которые нужно выполнить в Cisco Webex и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отмены подготовки пользователей в Cisco Webex.


> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

*   клиент Azure AD;
*   Клиент Cisco Webex
*   учетная запись пользователя в Cisco Webex с разрешениями администратора.


> [!NOTE]
> Интеграция подготовки Azure AD зависит от [веб-службы Cisco Webex](https://developer.webex.com/getting-started.html), которая доступна командам Cisco Webex.

## <a name="adding-cisco-webex-from-the-gallery"></a>добавление Cisco Webex из коллекции;
Перед настройкой Cisco Webex для автоматической подготовки пользователей в Azure AD необходимо добавить Cisco Webex из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Cisco Webex из коллекции приложений Azure AD, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]

3. Чтобы добавить Cisco Webex, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cisco Webex**.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppSearch.png)

5. В области результатов выберите **Cisco Webex** и нажмите кнопку **Добавить**, чтобы добавить это приложение в список приложений SaaS.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppSearchResults.png)

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-webex"></a>Назначение пользователей в Cisco Webex

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей нужно решить, какие пользователи или группы в Azure AD должны иметь доступ к Cisco Webex. После этого можно назначить этих пользователей для приложения Cisco Webex, выполнив следующие действия:

*   [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Важные рекомендации по назначению пользователей в Cisco Webex

*   Рекомендуется назначить одного пользователя Azure AD в Cisco Webex для проверки конфигурации автоматической подготовки пользователей. Дополнительные пользователи могут быть назначены позднее.

*   При назначении пользователя в Cisco Webex в диалоговом окне назначения необходимо выбрать действительную роль для конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Настройка автоматической подготовки пользователей в Cisco Webex

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в Cisco Webex на основе их назначений в Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей Cisco Webex в Azure AD, сделайте следующее:


1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. В списке приложений SaaS выберите Cisco Webex.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/Successcenter2.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **Учетные данные администратора** укажите **URL-адрес клиента** и **Маркер секрета** для вашей учетной записи Cisco Webex.

    *   В поле **URL-адрес клиента** укажите URL-адрес API SCIM Cisco Webex, который имеет вид `https://api.webex.com/v1/scim/[Tenant ID]/`, где `[Tenant ID]` — буквенно-цифровая строка, как описано в шаге 6.

    *   В поле **Маркер секрета** укажите маркер секрета, как описано в шаге 6.

1. Чтобы узнать **Идентификатор клиента** и **Маркер секрета** для вашей учетной записи Cisco Webex, войдите на [сайт разработчика Cisco Webex](https://developer.webex.com/) с учетной записью администратора. После входа в систему
    * Перейдите на страницу [Приступая к работе](https://developer.webex.com/getting-started.html)
    * Прокрутите содержимое страницы вниз до раздела [Проверка подлинности](https://developer.webex.com/getting-started.html#authentication)
    ![Маркер проверки подлинности Cisco Webex](./media/cisco-webex-provisioning-tutorial/SecretToken.png)
    * Буквенно-цифровая строка в этом поле и представляет собой **Маркер секрета**. Скопируйте ее в буфер обмена
    * Перейдите на страницу [Get My Own Details](https://developer.webex.com/endpoint-people-me-get.html) (Получить сведения о своей учетной записи)
        * Убедитесь, что включен режим тестирования
        * Напечатайте слово "Bearer", пробел и затем вставьте маркер секрета в поле "Проверка подлинности" ![маркера проверки подлинности Cisco Webex](./media/cisco-webex-provisioning-tutorial/GetMyDetails.png)
        * Нажмите кнопку "Выполнить"
    * В тексте ответа справа **Идентификатор клиента** указан в качестве значения параметра "orgId":

    ```json
    {
        "id": "(...)",
        "emails": [
            "admin.user@contoso.com"
        ],
        "displayName": "John Smith",
        "nickName": "John",
        "orgId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
        (...)
    }
    ```

1. После заполнения полей, указанных в шаге 5, нажмите кнопку **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к Cisco Webex. Если установить подключение не удалось, убедитесь, что у учетной записи Cisco Webex есть разрешения администратора, и повторите попытку.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/TestConnection.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/EmailNotification.png)

9. Выберите команду **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Cisco Webex**.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/UserMapping.png)

11. В разделе **Сопоставление атрибутов** просмотрите пользовательские атрибуты, которые синхронизированы с Cisco Webex из Azure AD. Атрибуты, выбранные как свойства **сопоставления**, используются для сопоставления учетных записей пользователей в Cisco Webex для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/UserMappingAttributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../active-directory-saas-scoping-filters.md).

13. Чтобы включить службу подготовки Azure AD для Cisco Webex, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/ProvisioningStatus.png)

14. Определите пользователей или группы для подготовки в Cisco Webex, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/SyncScope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка Cisco Webex](./media/cisco-webex-provisioning-tutorial/Save.png)


После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения синхронизации и перейти по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые службой подготовки Azure AD с приложением Cisco Webex.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../active-directory-saas-provisioning-reporting.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Приложение Cisco Webex компании Cisco сейчас находится на стадии раннего тестирования (EFT). Для получения дополнительной информации обратитесь в [группу поддержки Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-provisioning-tutorial/tutorial_general_03.png
