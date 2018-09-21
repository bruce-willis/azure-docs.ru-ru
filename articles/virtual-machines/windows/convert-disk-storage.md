---
title: Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот | Документация Майкрософт
description: Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот с помощью Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankum
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 6cfbc458a4bd751b8b871501d19db641e3980767
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44719330"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот

В службе "Управляемые диски" доступны три варианта хранилища: [диск SSD ценовой категории "Премиум"](../windows/premium-storage.md), диск SSD ценовой категории "Стандартный" (предварительная версия) и [диск HDD ценовой категории "Стандартный"](../windows/standard-storage.md). Это позволяет с легкостью переключаться между вариантами в зависимости от потребностей производительности с минимальным временем простоя. Эта возможность недоступна для неуправляемых дисков. Но вы легко можете выполнить [преобразование в управляемые диски](convert-unmanaged-to-managed-disks.md), чтобы переключаться между типами дисков.

В этой статье рассматривается преобразование хранилища управляемых дисков с уровня "Стандартный" до уровня "Премиум" и наоборот с помощью Azure PowerShell. Если вам необходимо установить или обновить Azure PowerShell, ознакомьтесь со статьей [об установке и настройке Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.8.1).

## <a name="before-you-begin"></a>Перед началом работы

* Процесс преобразования требует перезагрузки виртуальной машины, поэтому запланируйте перенос хранилища дисков на предварительно установленный период обслуживания. 
* При использовании неуправляемых дисков сначала [преобразуйте их в управляемые диски](convert-unmanaged-to-managed-disks.md), чтобы воспользоваться приведенными в этой статье сведениями о переключении между вариантами хранилища. 
* Для работы с этой статьей требуется модуль Azure PowerShell 6.0.0 или более поздней версии. Чтобы узнать версию, выполните команду ` Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Кроме того, нужно выполнить команду `Connect-AzureRmAccount`, чтобы создать подключение к Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Преобразование всех управляемых дисков на виртуальной машине с уровня "Стандартный" до уровня "Премиум" и наоборот

В приведенном ниже примере показано, как переключить все диски на виртуальной машине с хранилища ценовой категории "Стандартный" на хранилище ценовой категории "Премиум". Чтобы использовать управляемые диски уровня "Премиум", необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище уровня "Премиум". В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Преобразование управляемого диска с уровня "Стандартный" до уровня "Премиум" и наоборот

Для среды разработки и тестирования может потребоваться сочетание дисков уровней "Стандартный" и "Премиум", чтобы сократить затраты. Для этого переведите на уровень "Премиум" только диски, для которых требуется более высокая производительность. В приведенном ниже примере показано, как переключить один диск виртуальной машины с хранилища ценовой категории "Стандартный" на хранилище ценовой категории "Премиум" и наоборот. Чтобы использовать управляемые диски уровня "Премиум", необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище уровня "Премиум". В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Преобразование управляемого диска из HHD ценовой категории "Стандартный" в SSD ценовой категории "Стандартный" и наоборот

В следующем примере показано, как переключить единственный диск на виртуальной машине с уровня "Стандартный" HHD до уровня "Стандартный" SSD и наоборот.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Дополнительная информация

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).

