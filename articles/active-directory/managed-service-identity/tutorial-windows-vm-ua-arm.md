---
title: Получение доступа к Azure Resource Manager с помощью назначаемого пользователем управляемого удостоверения на виртуальной машине Windows
description: Из этого руководства вы узнаете, как получить доступ к Azure Resource Manager с помощью назначаемого пользователем управляемого удостоверения на виртуальной машине Windows.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: 8e7a662fa0bcb3236a21b717b56e4740a6790ec8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43343632"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Руководство. Получение доступа к Azure Resource Manager с помощью назначаемого пользователем управляемого удостоверения на виртуальной машине Windows

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этом руководстве описано, как создать назначаемое пользователем удостоверение, назначить его виртуальной машине Windows и затем с помощью этого удостоверения получить доступ к API Azure Resource Manager. Управляемые удостоверения службы автоматически управляются платформой Azure. Они обеспечивают проверку подлинности для служб, поддерживающих проверку подлинности Azure AD, без необходимости внедрения учетных данных в код. 

Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание назначаемого пользователем управляемого удостоверения.
> * Назначение пользовательского удостоверения виртуальной машине Windows.
> * Предоставление назначаемому пользователем удостоверению доступа к группе ресурсов в Azure Resource Manager. 
> * Получение маркера доступа и вызов Azure Resource Manager с помощью назначаемого пользователем удостоверения 
> * Чтение свойств группы ресурсов.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Windows](/azure/virtual-machines/windows/quick-create-portal).

- Для выполнения требуемых операций по созданию ресурсов и управлению ролями учетной записи в этом руководстве нужно предоставить учетной записи разрешения "Владелец" в соответствующей области (подписка или группа ресурсов). Прочитайте раздел [Использование управления доступом на основе ролей для контроля доступа к ресурсам в подписке Azure](/azure/role-based-access-control/role-assignments-portal), если нуждаетесь в помощи с назначением ролей.
- Чтобы установить и использовать PowerShell локально для работы с этим руководством, понадобится модуль Azure PowerShell 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). 
- Если модуль PowerShell запущен локально, необходимо также выполнить следующие действия. 
    - Выполните команду `Login-AzureRmAccount`, чтобы создать подключение к Azure.
    - Установите [PowerShellGet последней версии](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
    - Выполните `Install-Module -Name PowerShellGet -AllowPrerelease`, чтобы получить предварительную версию модуля `PowerShellGet` (может потребоваться `Exit` из текущего сеанса PowerShell после выполнения этой команды для установки модуля `AzureRM.ManagedServiceIdentity`).
    - Выполните команду `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease`, чтобы установить предварительную версию модуля `AzureRM.ManagedServiceIdentity` для выполнения операций назначаемого пользователем удостоверения в рамках этой статьи.

## <a name="create-a-user-assigned-identity"></a>Создание назначаемого пользователем удостоверения

Назначаемое пользователем удостоверение создается как изолированный ресурс Azure. С помощью команды [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity) Azure создает в клиенте Azure AD удостоверение, которое можно назначить одному или нескольким экземплярам службы Azure.

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

Ответ содержит подробные сведения о созданном назначаемом пользователем удостоверении, как показано в следующем примере. Запишите значения `Id` и `ClientId` для назначаемого пользователем удостоверения, так как они будут использоваться на следующих этапах:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Назначение пользовательского удостоверения виртуальной машине Windows

Клиенты могут использовать назначаемое пользователем удостоверение для целого ряда ресурсов Azure. Чтобы назначить пользовательское удостоверение отдельной виртуальной машине, используйте следующие команды. Используйте свойство `Id`, возвращенное на предыдущем шаге, для параметра `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Предоставление назначаемому пользователем удостоверению доступа к группе ресурсов в Azure Resource Manager 

Управляемые удостоверения для ресурсов Azure предоставляют удостоверения. С их помощью в коде можно запрашивать маркеры доступа для аутентификации в API ресурсов с поддержкой аутентификации Azure AD. В этом руководстве код получит доступ к API Azure Resource Manager. 

Чтобы ваш код мог получить доступ к API, необходимо сначала предоставить удостоверению доступ к ресурсу в Azure Resource Manager. В этом случае — к группе ресурсов, в которой содержится виртуальная машина. Измените значение `<SUBSCRIPTION ID>` в соответствии с вашей средой.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

Ответ содержит подробные сведения о созданном назначении роли, подобные следующему примеру.

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Resource Manager с его помощью 

Далее в этом руководстве мы будем работать с виртуальной машиной, которую создали ранее.

1. Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

2. На портале перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и в разделе **Обзор** щелкните **Подключить**.

3. Введите **имя пользователя** и **пароль**, указанные при создании виртуальной машины Windows.

4. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте **PowerShell** в удаленном сеансе.

5. С помощью командлета PowerShell `Invoke-WebRequest` выполните запрос к управляемым удостоверениям для локальной конечной точки ресурсов Azure, чтобы получить маркер доступа к Azure Resource Manager.  Значение `client_id` возвращается при [создании назначаемого пользователем управляемого удостоверения](#create-a-user-assigned-identity).

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Чтение свойств группы ресурсов.

Используйте маркер, полученный на предыдущем шаге, для доступа к Azure Resource Manager и считайте свойства группы ресурсов, доступ к которой вы предоставили назначаемому пользователем удостоверению. Замените <SUBSCRIPTION ID> идентификатором подписки своей среды.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
Ответ содержит сведения об определенной группе ресурсов, подобные следующему примеру:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать назначаемое пользователем удостоверение и подключить его к виртуальной машине Azure, чтобы получить доступ к API Azure Resource Manager.  Сведения об Azure Resource Manager см. здесь:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)