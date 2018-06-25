---
title: Сценарий PowerShell для задания допустимых размеров виртуальных машин в службе "Службы лабораторий Azure" | Документация Майкрософт
description: Этот сценарий PowerShell задает допустимые размеры виртуальных машин в службе "Службы лабораторий Azure".
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: 159f175e7bb27b2d89001e1eba737c67adb89e50
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638149"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Задание допустимых размеров виртуальных машин в службе "Службы лабораторий Azure" с помощью PowerShell

Этот пример сценария PowerShell задает допустимые размеры виртуальных машин в службе "Службы лабораторий Azure".

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>предварительным требованиям
* **Лаборатория.** Этот сценарий требует наличия лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Заметки |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | Выполняет поиск ресурсов на основе указанных параметров. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Получает ресурсы. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Изменяет ресурс. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Создает ресурса. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).