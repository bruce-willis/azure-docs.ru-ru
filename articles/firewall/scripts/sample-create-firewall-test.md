---
title: Пример сценария Azure PowerShell по созданию тестовой среды брандмауэра Azure
description: Пример сценария Azure PowerShell по созданию тестовой среды брандмауэра Azure.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: daf847ea9a6b6310afbc8eca7f662065fe75ade6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397188"
---
# <a name="create-an-azure-firewall-test-environment"></a>Создание тестовой среды брандмауэра Azure

[!INCLUDE [firewall-preview-notice](../../../includes/firewall-preview-notice.md)]

Примеры в статьях о брандмауэре Azure предполагают, что общедоступная предварительная версия брандмауэра Azure уже активирована. Дополнительные сведения см. в статье [Enable the Azure Firewall public preview](../public-preview.md) (Включение общедоступной предварительной версии брандмауэра Azure).

Этот сценарий создает брандмауэр и тестовую сетевую среду. Сеть содержит одну виртуальную сеть с тремя подсетями: *AzureFirewallSubnet*, *ServersSubnet* и *JumpboxSubnet*. ServersSubnet и JumpboxSubnet содержат по одному 2-ядерному серверу Windows Server.

В AzureFirewallSubnet размещен брандмауэр, которому присваивается коллекция правил приложения, единственное правило которой разрешает доступ к www.microsoft.com.

Создается пользовательский маршрут, направляющий сетевой трафик из ServersSubnet через брандмауэр, где к нему применяются правила брандмауэра.

Скрипт можно выполнить из Azure [Cloud Shell](https://shell.azure.com/powershell) или из локальной установки PowerShell. 

При локальном запуске PowerShell для выполнения этого скрипта понадобится модуль AzureRM PowerShell 6.4.0 или более поздней версии. Выполните командлет `Get-Module -ListAvailable AzureRM`, чтобы узнать установленную версию. 

Если необходимо выполнить обновление, можно использовать модуль `PowerShellGet`, встроенный в Windows 10 и Windows Server 2016.

> [!NOTE]
>В других версиях Windows необходимо установить модуль `PowerShellGet` перед его использованием. Можно запустить команду `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path`, чтобы определить установлен ли этот модуль на компьютере. Если выходные данные не заполнены, необходимо установить последнюю версию [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616).

Дополнительные сведения см. в статье [Установка Azure PowerShell в ОС Windows с помощью PowerShellGet](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.4.0)

Любая имеющаяся среда Azure PowerShell, установленная с помощью установщика веб-платформы, будет конфликтовать с установкой PowerShellGet, и ее нужно удалить.

Кроме того, необходимо установить предварительную версию AzureRM.Network (версии 6.4.0). Если модуль старше, выполните `Uninstall-Module AzureRM.Network -Force` для его удаления. Далее выполните:

 `Install-Module -Name AzureRM.Network -Repository PSGallery -RequiredVersion 6.4.0-preview -AllowPrerelease -Force`

и начнется установка версии 6.4.0.

Помните, что при локальном запуске PowerShell необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Пример скрипта


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Очистка развертывания 

Выполните следующую команду, чтобы удалить группу ресурсов, виртуальную машину и все связанные с ней ресурсы:

```powershell
Remove-AzureRmResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Описание скрипта

Для создания группы ресурсов, виртуальной сети и групп безопасности сети в этом скрипте используются приведенные ниже команды. Для каждой команды в следующей таблице приведены ссылки на соответствующую документацию:

| Get-Help | Примечания |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Создает группу ресурсов, в которой хранятся все ресурсы. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Создает объект конфигурации подсети. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Создает виртуальную сеть Azure и интерфейсную подсеть. |
| [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) | Создает правила безопасности, которые будут добавлены в группу безопасности сети. |
| [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) |Создает правила групп безопасности сети, которые разрешают или блокируют определенные порты для конкретных подсетей. |
| [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) | Связывает группы безопасности сети с подсетями. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Создает общедоступный IP-адрес для доступа к виртуальной машине из Интернета. |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | Создает виртуальные сетевые интерфейсы и присоединяет их к интерфейсной и внутренней подсети виртуальной сети. |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | Создает конфигурацию виртуальной машины. Эта конфигурация включает в себя такие сведения, как имя виртуальной машины, операционную систему и учетные данные администратора. Данная конфигурации используется при создании виртуальной машины. |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | Создайте виртуальную машину. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Удаляет группу ресурсов и все ресурсы, содержащиеся в ней. |
|New-AzureRmFirewall| Создает экземпляр службы "Брандмауэр Azure".|
|Get-AzureRmFirewall|Получает объект "Брандмауэр Azure".|
|New-AzureRmFirewallApplicationRule|Создает правило приложения службы "Брандмауэр Azure".|
|Set-AzureRmFirewall|Фиксирует изменения в объекте "Брандмауэр Azure".|


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Azure PowerShell см. в [документации по Azure PowerShell](/powershell/azure/overview).

