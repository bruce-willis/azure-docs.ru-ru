---
title: Управление пользователями в Azure Blockchain Workbench
description: Как управлять пользователями в Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: adb4b57ce304a3c6e369d6cc68dbb7c5857fb9fe
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48241581"
---
# <a name="manage-users-in-azure-blockchain-workbench"></a>Управление пользователями в Azure Blockchain Workbench

Azure Blockchain Workbench позволяет управлять пользователями как для отдельных сотрудников, так и для организаций, которые являются частью консорциума.

## <a name="prerequisites"></a>Предварительные требования

Необходимо развертывание Blockchain Workbench. Дополнительные сведения о развертывании Azure Blockchain Workbench см. в [этой статье](deploy.md).

## <a name="add-azure-ad-users"></a>Добавление пользователей Azure AD

Azure Blockchain Workbench использует Azure Active Directory (Azure AD) для проверки подлинности, управления доступом и ролями. Пользователи в клиенте Azure AD могут проверять подлинность и использовать Blockchain Workbench. Добавьте пользователей в роль приложения "Администратор", чтобы взаимодействовать и выполнять действия.

Для приложений и ролей можно назначать только тех пользователей Blockchain Workbench, которые уже существуют в клиенте Azure AD. Чтобы добавить пользователей в Azure AD, выполните следующие действия:

1.  Войдите на [портале Azure](https://portal.azure.com).
2.  В правом верхнем углу выберите свою учетную запись, а затем перейдите в клиент Azure AD, связанный с Blockchain Workbench.
3.  Выберите **Azure Active Directory > Пользователи**. Просмотрите список пользователей в каталоге.
4.  Чтобы добавить пользователей в каталог, выберите **Новый пользователь**. Для внешних пользователей выберите **Новый гостевой пользователь**.

    ![Новый пользователь](./media/manage-users/add-ad-user.png)

5.  Заполните обязательные поля для нового пользователя. Нажмите кнопку **Создать**.

Дополнительные сведения о том, как управлять пользователями в Azure AD, см. в документации по [Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="manage-blockchain-workbench-administrators"></a>Управление администраторами Blockchain Workbench

После добавления пользователей в каталог следующим шагом является выбор пользователей, которые будут администраторами Blockchain Workbench. Пользователи в группе **Администратор** связаны с **ролью приложения "Администратор"** в Blockchain Workbench. Администраторы могут добавлять и удалять пользователей, а также назначать их для определенных сценариев и создавать приложения.

Чтобы добавить пользователей в группу **Администратор** в каталоге Azure AD, сделайте следующее:

1.  Войдите на [портале Azure](https://portal.azure.com).
2.  В правом верхнем углу выберите свою учетную запись, а затем перейдите в клиент Azure AD, связанный с Blockchain Workbench.
3.  Выберите **Azure Active Directory > Корпоративные приложения**.
4.  Выберите клиентское приложение Azure AD для Blockchain Workbench.
    
    ![Все регистрации корпоративных приложений](./media/manage-users/select-blockchain-client-app.png)

5.  Выберите **Пользователи и группы > Добавить пользователя**.
6.  В колонке **Добавление назначения** выберите **Пользователи**. Выберите или найдите пользователя, которого необходимо добавить в качестве администратора. Когда будете готовы, нажмите кнопку **Выбрать**.

    ![Добавление назначения](./media/manage-users/add-user-assignment.png)

9.  Проверьте, установлено ли для **роли** значение **Администратор**.
10. Выберите **Назначить**. Добавленные пользователи отображаются в списке с присвоенной ролью администратора.

    ![Пользователи клиентского приложения Blockchain](./media/manage-users/blockchain-admin-list.png)

## <a name="managing-blockchain-workbench-members"></a>Управление клиентами Blockchain Workbench

Используйте приложение Blockchain Workbench для управления пользователями и организациями, которые являются частью консорциума. Вы можете добавить пользователей в роли и приложения или удалить их.

1. [Откройте Blockchain Workbench](deploy.md#blockchain-workbench-web-url) в браузере и войдите в качестве администратора.

    ![Blockchain Workbench](./media/manage-users/blockchain-workbench-applications.png)

    Участники добавляются в каждое приложение. Участники могут иметь одну или несколько ролей приложений для запуска контрактов или выполнения действий.

2. Чтобы управлять участниками в приложении, выберите плитку приложения на панели **Приложения**.

    Число участников, связанных с выбранным приложением, отражено на плитке участников.

    ![Выбор приложения](./media/manage-users/blockchain-workbench-select-application.png)


#### <a name="add-member-to-application"></a>Добавление участника в приложение

1. Выберите плитку участника, чтобы отобразить список текущих участников.
2. Выберите **Добавить участников**.

    ![Добавление членов](./media/manage-users/application-add-members.png)

3. Найдите имя пользователя.  В списке будут представлены только пользователи Azure AD, которые есть в клиенте Blockchain Workbench. Если пользователь не найден, вам необходимо [добавить пользователей Azure AD](#add-azure-ad-users).

    ![Добавление членов](./media/manage-users/find-user.png)

4. В раскрывающемся меню выберите **роль**.

    ![Выбор роли участников](./media/manage-users/application-select-role.png)

5. Выберите **Добавить**, чтобы добавить участника со связанной ролью в приложение.

#### <a name="remove-member-from-application"></a>Удаление участника из приложения

1. Выберите плитку участника, чтобы отобразить список текущих участников.
2. Для пользователя, которого хотите удалить, выберите **Удалить** из раскрывающегося списка.

    ![Удаление участника](./media/manage-users/application-remove-member.png)

#### <a name="change-or-add-role"></a>Изменение роли или ее добавление

1. Выберите плитку участника, чтобы отобразить список текущих участников.
2. Щелкните раскрывающийся список и выберите новую роль для пользователя, которого требуется изменить.

    ![Изменение роли](./media/manage-users/application-change-role.png)

## <a name="next-steps"></a>Дополнительная информация

Работая с этой статьей, вы научились управлять пользователями в Azure Blockchain Workbench. Сведения о создании блокчейн-приложения см. в следующей статье.

> [!div class="nextstepaction"]
> [Создание блокчейн-приложения в Azure Blockchain Workbench](create-app.md)
