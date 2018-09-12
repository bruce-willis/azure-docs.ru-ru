---
title: Руководство. Настройка Cisco Spark для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: В этой статье описано, как настроить Azure Active Directory для автоматической подготовки и отмены подготовки учетных записей пользователей в Cisco Spark.
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
ms.openlocfilehash: aafbde6907e59be3b0ff1d5807ffe4a7d2fffaa4
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346111"
---
# <a name="tutorial-configure-cisco-spark-for-automatic-user-provisioning"></a>Руководство. Настройка Cisco Spark для автоматической подготовки пользователей


В этом руководстве описаны шаги, которые нужно выполнить в Cisco Spark и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отмены подготовки пользователей.


> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Предварительные требования

В сценарии, описанном в этом руководстве, предполагается, что у вас уже имеется:

*   клиент Azure AD;
*   Клиент Cisco Spark
*   Учетная запись пользователя Cisco Spark с разрешениями администратора


> [!NOTE]
> Интеграция подготовки Azure AD зависит от [веб-службы Cisco Spark](https://developer.webex.com/getting-started.html), которая доступна командам разработчиков в Cisco Spark.

## <a name="adding-cisco-spark-from-the-gallery"></a>Добавление Cisco Spark из коллекции
Перед началом настройки Cisco Spark для автоматической подготовки пользователей с помощью Azure AD необходимо добавить Cisco Spark в список управляемых приложений SaaS из коллекции приложений Azure AD.

**Чтобы добавить Cisco Spark из коллекции приложений Azure AD, выполните следующие действия.**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]

3. Чтобы добавить Cisco Spark, нажмите кнопку **Новое приложение** в верхней части диалогового окна.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Cisco Spark**.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppSearch.png)

5. Чтобы добавить это приложение в список приложений SaaS, выберите **Cisco Spark** на панели результатов и нажмите кнопку **Добавить**.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppSearchResults.png)

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-cisco-spark"></a>Назначение пользователей в Cisco Spark

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей, определите, каким пользователям или группам Azure AD необходим доступ к Cisco Spark. После этого, выполнив следующие действия, этих пользователей можно назначить приложению Cisco Spark.

*   [Назначение корпоративному приложению пользователя или группы](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-spark"></a>Важные советы по назначению пользователей в Cisco Spark

*   Для проверки конфигурации автоматической подготовки пользователей в Cisco Spark, рекомендуется назначить одного пользователя Azure AD. Дополнительные пользователи могут быть назначены позднее.

*   Перед тем, как определить пользователей Cisco Spark, в диалоговом окне им необходимо указать действительную роль для конкретного приложения (если она доступна). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-cisco-spark"></a>Настройка автоматической подготовки пользователей в Cisco Spark

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп Cisco Spark, на основании назначения пользователей в Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-cisco-spark-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей Cisco Spark в Azure AD, выполните следующие действия.


1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. В списке приложений SaaS выберите Cisco Spark.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/Successcenter2.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **Учетные данные администратора** укажите **URL-адрес клиента** и **Секретный токен** учетной записи Cisco Spark.

    *   В поле **URL-адрес клиента** укажите URL-адрес API SCIM Cisco Spark, который соответствует `https://api.ciscospark.com/v1/scim/[Tenant ID]/`, где `[Tenant ID]` — буквенно-цифровая строка, как описанная в шаге 6.

    *   В поле **Секретный токен** введите секретный токен, как описано в шаге 6.

1. Чтобы узнать **Идентификатор клиента** и **Секретный токен** учетной записи Cisco Spark, используйте учетную запись администратора для входа на [веб-сайт для разработчиков Cisco Spark](https://developer.webex.com/). Действия, которые необходимо выполнить после входа в систему.
    * Перейдите на страницу [Начало работы](https://developer.webex.com/getting-started.html)
    * Прокрутите страницу вниз до раздела [Проверка подлинности](https://developer.webex.com/getting-started.html#authentication)
    ![Cisco Spark Authentication Token](./media/cisco-spark-provisioning-tutorial/SecretToken.png) (Токен проверки подлинности Cisco Spark)
    * Буквенно-цифровая строка в этом поле является **Секретным токеном**. Скопируйте ее в буфер обмена
    * Перейдите на страницу [Get My Own Details](https://developer.webex.com/endpoint-people-me-get.html) (Получить сведения о своей учетной записи)
        * Убедитесь, что режим тестирования включен
        * Напечатайте слово "Bearer" (Носитель), затем ПРОБЕЛ, а затем вставьте секретный токен в поле "Проверка подлинности" раздела ![Cisco Spark Authentication Token](./media/cisco-spark-provisioning-tutorial/GetMyDetails.png) (Токен проверки подлинности Cisco Spark)
        * Нажмите кнопку "Выполнить"
    * В тексте ответа справа **Идентификатор клиента** отобразится как "orgId".

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

1. После заполнения полей, указанных в шаге 5, щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к Cisco Spark. Если установить подключение не удалось, убедитесь, что учетная запись Cisco Spark обладает разрешением администратора, и повторите попытку.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/TestConnection.png)

8. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/EmailNotification.png)

9. Выберите команду **Сохранить**.

10. В разделе **Сопоставления** выберите **Синхронизировать пользователей Azure Active Directory с Cisco Spark**.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/UserMapping.png)

11. В разделе **Сопоставление атрибутов** просмотрите атрибуты пользователей, которые были синхронизированы между Azure AD и Cisco Spark. Атрибуты, выбранные в качестве свойств **сопоставления**, используются операциями обновления для сопоставления учетных записей пользователей в Cisco Spark. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/UserMappingAttributes.png)

12. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Чтобы включить службу подготовки Azure AD для Cisco Spark, необходимо изменить значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/ProvisioningStatus.png)

14. Определите пользователей или группы для подготовки в Cisco Spark, выбрав требуемые значения в поле **Область** раздела **Параметры**.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/SyncScope.png)

15. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка Cisco Spark](./media/cisco-spark-provisioning-tutorial/Save.png)


После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. Сведения из раздела **Сведения о синхронизации** можно использовать для отслеживания хода выполнения синхронизации и перехода по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые службой подготовки Azure AD в Cisco Spark.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Ограничения соединителя

* Приложение Cisco Spark компании Cisco сейчас находится на стадии раннего тестирования в условиях эксплуатации (EFT). Для получения дополнительной информации обратитесь в [группу поддержки Cisco](https://www.webex.co.in/support/support-overview.html). 

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-provisioning-tutorial/tutorial_general_03.png
