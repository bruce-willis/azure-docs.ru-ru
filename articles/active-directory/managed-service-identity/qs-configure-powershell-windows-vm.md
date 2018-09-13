---
title: Как настроить управляемые удостоверения для ресурсов Azure на виртуальной машине Azure с помощью PowerShell
description: Пошаговые инструкции по настройке управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 4780e9cc60f44ddd3f974a28404e18e6b765c08b
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43340301"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как с помощью PowerShell выполнять приведенные ниже операции с управляемыми удостоверениями для ресурсов Azure на виртуальной машине Azure.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания виртуальной машины, а также включения и удаления управляемого удостоверения, назначаемого системой и (или) управляемого удостоверения, назначаемого пользователем, для виртуальной машины Azure.
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor): для создания управляемого удостоверения, назначаемого пользователем.
    - Роль [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для назначения и удаления управляемого удостоверения, назначаемого пользователем, для виртуальной машины.
- Установите [последнюю версию Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), если это еще не сделано.

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое системой

В этом разделе вы узнаете, как включить и отключить управляемое удостоверение, назначаемое системой, с помощью Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Включение управляемого удостоверения, назначаемого системой, во время создания виртуальной машины Azure

Чтобы создать виртуальную машину Azure с включенным управляемым удостоверением, назначаемым системой, сделайте следующее.

1. Используя одно из кратких руководств ниже выполните действия только в нужных разделах ("Вход в Azure", "Создание группы ресурсов", "Создание группы сети", "Создание виртуальной машины").
    
    При выполнении действий, описанных в разделе о создании виртуальной машины, не забудьте внести небольшие изменения в синтаксис командлета [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Чтобы подготовить виртуальную машину с включенным удостоверением, назначаемым системой, добавьте параметр `-AssignIdentity:$SystemAssigned`, как показано ниже.
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Необязательно.) Добавьте расширение виртуальной машины для управляемых удостоверений для ресурсов Azure, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Включение управляемого удостоверения, назначаемого системой, для имеющейся виртуальной машины Azure

Если нужно включить управляемое удостоверение, назначаемое системой, на имеющейся виртуальной машине, выполните указанные ниже действия.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину.

   ```powershell
   Login-AzureRmAccount
   ```

2. Сначала извлеките свойства виртуальной машины с помощью командлета `Get-AzureRmVM`. Затем, чтобы включить управляемое удостоверение, назначаемое системой, используйте параметр `-AssignIdentity` в командлете [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Необязательно.) Добавьте расширение виртуальной машины для управляемых удостоверений для ресурсов Azure, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена. Убедитесь, что указан правильный параметр `-Location`, совпадающий с расположением имеющейся виртуальной машины:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Отключение управляемого удостоверения, назначаемого системой, на виртуальной машине Azure

Если на виртуальной машине больше не требуется управляемое удостоверение, назначаемое системой, но по-прежнему требуются управляемые удостоверения, назначаемые пользователем, используйте следующий командлет.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину.

   ```powershell
   Login-AzureRmAccount
   ```

2. Получите свойства виртуальной машины с помощью командлета `Get-AzureRmVM` и задайте для параметра `-IdentityType` значение `UserAssigned`.

   ```powershell   
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM    
   Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Если имеется виртуальная машина, для которой не требуется управляемое удостоверение, назначаемое системой, и у которой нет управляемых удостоверений, назначаемых пользователем, используйте следующие команды.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

Чтобы удалить расширение виртуальной машины для управляемых удостоверений для ресурсов Azure, используйте параметр -Name в командлете [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension), указав то же имя, которое вы использовали при добавлении расширения.

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое пользователем

В этом разделе вы узнаете, как добавить и удалить управляемое удостоверение, назначаемое пользователем, для виртуальной машины с помощью Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Задание управляемого удостоверения, назначаемого пользователем, для виртуальной машины во время ее создания

Чтобы задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины Azure во время ее создания, выполните указанные ниже действия.

1. Используя одно из кратких руководств ниже выполните действия только в нужных разделах ("Вход в Azure", "Создание группы ресурсов", "Создание группы сети", "Создание виртуальной машины"). 
  
    При выполнении действий, описанных в разделе о создании виртуальной машины, не забудьте внести небольшие изменения в синтаксис командлета [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm). Добавьте параметры `-IdentityType UserAssigned` и `-IdentityID `, чтобы подготовить виртуальную машину с удостоверением, назначаемым пользователем.  Замените `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.  Например: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Необязательно.) Добавьте расширение виртуальной машины для управляемых удостоверений для ресурсов Azure, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена. Убедитесь, что указан правильный параметр `-Location`, совпадающий с расположением имеющейся виртуальной машины:
      > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Задание управляемого удостоверения, назначаемого пользователем, для имеющейся виртуальной машины Azure

Чтобы задать управляемое удостоверение, назначаемое пользователем, для имеющейся виртуальной машины Azure, сделайте следующее.

1. Войдите в Azure, используя команду `Connect-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину.

   ```powershell
   Connect-AzureRmAccount
   ```

2. Создайте управляемое удостоверение, назначаемое пользователем, с помощью командлета [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity).  Обратите внимание на свойство `Id` в выходных данных. Оно потребуется в следующем шаге.

   > [!IMPORTANT]
   > При создании управляемых удостоверений, назначаемых пользователем, можно использовать только буквы, цифры и дефис (0–9, a–z, A–Z и -). Кроме того, чтобы назначение виртуальной машине или VMSS производилось правильно, длина имени не должна превышать 24 символа. Загляните сюда позже, чтобы проверить наличие новой информации. Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).

   ```powershell
   New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Извлеките свойства виртуальной машины с помощью командлета `Get-AzureRmVM`. Затем, чтобы задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины Azure, используйте параметры `-IdentityType` и `-IdentityID` в командлете [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).  Для параметра `-IdentityId` следует использовать значение `Id` из предыдущего шага.  Замените `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Добавьте расширение виртуальной машины для управляемых удостоверений для ресурсов Azure, используя параметр `-Type` в командлете [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Вы можете передать значения ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена. Укажите правильный параметр `-Location`, совпадающий с расположением имеющейся виртуальной машины.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Удаление управляемого удостоверения, назначаемого пользователем, из виртуальной машины Azure

Если у виртуальной машины несколько управляемых удостоверений, назначаемых пользователем, с помощью приведенных ниже команд можно удалить все эти удостоверения, кроме последнего. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. `<USER ASSIGNED IDENTITY NAME>` — это имя управляемого удостоверения, назначаемого пользователем, которое следует оставить в виртуальной машине. Эти сведения можно получить в разделе удостоверений виртуальной машины с помощью команды `az vm show`.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Если в виртуальной машине нет управляемого удостоверения, назначаемого системой, и вы хотите удалить из нее все управляемые удостоверения, назначаемые пользователем, используйте следующую команду.

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Если у виртуальной машины есть управляемые удостоверения, назначаемые системой и назначаемые пользователем, вы можете удалить все управляемые удостоверения, назначаемые пользователем, переключившись на использование только управляемого удостоверения, назначаемого системой.

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Дополнительная информация

- [Обзор управляемых удостоверений для ресурсов Azure](overview.md).
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure:
  
  - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
