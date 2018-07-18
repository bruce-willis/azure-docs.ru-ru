---
title: Руководство. Создание пользовательских ролей с помощью Azure PowerShell | Документация Майкрософт
description: Создание пользовательских ролей с помощью Azure PowerShell.
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
ms.date: 06/12/2018
ms.author: rolyon
ms.openlocfilehash: 2b3bd55fd2ed9f392611fcb32b0ebd0f5c6b5e09
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36322531"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Руководство. Создание пользовательских ролей с помощью Azure PowerShell

Если встроенные роли не соответствуют потребностям вашей организации, вы можете создать собственные [настраиваемые роли](built-in-roles.md). С помощью этого руководства и Azure PowerShell вы создадите настраиваемую роль с именем "Запросы в службу поддержки от читателя". Она позволяет пользователю просматривать все объекты в подписке, а также открывать запросы в службу поддержки.

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
> * Создание настраиваемой роли
> * Вывод списка настраиваемых ролей
> * Обновление пользовательской роли
> * Удаление настраиваемой роли

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>предварительным требованиям

Для работы с этим учебником требуется:

- разрешения на создание пользовательских ролей, такие как [Владелец](built-in-roles.md#owner) или [Администратор доступа пользователя](built-in-roles.md#user-access-administrator);
- Локальная установка [Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="sign-in-to-azure-powershell"></a>Вход в Azure PowerShell

Войдите в [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Создать пользовательскую роль проще всего с помощью встроенной роли, в которую вы можете добавлять изменения для создания новой роли.

1. В PowerShell вы можете получить список операций, доступных поставщику ресурсов с помощью команды [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation). Полезно знать операции, которые доступны для создания разрешений. См. дополнительные сведения о [доступных операциях поставщиков ресурсов Azure Resource Manager](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Используйте команду [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition), чтобы отобразить роль [Читатель](built-in-roles.md#reader) в формате JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Откройте в редакторе файл **ReaderSupportRole.json**.

    Ниже приведены выходные данные JSON. См. дополнительные сведения о [различных свойствах пользовательских ролей](custom-roles.md).

    ```json
    {
        "Name":  "Reader",
        "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "IsCustom":  false,
        "Description":  "Lets you view everything, but not make any changes.",
        "Actions":  [
                        "*/read"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. Измените файл JSON, добавив операцию `"Microsoft.Support/*"` в свойство `Actions`. Не забудьте включить запятую после операции чтения. Это действие разрешит пользователю создавать запросы в службу поддержки.

1. Получите идентификатор подписки с помощью команды [Get-AzureRmSubscription](/powershell/module/azurerm.resources/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. В `AssignableScopes` добавьте свой идентификатор подписки в следующем формате: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Вам нужно добавить явные идентификаторы подписки, иначе вы не сможете импортировать роль в подписку.

1. Удалите строку свойства `Id` и измените свойство `IsCustom` на `true`.

1. Измените свойства `Name` и `Description` на "Запросы в службу поддержки от читателя" и "Просмотр всех объектов в подписке и создание запросов в службу поддержки".

    Файл JSON должен выглядеть примерно так:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. Чтобы создать пользовательскую роль, используйте команду [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) и укажите файл определения роли JSON.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Новая пользовательская роль станет доступной на портале Azure. Ее можно назначать пользователям, группам или субъектам-службам, как и встроенные роли.

## <a name="list-custom-roles"></a>Вывод списка настраиваемых ролей

- Получить список всех пользовательских ролей можно с помощью команды [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Пользовательскую роль также можно просмотреть на портале Azure.

    ![Снимок экрана пользовательской роли, импортированной на портал Azure](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Обновление пользовательской роли

Чтобы обновить пользовательскую роль, обновите файл JSON или используйте объект `PSRoleDefinition`.

1. Используйте команду [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition), чтобы обновить файл JSON и отобразить пользовательскую роль в формате JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Откройте файл в редакторе.

1. В `Actions` добавьте операцию для создания и администрирования развертываний группы ресурсов `"Microsoft.Resources/deployments/*"`.

    Обновленный файл JSON должен выглядеть примерно так:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. Чтобы обновить пользовательскую роль, используйте команду [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) и укажите обновленный файл JSON.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Чтобы с помощью объекта `PSRoleDefintion` обновить пользовательскую роль, сначала получите эту роль, используя команду [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Вызовите метод `Add`, чтобы добавить операцию для чтения параметров диагностики.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Обновите роль с помощью команды [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition).

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли

1. Получите идентификатор пользовательской роли с помощью команды [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Укажите идентификатор пользовательской роли, чтобы удалить пользовательскую роль, с помощью команды [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition).

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. В запросе на подтверждение выберите **Да**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание пользовательских ролей с помощью PowerShell](custom-roles-powershell.md)