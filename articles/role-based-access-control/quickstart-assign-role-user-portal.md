---
title: Краткое руководство. Предоставление доступа пользователю с помощью RBAC и портала Azure | Документация Майкрософт
description: Используйте управление доступом на основе ролей (RBAC), чтобы предоставить пользователю разрешения путем присвоения роли на портале Azure.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: 74ecca671409b6e163bc0db29d66167d240b645c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092528"
---
# <a name="quickstart-grant-access-for-a-user-using-rbac-and-the-azure-portal"></a>Краткое руководство. Предоставление доступа пользователю с помощью RBAC и портала Azure

Управление доступом на основе ролей (RBAC) — это способ управления доступом к ресурсам в Azure. В этом кратком руководстве пользователь получает доступ к созданию и управлению виртуальными машинами в группе ресурсов.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу http://portal.azure.com.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

1. В списке переходов выберите **Группы ресурсов**.

1. Чтобы открыть колонку **Группа ресурсов**, нажмите **Добавить**.

   ![Добавление новой группы ресурсов](./media/quickstart-assign-role-user-portal/resource-group.png)

1. Как **имя группы ресурсов** введите **rbac-quickstart-resource-group**.

1. Выберите подписку и расположение группы ресурсов.

1. Выберите **Создать**, чтобы создать группу ресурсов.

1. Выберите **Обновить**, чтобы обновить список групп ресурсов.

   Новая группа ресурсов появится в списке групп ресурсов.

   ![Список групп ресурсов](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Предоставление доступа

При использовании RBAC, чтобы предоставить доступ, нужно создать назначение ролей.

1. В списке **Группы ресурсов** выберите новую группу ресурсов **rbac-quickstart-resource-group**.

1. Выберите **Управление доступом (IAM)**, чтобы просмотреть текущий список назначения ролей.

   ![Колонка "Управление доступом (IAM)" для группы ресурсов](./media/quickstart-assign-role-user-portal/access-control.png)

1. Выберите **Добавить**, чтобы открыть панель **Добавление разрешений**.

   Если у вас нет разрешений на назначение ролей, параметр **Добавить** не будет отображаться.

   ![Панель "Добавление разрешений"](./media/quickstart-assign-role-user-portal/add-permissions.png)

1. В раскрывающемся списке **Роль** выберите **Участник виртуальных машин**.

1. В списке **Выбор** выберите себя или другого пользователя.

1. Нажмите кнопку **Сохранить**, чтобы создать назначение роли.

   Через несколько секунд пользователю назначается роль "Участник виртуальной машины" в пределах группы ресурсов rbac-quickstart-resource-group.

   ![Назначение роли "Участник виртуальных машин"](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Запрет доступа

В RBAC, чтобы удалить доступ, нужно удалить назначение роли.

1. В списке назначения ролей добавьте флажок рядом с пользователем с ролью "Участник виртуальной машины".

1. Нажмите кнопку **Удалить**.

   ![Сообщение об удалении назначения роли](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. В появившемся сообщении об удалении назначения роли нажмите кнопку **Да**.

## <a name="clean-up"></a>Очистка

1. В списке переходов выберите **Группы ресурсов**.

1. Чтобы открыть группу ресурсов, выберите **rbac-quickstart-resource-group**.

1. Чтобы удалить группу ресурсов, выберите **Удалить группу ресурсов**.

   ![Удалить группу ресурсов](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. В колонке **Вы действительно хотите удалить** введите имя группы ресурсов: **rbac-quickstart-resource-group**.

1. Чтобы удалить группу ресурсов, выберите **Удалить**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по предоставлению доступа пользователям с помощью RBAC и Azure PowerShell](tutorial-role-assignments-user-powershell.md)

