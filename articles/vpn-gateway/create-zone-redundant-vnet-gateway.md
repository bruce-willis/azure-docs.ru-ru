---
title: Создание избыточного в пределах зоны шлюза виртуальной сети в Зонах доступности Azure | Документация Майкрософт
description: Развертывание VPN-шлюза и шлюзов ExpressRoute в Зонах доступности Azure
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: f531be5a814ed1805a2938daec1d210f9daccfa5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949780"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Создание избыточного в пределах зоны шлюза виртуальной сети в Зонах доступности Azure

VPN-шлюзы и шлюзы ExpressRoute можно развернуть в Зонах доступности Azure. В результате шлюзы виртуальной сети смогут работать на более высоких уровнях отказоустойчивости, масштабируемости и доступности. При развертывании в зонах доступности Azure происходит физическое и логическое разделение шлюзов в пределах региона с одновременной защитой локального сетевого подключения к Azure от сбоев на уровне зоны. Дополнительные сведения см. в статье [об избыточных в пределах зоны шлюзах виртуальной сети](about-zone-redundant-vnet-gateways.md) и статье [Что такое Зоны доступности в Azure?](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Перед началом работы

Можно использовать оболочку PowerShell, установленную локально на компьютере, или Azure Cloud Shell. Чтобы установить и использовать PowerShell локально, вам понадобится модуль PowerShell последней версии.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Локальное использование PowerShell

Если вместо Cloud Shell вы используете PowerShell локально на компьютере, необходимо установить модуль PowerShell 6.1.1 или более поздней версии. Чтобы узнать, какая версия PowerShell установлена, используйте приведенную ниже команду.

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. Объявление переменных

Ниже перечислены значения, применяемые для выполняемых в примере действий. Кроме того, на шагах в некоторых примерах используются объявленные переменные. Если вы следуете эти шагам в собственной среде, необходимо заменить эти значения собственными. При указании расположения проверьте, поддерживается ли задаваемый регион. Дополнительные сведения см. в разделе [Часто задаваемые вопросы](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="configure"></a>2. Создание виртуальной сети

Создайте группу ресурсов.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Создайте виртуальную сеть.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3. Добавление подсети шлюза

Подсеть шлюза содержит зарезервированные IP-адреса, используемые службами шлюза виртуальной сети. Используйте приведенные далее примеры для добавления и установки подсети шлюза.

Добавьте подсеть шлюза.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Установка конфигурации подсети шлюза для виртуальной сети.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>4. Запрос общедоступного IP-адреса
 
На этом шаге выберите инструкции, которые применяются к создаваемому шлюзу. Выбор зон для развертывания шлюзов зависит от зон, указанных для общедоступных IP-адресов.

### <a name="ipzoneredundant"></a>Для шлюзов, избыточных в пределах зоны

Запросите общедоступный IP-адрес с номером SKU PublicIpaddress типа **Стандартный** и не указывайте никакую зону. В этом случае созданный стандартный общедоступный IP-адрес будет общедоступным IP-адресом, избыточным в пределах зоны.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Для зональных шлюзов

Запросите общедоступный IP-адрес с номером SKU PublicIpaddress типа **Стандартный**. Укажите зону (1, 2 или 3). Все экземпляры шлюза будут развернуты в этой зоне.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Для региональных шлюзов

Запросите общедоступный IP-адрес с номером SKU PublicIpaddress типа **Базовый**. В этом случае шлюз развертывается как региональный и не имеет встроенной избыточности в пределах зоны. Экземпляры шлюза создаются в любых зонах.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Создание IP-конфигурации

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Создание шлюза

Создайте шлюз виртуальной сети.

### <a name="for-expressroute"></a>Для шлюза ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Для VPN-шлюза

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>Часто задаваемые вопросы

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Что изменится после развертывания этих новых номеров SKU?

Вы можете развертывать шлюзы с избыточностью в пределах зоны. Это означает, что все экземпляры шлюзов будут развернуты в зонах доступности Azure, а каждая зона доступности является сочетанием домена сбоя и домена обновления. В результате шлюзы становятся более надежными, доступными и устойчивыми к сбоям на уровне зоны.

### <a name="can-i-use-the-azure-portal"></a>Я могу использовать портал Azure?

Да, вы можете использовать портал Azure для развертывания новых номеров SKU. Но эти новые номера SKU доступны только для тех регионов Azure, в которых есть Зоны доступности Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>В каких регионах доступны новые номера SKU?

Новые номера SKU доступны в тех регионах Azure, в которых есть Зоны доступности Azure: это центральная часть США, центральная Франция и Западная Европа. В будущем шлюзы, избыточные в пределах зоны, будут доступны и в других общедоступных регионах Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Можно ли обновить существующие шлюзы виртуальной сети до шлюзов, избыточных в пределах зоны, или зональных шлюзов?

Сейчас миграция существующих шлюзов виртуальной сети на избыточные в пределах зоны или зональные шлюзы не поддерживается. Однако вы можете удалить существующий шлюз и повторно создать избыточный в пределах зоны или зональный шлюз.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Можно ли развертывать VPN-шлюзы и шлюзы Express Route в одной виртуальной сети?

Допускается одновременное функционирование VPN-шлюзов и шлюзов Express Route в одной виртуальной сети. Но при этом необходимо зарезервировать диапазон IP-адресов /27 для подсети шлюза.