---
title: Общие команды PowerShell для виртуальных машин Azure | Документация Майкрософт
description: Общие команды PowerShell, позволяющие приступить к созданию виртуальных машин Windows в Azure и управлению ими.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: cb9f03ab87079ba33135840e3e7599d2e8cc95e9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2018
ms.locfileid: "42143849"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Общие команды PowerShell для создания виртуальных машин Azure и управления ими

В этой статье рассматриваются некоторые из команд Azure PowerShell, которые можно использовать для создания виртуальных машин в подписке Azure и управления ими.  Для получения более подробной справки о конкретных параметрах командной строки можно использовать *команду* **Get-Help**.

Сведения об установке последней версии Azure PowerShell, а также о выборе нужной подписки и входе в учетную запись Azure см. в статье [Как установить и настроить службы Azure PowerShell](/powershell/azure/overview).

При выполнении нескольких команд, описываемых в этой статье, могут пригодиться приведенные ниже переменные.

- $location — расположение виртуальной машины. Можно использовать командлет [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation), чтобы найти [географический регион](https://azure.microsoft.com/regions/), который вы используете.
- $myResourceGroup — имя группы ресурсов, содержащей виртуальную машину.
- $myVM — имя виртуальной машины.

## <a name="create-a-vm---simplified"></a>Создание упрощенной виртуальной машины

| Задача | Get-Help |
| ---- | ------- |
| Создание простой виртуальной машины | [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -Name $myVM <BR></BR><BR></BR> Команда New-AzureRMVM имеет набор *упрощенных* параметров, в которых нужно указать только одно имя. Значения параметра -Name будет использоваться как имя для всех ресурсов, необходимых для создания виртуальной машины. Можно указать дополнительные значения, но только это является обязательным.|
| Создание виртуальной машины из пользовательского образа | New-AzureRmVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>У вас уже должен быть собственный [управляемый образ](capture-image-resource.md). Его можно использовать для создания нескольких одинаковых виртуальных машин. |



## <a name="create-a-vm-configuration"></a>Создание конфигурации виртуальной машины

| Задача | Get-Help |
| ---- | ------- |
| Создание конфигурации виртуальной машины |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>Конфигурация виртуальной машины используется для определения или обновления параметров виртуальной машины. Для инициализации конфигурации используется имя виртуальной машины и ее [размер](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Добавление параметров конфигурации |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Параметры операционной системы, в том числе [учетные данные](https://technet.microsoft.com/library/hh849815.aspx), добавляются в объект конфигурации, созданный ранее с помощью командлета New-AzureRmVMConfig. |
| Добавление сетевого интерфейса |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>У виртуальной машины должен быть [сетевой интерфейс](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) для обмена данными в виртуальной сети. Можно также использовать командлет [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) для получения существующего объекта сетевого интерфейса. |
| Указание образа платформы |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "имя_издателя" -Offer "предложение_издателя" -Skus "SKU_продукта" -Version "последняя_версия"<BR></BR><BR></BR>В объект конфигурации, созданный ранее с помощью командлета New-AzureRmVMConfig, добавляется [информация об образе](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Объект, возвращенный этой командой, используется только в том случае, если диск операционной системы настроен для использования образа платформы. |
| Создание виртуальной машины |[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Все ресурсы создаются в [группе ресурсов](../../azure-resource-manager/powershell-azure-resource-manager.md). Прежде чем выполнить эту команду, выполните командлеты New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface и Set-AzureRmVMOSDisk. |
| Обновление виртуальной машины |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Получите текущую конфигурацию виртуальной машины с помощью командлета Get-AzureRmVM, измените параметры конфигурации в объекте виртуальной машины, после чего выполните данную команду. |

## <a name="get-information-about-vms"></a>Получение информации о виртуальных машинах

| Задача | Get-Help |
| ---- | ------- |
| Вывод списка виртуальных машин в подписке |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Вывод списка виртуальных машин в группе ресурсов |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Для получения списка групп ресурсов в подписке используйте командлет [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Получение информации о виртуальной машине |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Управление виртуальными машинами
| Задача | Get-Help |
| --- | --- |
| Запуск виртуальной машины |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Остановка виртуальной машины |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Перезапуск выполняющейся виртуальной машины |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Удаление виртуальной машины |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Дополнительная информация
* Ознакомьтесь с основными шагами по созданию виртуальной машины в разделе [Создание виртуальной машины Windows с помощью Resource Manager и PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

