---
title: Руководство по настройке Samanage для автоматической подготовки пользователей с помощью Azure Active Directory | Документация Майкрософт
description: Узнайте, как настроить Azure Active Directory для автоматической подготовки и отзыва учетных записей пользователей в Samanage.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2018
ms.author: v-wingf-msft
ms.openlocfilehash: 98a1746804d0d5a9526e264f99d03ed4e8590ad6
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043563"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Руководство по настройке Samanage для автоматической подготовки пользователей

В этом руководстве описаны шаги, которые нужно выполнить в Samanage и Azure Active Directory (Azure AD), чтобы настроить Azure AD для автоматической подготовки и отзыва пользователей и групп в Samanage.

> [!NOTE]
> В этом руководстве рассматривается соединитель, созданный на базе службы подготовки пользователей Azure AD. Подробные сведения о том, что делает эта служба, как она работает, и часто задаваемые вопросы см. в статье [Автоматическая подготовка пользователей и ее отзыв для приложений SaaS в Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Предварительные требования

Сценарий, описанный в этом руководстве, предполагает, что у вас уже имеется:

*   клиент Azure AD;
*   [клиент Samanage](https://www.samanage.com/pricing/) с пакетом Professional;
*   учетная запись пользователя в Samanage с разрешениями администратора.

> [!NOTE]
> Интеграция подготовки Azure AD зависит от [REST API Samanage](https://www.samanage.com/api/). Этот REST API доступен для разработчиков Samanage, использующих учетные записи с пакетом Professional.

## <a name="adding-samanage-from-the-gallery"></a>Добавление Samanage из коллекции.
Перед настройкой Samanage для автоматической подготовки пользователей в Azure AD необходимо добавить Samanage из коллекции приложений Azure AD в список управляемых приложений SaaS.

**Чтобы добавить Samanage из коллекции приложений Azure AD, сделайте следующее:**

1. На **[портале Azure](https://portal.azure.com)** в области навигации слева щелкните значок **Azure Active Directory**.

    ![Кнопка "Azure Active Directory"][1]

2. Перейдите к элементу **Корпоративные приложения** > **Все приложения**.

    ![Раздел "Корпоративные приложения"][2]

3. Чтобы добавить Samanage, в верхней части диалогового окна нажмите кнопку **Новое приложение**.

    ![Кнопка "Новое приложение"][3]

4. В поле поиска введите **Samanage**.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/AppSearch.png)

5. В области результатов выберите **Samanage** и нажмите кнопку **Добавить**, чтобы добавить это приложение в список приложений SaaS.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/AppSearchResults.png)

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-samanage"></a>Назначение пользователей в Samanage

В Azure Active Directory для определения того, какие пользователи должны получать доступ к выбранным приложениям, используется концепция, называемая "назначение". В контексте автоматической подготовки синхронизированы будут только те пользователи и группы, которые были "назначены" приложению в Azure AD.

Перед настройкой и включением автоматической подготовки пользователей нужно решить, какие пользователи или группы в Azure AD должны иметь доступ к Samanage. Когда этот вопрос будет решен, этих пользователей и (или) группы можно будет назначить приложению Samanage, следуя приведенным ниже указаниям.

*   [Назначение корпоративному приложению пользователя или группы](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Важные советы по назначению пользователей в Samanage

*   Рекомендуется назначить одного пользователя Azure AD в Samanage для тестирования конфигурации автоматической подготовки пользователей. Дополнительные пользователи и/или группы можно назначить позднее.

*   При назначении пользователя в Samanage в диалоговом окне назначения необходимо выбрать действительную роль для конкретного приложения (если доступно). Пользователи с ролью **Доступ по умолчанию** исключаются из подготовки.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Настройка автоматической подготовки пользователей в Samanage

В этом разделе описывается, как настроить службу подготовки Azure AD для создания, обновления и отключения пользователей и групп в Samanage на основе их назначений в Azure AD.

> [!TIP]
> Для Samanage также можно включить единый вход на основе SAML. Для этого следуйте инструкциям, приведенным в [руководстве по единому входу в Samanage](samanage-tutorial.md). Единый вход можно настроить независимо от автоматической подготовки пользователей, хотя эти две возможности дополняют друг друга.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Чтобы настроить автоматическую подготовку пользователей в Samanage, сделайте следующее.

1. Войдите на [портал Azure](https://portal.azure.com) и перейдите к **Azure Active Directory > Корпоративные приложения > Все приложения**.

2. Из списка приложений SaaS выберите Samanage.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/AppInstanceSearch.png)

3. Выберите вкладку **Подготовка**.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Для параметра **Режим подготовки к работе** выберите значение **Automatic** (Автоматически).

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. В разделе **Учетные данные администратора** введите **учетные данные** и **пароль администратора** от учетной записи Samanage. Примеры этих значений:

    *   В поле **Имя администратора** укажите имя учетной записи администратора в клиенте Samanage. Пример: admin@contoso.com.

    *   В поле **Пароль администратора** введите пароль учетной записи, соответствующей имени пользователя-администратора.

6. После заполнения полей, указанных на шаге 5, щелкните **Проверить подключение**, чтобы убедиться, что Azure AD может подключиться к Samanage. Если установить подключение не удалось, убедитесь, что у учетной записи Samanage есть разрешения администратора, и повторите попытку.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. В поле **Почтовое уведомление** введите адрес электронной почты пользователя или группы, которые должны получать уведомления об ошибках подготовки, а также установите флажок **Send an email notification when a failure occurs** (Отправить уведомление по электронной почте при сбое).

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Выберите команду **Сохранить**.

9. В разделе **Сопоставления** выберите **Synchronize Azure Active Directory Users to Samanage** (Синхронизировать пользователей Azure Active Directory с Samanage).

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. В разделе **Сопоставления атрибутов** просмотрите пользовательские атрибуты, синхронизированные из Azure AD в Samanage. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Samanage для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Чтобы включить сопоставление групп, в разделе **Сопоставления** выберите **Synchronize Azure Active Directory Groups to Samanage** (Синхронизировать группы Azure Active Directory с Samanage).

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. В столбце **Включено** выберите значение **Да** для синхронизации групп. В разделе **Сопоставления атрибутов** просмотрите атрибуты группы, синхронизированные из Azure AD в Samanage. Атрибуты, выбранные как свойства **Matching**, используются для сопоставления учетных записей пользователей в Samanage для операций обновления. Нажмите кнопку **Сохранить**, чтобы зафиксировать все изменения.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Чтобы настроить фильтры области, ознакомьтесь со следующими инструкциями, предоставленными в [руководстве по фильтрам области](../active-directory-saas-scoping-filters.md).

14. Чтобы включить службу подготовки Azure AD для Samanage, измените значение параметра **Состояние подготовки** на **Включено** в разделе **Параметры**.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Определите пользователей или группы для подготовки в Samanage, выбрав нужные значения в поле **Область** раздела **Параметры**.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Когда будете готовы выполнить подготовку, нажмите кнопку **Сохранить**.

    ![Подготовка Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


После этого начнется начальная синхронизация пользователей и (или) групп, определенных в поле **Область** раздела **Параметры**. Начальная синхронизация занимает больше времени, чем последующие операции синхронизации. Если служба запущена, они выполняются примерно каждые 40 минут. В разделе **Сведения о синхронизации** можно отслеживать ход выполнения и переходить по ссылкам для просмотра отчетов о подготовке, в которых зафиксированы все действия, выполняемые службой подготовки Azure AD с приложением Samanage.

Дополнительные сведения о чтении журналов подготовки Azure AD см. в руководстве по [отчетам об автоматической подготовке учетных записей](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Управление подготовкой учетных записей пользователей для корпоративных приложений](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Что такое доступ к приложениям и единый вход с помощью Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Дополнительная информация

* [Сведения о просмотре журналов и получении отчетов о действиях по подготовке](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
