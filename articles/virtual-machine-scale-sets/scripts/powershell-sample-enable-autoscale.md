---
title: Примеры для Azure PowerShell. Включение автомасштабирования на основе узла | Документы Microsoft
description: Примеры сценариев Azure PowerShell.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: ca484a6f1b2dafa3688efe0e50bb2c35f954a8b6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38697421"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>Автоматическое масштабирование масштабируемых наборов виртуальных машин с помощью PowerShell
С помощью этого скрипта создается масштабируемый набор виртуальных машин под управлением Windows Server 2016. При этом используются метрики на основе узла для автоматического масштабирования в соответствии с изменением нагрузки на ЦП.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Очистка развертывания
Выполните следующую команду, чтобы удалить группу ресурсов, масштабируемый набор и все связанные с ними ресурсы.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Описание скрипта
Чтобы создать развертывание, скрипт использует следующие команды. Для каждого элемента в таблице приведены ссылки на документацию по команде.

| Get-Help | Заметки |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Создание масштабируемого набора виртуальных машин и всех вспомогательных ресурсов, включая виртуальную сеть, подсистему балансировки нагрузки и правила NAT. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Получение информации о масштабируемом наборе виртуальных машин. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Добавление расширения виртуальной машины для настраиваемого скрипта установки базового веб-приложения. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Обновление модели масштабируемого набора виртуальных машин для применения расширения виртуальной машины. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Позволяет получить сведения об общедоступном IP-адресе, который использует подсистема балансировки нагрузки. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о модуле Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

См. дополнительные примеры сценариев PowerShell для [масштабируемого набора виртуальных машин Azure](../powershell-samples.md).