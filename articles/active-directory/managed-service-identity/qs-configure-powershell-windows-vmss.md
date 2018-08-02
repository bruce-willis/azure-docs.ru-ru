---
title: Как настроить управляемое удостоверение службы в масштабируемом наборе виртуальных машин Azure с помощью PowerShell
description: Пошаговые инструкции по настройке назначенных системой и пользовательских удостоверений в масштабируемом наборе виртуальных машин Azure с помощью PowerShell.
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
ms.openlocfilehash: 5d4539c05d05053ac2ea6cd1c5fadbd161b41173
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257748"
---
# <a name="configure-a-vmss-managed-service-identity-using-powershell"></a>Настройка управляемого удостоверения службы в масштабируемом наборе виртуальных машин с помощью PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять операции с управляемым удостоверением службы в масштабируемом наборе виртуальных машин Azure с помощью PowerShell.
- Включение и отключение назначаемого системой удостоверения в масштабируемом наборе виртуальных машин.
- Добавление и удаление назначаемого пользователем удостоверения в масштабируемом наборе виртуальных машин.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи требуются следующие роли.
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания масштабируемого набора виртуальных машин, а также для включения и удаления назначаемого системой и (или) пользователем управляемого удостоверения из масштабируемого набора виртуальных машин.
    - [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания назначаемого пользователем удостоверения.
    - [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для назначения и удаления назначаемого пользователем удостоверения в масштабируемый набор виртуальных машин и из него.
- Установите [последнюю версию Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM), если это еще не сделано. 

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначенное системой

В этом разделе вы узнаете, как включать и удалять назначенное системой удостоверение с помощью Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Включение назначенного системой удостоверения во время создания масштабируемого набора виртуальных машин Azure

Чтобы создать масштабируемый набор виртуальных машин Azure с включенным удостоверением, назначенным системой, выполните указанные ниже действия.

1. Инструкции по созданию масштабируемого набора виртуальных машин с удостоверением, назначенным системой, см. в *примере 1* в справочной статье по командлету [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig).  Добавьте параметр `-IdentityType SystemAssigned` в командлет `New-AzureRmVmssConfig`:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Необязательно.) Добавьте расширение MSI для VMSS, используя параметры `-Name` и `-Type` в командлете [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Вы можете передать значение ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

    > [!NOTE]
    > Этот шаг необязателен, так как для получения токенов можно также использовать конечную точку службы метаданных экземпляров Azure (IMDS).

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Включение назначенного системой удостоверения в существующем масштабируемом наборе виртуальных машин Azure

Если нужно включить назначаемое системой удостоверение в существующем масштабируемом наборе виртуальных машин Azure, выполните указанные ниже действия.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в масштабируемом наборе виртуальных машин, например "Участник виртуальных машин".

   ```powershell
   Login-AzureRmAccount
   ```

2. Сначала получите свойства масштабируемого набора виртуальных машин с помощью командлета [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Затем, чтобы включить назначаемое системой удостоверение, используйте параметр `-IdentityType` в командлете [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss).

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Добавьте расширение MSI для VMSS, используя параметры `-Name` и `-Type` в командлете [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Вы можете передать значение ManagedIdentityExtensionForWindows или ManagedIdentityExtensionForLinux (в зависимости от типа виртуальной машины) и задать имя с помощью параметра `-Name`. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение назначаемого системой удостоверения в масштабируемом наборе виртуальных машин Azure

Если в масштабируемом наборе виртуальных машин не требуется назначаемое системой удостоверение, но по-прежнему требуются назначаемые пользователем удостоверения, используйте следующий командлет.

1. Войдите в Azure, используя команду `Login-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в масштабируемом наборе виртуальных машин, например "Участник виртуальных машин".

2. Выполните следующий командлет:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Если имеется масштабируемый набор виртуальных машин, для которого не требуется назначаемое системой удостоверение и в котором нет назначаемых пользователем удостоверений, используйте следующие команды.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы узнаете, как добавить и удалить назначаемое пользователем удостоверение в масштабируемом наборе виртуальных машин с помощью Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Установка назначаемого пользователем удостоверения во время создания масштабируемого набора виртуальных машин Azure

Сейчас создание масштабируемого набора виртуальных машин с назначаемым пользователем удостоверением с помощью PowerShell не поддерживается. Сведения о добавлении назначаемого пользователем удостоверения в существующий масштабируемый набор виртуальных машин приведены в следующем разделе. Загляните сюда позже, чтобы проверить наличие новой информации.

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Установка назначаемого пользователем удостоверения для существующего масштабируемого набора виртуальных машин Azure

Чтобы установить назначаемое пользователем удостоверение для существующего масштабируемого набора виртуальных машин Azure, выполните следующее.

1. Войдите в Azure, используя команду `Connect-AzureRmAccount`. Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин. Учетная запись должна принадлежать роли, которая предоставляет разрешения на запись в масштабируемом наборе виртуальных машин, например "Участник виртуальных машин".

   ```powershell
   Connect-AzureRmAccount
   ```

2. Сначала получите свойства масштабируемого набора виртуальных машин с помощью командлета `Get-AzureRmVM`. Затем, чтобы установить назначаемое пользователем удостоверение для масштабируемого набора виртуальных машин, используйте параметры `-IdentityType` и `-IdentityID` в командлете [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Замените `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` собственными значениями.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Удаление назначаемого пользователем управляемого удостоверения из масштабируемого набора виртуальных машин Azure

Если у масштабируемого набора виртуальных машин несколько назначаемых пользователем удостоверений, с помощью приведенных ниже команд можно удалить все удостоверения, кроме последнего. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<MSI NAME>` — это имя назначаемого пользователем удостоверения, которое следует оставить в масштабируемом наборе виртуальных машин. Эти сведения можно получить в разделе удостоверений масштабируемого набора виртуальных машин с помощью команды `az vmss show`.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
Если в масштабируемом наборе виртуальных машин нет назначаемого системой удостоверения и вы хотите удалить из него все назначаемые пользователем удостоверения, используйте следующую команду.

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Если в масштабируемом наборе виртуальных машин есть как назначаемое системой, так и назначаемые пользователем удостоверения, вы можете удалить все назначаемые пользователем удостоверения, переключившись на использование только назначаемого системой удостоверения.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Связанная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md)
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure:
  
  - [Создание виртуальной машины Windows с помощью PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Создание виртуальной машины Linux с помощью PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















