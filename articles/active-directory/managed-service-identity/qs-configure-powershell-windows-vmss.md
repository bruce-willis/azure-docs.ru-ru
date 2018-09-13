---
title: Как настроить управляемые удостоверения для ресурсов Azure в масштабируемом наборе виртуальных машин с помощью PowerShell
description: Пошаговые инструкции по настройке управляемых удостоверений, назначаемых системой и назначаемых пользователем, в масштабируемом наборе виртуальных машин с помощью PowerShell.
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
ms.openlocfilehash: c34e858f3dc798310778877ce282fbed2788c7ea
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338039"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Настройка управляемых удостоверений для ресурсов Azure в масштабируемых наборах виртуальных машин с помощью PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как с помощью PowerShell выполнять операции с управляемыми удостоверениями для ресурсов Azure в масштабируемом наборе виртуальных машин.
- Включение и отключение управляемого удостоверения, назначаемого системой, в масштабируемом наборе виртуальных машин
- Добавление и удаление управляемого удостоверения, назначаемого пользователем, в масштабируемом наборе виртуальных машин

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания масштабируемого набора виртуальных машин, а также для включения и удаления управляемого удостоверения, назначаемого системой, и (или) управляемого удостоверения, назначаемого пользователем, из масштабируемого набора виртуальных машин.
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor): для создания управляемого удостоверения, назначаемого пользователем.
    - Роль [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator): для назначения и удаления управляемого удостоверения, назначаемого пользователем, в масштабируемом наборе виртуальных машин.
- Установите [последнюю версию Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), если это еще не сделано. 

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое системой

В этом разделе вы узнаете, как включить и удалить управляемое удостоверение, назначаемое системой, с помощью Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Включение управляемого удостоверения, назначаемого системой, во время создания масштабируемого набора виртуальных машин Azure

Чтобы создать масштабируемый набор виртуальных машин Azure с включенным управляемым удостоверением, назначаемым системой, сделайте следующее.

1. Инструкции по созданию масштабируемого набора виртуальных машин с управляемым удостоверением, назначаемым системой, приведены в *примере 1* в справочной статье по командлету [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig).  Добавьте параметр `-IdentityType SystemAssigned` в командлет `New-AzureRmVmssConfig`:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Необязательно.) Добавьте расширение масштабируемого набора виртуальных машин для управляемых удостоверений для ресурсов Azure, используя параметры `-Name` и `-Type` в командлете [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Вы можете передать значение ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

    > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Включение управляемого удостоверения, назначаемого системой, в существующем масштабируемом наборе виртуальных машин Azure

Если нужно включить управляемое удостоверение, назначаемое системой, в существующем масштабируемом наборе виртуальных машин Azure, сделайте следующее.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в масштабируемом наборе виртуальных машин, например "Участник виртуальных машин".

   ```powershell
   Login-AzureRmAccount
   ```

2. Сначала получите свойства масштабируемого набора виртуальных машин с помощью командлета [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Затем, чтобы включить управляемое удостоверение, назначаемое системой, используйте параметр `-IdentityType` в командлете [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss).

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Добавьте расширение масштабируемого набора виртуальных машин для управляемых удостоверений для ресурсов Azure, используя параметры `-Name` и `-Type` в командлете [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Вы можете передать значение ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение управляемого удостоверения, назначаемого системой, в масштабируемом наборе виртуальных машин Azure

Если в масштабируемом наборе виртуальных машин не требуется управляемое удостоверение, назначаемое системой, но по-прежнему требуются управляемые удостоверения, назначаемые пользователем, используйте следующий командлет.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в масштабируемом наборе виртуальных машин, например "Участник виртуальных машин".

2. Выполните следующий командлет:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Если имеется масштабируемый набор виртуальных машин, для которого не требуется управляемое удостоверение, назначаемое системой, и у которого нет управляемых удостоверений, назначаемых пользователем, используйте следующие команды.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое пользователем

В этом разделе вы узнаете, как добавить и удалить управляемое удостоверение, назначаемое пользователем, в масштабируемом наборе виртуальных машин с помощью Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Задание управляемого удостоверения, назначаемого пользователем, во время создания масштабируемого набора виртуальных машин Azure

Сейчас не поддерживается создание масштабируемого набора виртуальных машин с управляемым удостоверением, назначаемым пользователем, с помощью PowerShell. Сведения о добавлении управляемого удостоверения, назначаемого пользователем, в существующий масштабируемый набор виртуальных машин приведены в следующем разделе. Загляните сюда позже, чтобы проверить наличие новой информации.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Задание управляемого удостоверения, назначаемого пользователем, для существующего масштабируемого набора виртуальных машин Azure

Чтобы задать управляемое удостоверение, назначаемое пользователем, для существующего масштабируемого набора виртуальных машин Azure, выполните следующее.

1. Войдите в Azure, используя команду `Connect-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в масштабируемом наборе виртуальных машин, например "Участник виртуальных машин".

   ```powershell
   Connect-AzureRmAccount
   ```

2. Сначала получите свойства масштабируемого набора виртуальных машин с помощью командлета `Get-AzureRmVM`. Затем, чтобы задать управляемое удостоверение, назначаемое пользователем, для масштабируемого набора виртуальных машин, используйте параметры `-IdentityType` и `-IdentityID` в командлете [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Замените `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` собственными значениями.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Удаление управляемого удостоверения, назначаемого пользователем, из масштабируемого набора виртуальных машин Azure

Если у масштабируемого набора виртуальных машин несколько управляемых удостоверений, назначаемых пользователем, с помощью приведенных ниже команд можно удалить все удостоверения, кроме последнего. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<USER ASSIGNED IDENTITY NAME>` — это имя управляемого удостоверения, назначаемого пользователем, которое следует оставить в масштабируемом наборе виртуальных машин. Эти сведения можно получить в разделе удостоверений масштабируемого набора виртуальных машин с помощью команды `az vmss show`.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Если в масштабируемом наборе виртуальных машин нет управляемого удостоверения, назначаемого системой, и вы хотите удалить из него все управляемые удостоверения, назначаемые пользователем, используйте следующую команду.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Если в масштабируемом наборе виртуальных машин есть управляемые удостоверения, как назначаемое системой, так и назначаемые пользователем, вы можете удалить все управляемые удостоверения, назначаемые пользователем, переключившись на использование только управляемого удостоверения, назначаемого системой.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Дополнительная информация

- [Обзор управляемых удостоверений для ресурсов Azure](overview.md).
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure:
  
  - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















