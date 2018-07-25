---
title: Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот | Документация Майкрософт
description: Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот с помощью интерфейса командной строки Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 5c06de6b757da63bb35a4f094e5912e8dcd786f9
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005056"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Преобразование хранилища управляемых дисков Azure с уровня "Стандартный" до уровня "Премиум" и наоборот

В службе "Управляемые диски" доступны три варианта хранилища: [диск SSD ценовой категории "Премиум"](../windows/premium-storage.md), диск SSD ценовой категории "Стандартный" (предварительная версия) и [диск HDD ценовой категории "Стандартный"](../windows/standard-storage.md). Это позволяет с легкостью переключаться между вариантами в зависимости от потребностей производительности с минимальным временем простоя. Эта возможность недоступна для неуправляемых дисков. Но вы легко можете выполнить [преобразование в управляемые диски](convert-unmanaged-to-managed-disks.md), чтобы переключаться между типами дисков.

В этой статье рассматривается преобразование хранилища управляемых дисков с уровня "Стандартный" до уровня "Премиум" и наоборот с помощью интерфейса командной строки Azure. Если вам установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](/cli/azure/install-azure-cli.md). 

## <a name="before-you-begin"></a>Перед началом работы

* Процесс преобразования требует перезагрузки виртуальной машины, поэтому запланируйте перенос хранилища дисков на предварительно установленный период обслуживания. 
* При использовании неуправляемых дисков сначала [преобразуйте их в управляемые диски](convert-unmanaged-to-managed-disks.md), чтобы воспользоваться приведенными в этой статье сведениями о переключении между вариантами хранилища. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Преобразование всех управляемых дисков на виртуальной машине с уровня "Стандартный" до уровня "Премиум" и наоборот

В приведенном ниже примере показано, как переключить все диски на виртуальной машине с хранилища ценовой категории "Стандартный" на хранилище ценовой категории "Премиум". Чтобы использовать управляемые диски уровня "Премиум", необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище уровня "Премиум". В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Преобразование управляемого диска с уровня "Стандартный" до уровня "Премиум" и наоборот

Для среды разработки и тестирования может потребоваться сочетание дисков уровней "Стандартный" и "Премиум", чтобы сократить затраты. Для этого переведите на уровень "Премиум" только диски, для которых требуется более высокая производительность. В приведенном ниже примере показано, как переключить один диск виртуальной машины с хранилища ценовой категории "Стандартный" на хранилище ценовой категории "Премиум" и наоборот. Чтобы использовать управляемые диски уровня "Премиум", необходимо использовать [размер виртуальной машины](sizes.md), поддерживающий хранилище уровня "Премиум". В этом примере также выполняется переключение на размер, поддерживающий хранилище уровня "Премиум".

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Преобразование управляемого диска из HHD ценовой категории "Стандартный" в SSD ценовой категории "Стандартный" и наоборот

В приведенном ниже примере показано, как переключить один диск виртуальной машины с HHD ценовой категории "Стандартный" на SSD ценовой категории "Стандартный" и наоборот.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-using-the-azure-portal"></a>Преобразование с помощью портала Azure

Неуправляемые диски также можно преобразовать в управляемые с помощью портала Azure.

1. Войдите на [портале Azure](https://portal.azure.com).
2. Выберите виртуальную машину из списка виртуальных машин на портале.
3. В колонке виртуальной машины в меню выберите пункт **Диски**.
4. В верхней части колонки **Диски** выберите **Перемещение на управляемые диски**.
5. Если виртуальная машина входит в группу доступности, в колонке **Перемещение на управляемые диски** появится предупреждение о том, что сначала необходимо преобразовать группу доступности. В предупреждении должна быть указана ссылка, которую можно щелкнуть, чтобы преобразовать группу доступности. Когда группа доступности будет преобразована (или если виртуальная машина не входит в группу доступности), щелкните **Миграция**, чтобы начать перенос дисков в управляемые диски. 

Виртуальная машина будет остановлена и перезапущена после завершения миграции.

## <a name="next-steps"></a>Дополнительная информация

Создайте копию виртуальной машины, доступную только для чтения, с помощью [моментальных снимков](snapshot-copy-managed-disk.md).

