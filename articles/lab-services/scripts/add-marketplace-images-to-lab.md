---
title: Сценарий PowerShell. Добавление образа marketplace в лабораторию в Azure DevTest Labs | Документация Майкрософт
description: В этом сценарии PowerShell показано добавление образа marketplace в лабораторию в Azure DevTest Labs.
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
ms.openlocfilehash: a3a007bf19a28e6f361837856f83a191a761ef9b
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247420"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Добавление образа marketplace в лабораторию в Azure DevTest Labs с помощью PowerShell

В этом примере сценария PowerShell показано добавление образа marketplace в лабораторию в Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Предварительные требования
* **Лаборатория.** Этот сценарий требует наличия лаборатории. 

## <a name="sample-script"></a>Пример скрипта

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Описание скрипта

Этот сценарий использует следующие команды: 

| Get-Help | Примечания |
|---|---|
| [Find-AzureRmResource](/powershell/module/azurerm.resources/find-azurermresource) | Выполняет поиск ресурсов на основе указанных параметров. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Получает ресурсы. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Изменяет ресурс. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Создает ресурса. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](https://docs.microsoft.com/powershell/).

Дополнительные примеры сценариев PowerShell для службы "Службы лабораторий Azure" приведены [здесь](../samples-powershell.md).
