---
title: Расширение диска операционной системы виртуальной машины Windows в Azure | Документация Майкрософт
description: Увеличение размера диска ОС виртуальной машины с использованием Azure PowerShell в модели развертывания с помощью Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.openlocfilehash: f7ff1f2f27806c92c1ac887ce3c3343b96339745
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888955"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Расширение диска ОС виртуальной машины

При создании новой виртуальной машины в группе ресурсов путем развертывания образа из [Azure Marketplace](https://azure.microsoft.com/marketplace/) диск операционной системы по умолчанию обычно имеет размер 127 ГБ (в некоторых образах диск ОС по умолчанию меньше). Хотя существует возможность добавлять диски данных к виртуальной машине (их число зависит от выбора номеров SKU), при этом даже рекомендуется устанавливать приложения и интенсивные по ЦП рабочие нагрузки на этих дополнительных дисках, зачастую пользователям требуется расширить диск операционной системы для поддержки некоторых сценариев, например следующих:

- Поддержка старых приложений, устанавливающих свои компоненты на диске с ОС.
- Перенос локального физического компьютера или виртуальной машины с диском операционной системы большого размера.


> [!IMPORTANT]
> Если изменить размер диска ОС для виртуальной машины Azure, будет выполнен ее перезапуск.
>
> После расширения диска необходимо [расширить том ОС](#expand-the-volume-within-the-os), чтобы использовать преимущества диска большего размера.
> 

## <a name="resize-a-managed-disk"></a>Изменение размера управляемого диска

Откройте интегрированную среду сценариев Powershell или окно Powershell в режиме администратора и выполните следующие действия.

1. Войдите в учетную запись Microsoft Azure в режиме управления ресурсами и выберите подписку следующим образом.
   
   ```powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Задайте имя группы ресурсов и имя виртуальной машины следующим образом:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Получите ссылку на виртуальную машину следующим образом:
   
   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Остановите виртуальную машину перед изменением размера диска следующим образом:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Получите ссылку на управляемый диск ОС. Задайте нужный размер управляемого диска ОС и обновите диск следующим образом:
   
   ```Powershell
   $disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Новый размер должен быть больше, чем размер существующего диска. Максимальный допустимый размер диска ОС — 2048 ГБ. (Можно развернуть большой двоичный объект VHD сверх этого размера, но операционная система сможет работать только с первыми 2048 ГБ.)
   > 
   > 
6. Обновление виртуальной машины может занять несколько секунд. После завершения команды перезапустите виртуальную машину, как показано ниже:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```

Вот и все! Теперь подключитесь по RDP к виртуальной машине, откройте раздел "Управление компьютером" (или "Управление дисками") и увеличьте диск, используя только что выделенное для этого место.

## <a name="resize-an-unmanaged-disk"></a>Изменение размера неуправляемого диска

Откройте интегрированную среду сценариев Powershell или окно Powershell в режиме администратора и выполните следующие действия.

1. Войдите в учетную запись Microsoft Azure в режиме управления ресурсами и выберите подписку следующим образом.
   
   ```Powershell
   Connect-AzureRmAccount
   Select-AzureRmSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Задайте имя группы ресурсов и имя виртуальной машины следующим образом:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Получите ссылку на виртуальную машину следующим образом:
   
   ```Powershell
   $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Остановите виртуальную машину перед изменением размера диска следующим образом:
   
    ```Powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Задайте нужный размер неуправляемого диска операционной системы и обновите виртуальную машину следующим образом:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Новый размер должен быть больше, чем размер существующего диска. Максимальный допустимый размер диска ОС — 2048 ГБ. (Можно развернуть большой двоичный объект VHD сверх этого размера, но операционная система сможет работать только с первыми 2048 ГБ.)
   > 
   > 
   
6. Обновление виртуальной машины может занять несколько секунд. После завершения команды перезапустите виртуальную машину, как показано ниже:
   
   ```Powershell
   Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Сценарии для диска операционной системы

Ниже приведен полный код сценария для ссылки на управляемые и неуправляемые диски:


**Управляемые диски**

```Powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRMVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzureRmDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

**Неуправляемые диски**

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Изменение размера дисков данных

В этой статье основное внимание уделено расширению диска ОС виртуальной машины. Но разработанный сценарий также можно использовать для увеличения дисков данных, подключенных к виртуальной машине. Например, чтобы увеличить первый диск с данными, подключенный к виртуальной машине, замените объект `OSDisk` в `StorageProfile` на массив `DataDisks` и используйте числовой индекс для получения ссылки на первый подключенный диск с данными, как показано ниже:

**Управляемый диск**

```powershell
$disk= Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Неуправляемый диск**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Аналогичным образом можно ссылаться на другие диски с данными, подключенные к виртуальной машине, либо с помощью индекса, как показано выше, либо с помощью свойства **Имя** диска:


**Управляемый диск**

```powershell
(Get-AzureRmDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Неуправляемый диск**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Расширение тома в ОС

После расширения диска для виртуальной машины необходимо перейти к операционной системе и расширить том с учетом нового пространства. Существует несколько способов расширения раздела. В этом разделе описано подключение виртуальной машины с использованием протокола удаленного рабочего стола для расширения раздела с помощью **DiskPart**.

1. Откройте подключение к виртуальной машине по протоколу удаленного рабочего стола.

2.  Откройте командную строку и введите **diskpart**.

2.  В командной строке **DISKPART** введите `list volume`. Найдите том, который требуется расширить.

3.  В командной строке **DISKPART** введите `select volume <volumenumber>`. При этом будет выбран том с указанным *номером тома*, который вы хотите расширить в смежную пустую область на том же диске.

4.  В командной строке **DISKPART** введите `extend [size=<size>]`. Эта команда расширяет выбранный том на указанный *размер* в МБ.


##<a name="next-steps"></a>Дополнительная информация

Диски также можно подключить на [портале Azure](attach-managed-disk-portal.md).



