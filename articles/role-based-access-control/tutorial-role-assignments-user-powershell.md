---
title: Руководство по предоставлению доступа пользователям с помощью RBAC и Azure PowerShell | Документация Майкрософт
description: Используйте контроль доступа на основе ролей (RBAC), чтобы предоставлять пользователям доступ для просмотра любого содержимого в рамках подписки и обеспечивать полное управление в группе ресурсов с помощью Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: cac585b36c3b5969a18c941215b623443850cd4c
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301734"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-azure-powershell"></a>Руководство по предоставлению доступа пользователям с помощью RBAC и Azure PowerShell

[Управление доступом на основе ролей (RBAC)](overview.md) — это способ управления доступом к ресурсам в Azure. В этом руководстве описано, как предоставлять пользователям доступ для просмотра любого содержимого в рамках подписки и обеспечивать полное управление в группе ресурсов с помощью Azure PowerShell.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Предоставление доступа пользователям в разных областях
> * Вывод списка доступа
> * Запрет доступа

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется:

- разрешения на создание пользователей в Azure Active Directory (или наличие существующего пользователя);
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell);

## <a name="role-assignments"></a>Назначения ролей

Чтобы в RBAC предоставить доступ, нужно создать назначение ролей. Назначение ролей состоит из трех элементов: субъект безопасности, определение роли и область действия. Ниже приведены два варианта назначения роли, которые мы выполним в ходе работы с этим руководством.

| Субъект безопасности | Определение роли | Область |
| --- | --- | --- |
| Пользователь<br>(Пользователь из руководства по RBAC) | [Читатель](built-in-roles.md#reader) | Подписка |
| Пользователь<br>(Пользователь из руководства по RBAC)| [Участник](built-in-roles.md#contributor) | Группа ресурсов<br>(группа-ресурсов-из-руководства-по-rbac) |

   ![Назначения ролей для пользователя](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>Создание пользователя

Чтобы назначить роль, нам нужен пользователь, группа или субъект-служба. Если у вас еще нет пользователя, создайте его.

1. В Azure Cloud Shell создайте пароль, соответствующий требованиям к сложности.

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. Создайте пользователя для домена с помощью команды [New-AzureADUser](/powershell/module/azuread/new-azureaduser).

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Используйте группу ресурсов, чтобы показать, как назначить роль в области действия группы ресурсов.

1. Получите список расположений в регионе с помощью команды [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

   ```azurepowershell
   Get-AzureRmLocation | select Location
   ```

1. Выберите расположение недалеко от вас и присвойте его переменной.

   ```azurepowershell
   $location = "westus"
   ```

1. Создайте группу ресурсов с помощью команды [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup).

   ```azurepowershell
   New-AzureRmResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Предоставление доступа

Чтобы предоставить доступ пользователю, используйте команду [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) для назначения роли. Необходимо указать субъект безопасности, определение роли и область действия.

1. Получите идентификатор подписки с помощью команды [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Сохраните область действия подписки в переменной.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Назначьте пользователю роль [Читатель](built-in-roles.md#reader) в области действия подписки.

    ```azurepowershell
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. Назначьте роль [Участник](built-in-roles.md#contributor) в области действия группы ресурсов.

    ```azurepowershell
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>Вывод списка доступа

1. Чтобы проверить доступ для подписки, используйте команду [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment), которая отобразит список назначений ролей.

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    В выходных данных вы увидите, что роль "Читатель" была назначена пользователю из руководства по RBAC в пределах области действия подписки.

1. Чтобы проверить доступ для группы ресурсов, используйте команду [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment), которая отобразит список назначений ролей.

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    В выходных данных вы увидите, что обе роли — и "Читатель", и "Участник" — были назначены пользователю, созданному при работе с руководством по RBAC. Роль "Участник" находится в области действия группы ресурсов из руководства по RBAC, а роль "Читатель" наследуется в области действия подписки.

## <a name="optional-list-access-using-the-azure-portal"></a>(Необязательно.) Вывод списка доступа с помощью портала Azure

1. Чтобы увидеть, как выглядят назначения ролей на портале Azure, просмотрите колонку **Управление доступом (IAM)** для подписки.

    ![Назначение ролей для пользователя в области действия подписки](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. Просмотрите колонку **Управление доступом (IAM)** для группы ресурсов.

    ![Назначения ролей для пользователя в области действия группы ресурсов](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>Запрет доступа

Чтобы лишить прав доступа пользователей, группы и приложения, используйте команду [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) для удаления назначений ролей.

1. Используйте следующую команду, чтобы удалить назначение роли участника для пользователя в области действия группы ресурсов.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Используйте следующую команду, чтобы удалить назначение роли читателя для пользователя в области действия подписки.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы очистить ресурсы, созданные при работе с этим руководством, удалите группу ресурсов и пользователя.

1. Удалите группу ресурсов с помощью команды [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

    ```azurepowershell
    Remove-AzureRmResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. В запросе на подтверждение выберите **Да**. Удаление займет несколько секунд.

1. Удалите пользователя с помощью команды [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser).

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Управление доступом с помощью RBAC и PowerShell](role-assignments-powershell.md)
