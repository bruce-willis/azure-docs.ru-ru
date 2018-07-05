---
title: Создание избыточного в пределах зоны шлюза виртуальной сети в зонах доступности Azure (предварительная версия) | Документы Майкрософт
description: Развертывание VPN-шлюзов и шлюзов ExpressRoute в зонах доступности (предварительная версия)
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/25/2018
ms.author: cherylmc
ms.openlocfilehash: a08c0f772965ddb2b40ac1ced1f26ade4cba3197
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37017038"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Создание избыточного в пределах зоны шлюза виртуальной сети в зонах доступности Azure

Вы можете развернуть VPN-шлюзы и шлюзы ExpressRoute в [зонах доступности Azure](../availability-zones/az-overview.md). В результате шлюзы виртуальной сети смогут работать на более высоких уровнях отказоустойчивости, масштабируемости и доступности. При развертывании в зонах доступности Azure происходит физическое и логическое разделение шлюзов в пределах региона с одновременной защитой локального сетевого подключения к Azure от сбоев на уровне зоны.

Зональные и избыточные в пределах зоны шлюзы характеризуются более фундаментальными улучшениями производительности по сравнению с обычными шлюзами виртуальной сети. Кроме того, создание зонального или избыточного в пределах зоны шлюза виртуальной сети выполняется быстрее, чем создание других типов шлюзов. Теперь не нужно выделять 45 минут, так как создание шлюза ExpressRoute занимает примерно 15 минут, а создание VPN-шлюза — 19 минут.

### <a name="zrgw"></a>Шлюзы, избыточные в пределах зоны

Чтобы автоматически развернуть шлюзы виртуальной сети в зонах доступности, можно использовать шлюзы виртуальной сети, избыточные в пределах зоны. Благодаря избыточным в пределах зоны шлюзам можно задействовать соглашение об уровне обслуживания с гарантией времени непрерывной работы на уровне 99,99 % в общедоступной версии для доступа к критически важным и масштабируемым службам в Azure.

<br>
<br>

![схема с шлюзами, избыточными в пределах зоны](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Зональные шлюзы

Зональные шлюзы применяются для развертывания шлюзов в определенной зоне. При развертывании зонального шлюза оба экземпляра шлюза развертываются в одной зоне доступности.

<br>
<br>

![схема с зональными шлюзами](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKU шлюзов

Шлюзы, избыточные в пределах зоны, и зональные шлюзы должны использовать новые номера SKU шлюзов. После [самостоятельной регистрации для использования предварительной версии](#enroll) вы увидите новые номера SKU шлюзов виртуальной сети во всех регионах Azure AZ. Эти номера SKU аналогичны соответствующим номерам SKU для шлюза ExpressRoute и VPN-шлюза, за исключением того, что они относятся к шлюзам, избыточным в пределах зоны, и зональным шлюзам.

Доступны следующие новые номера SKU шлюзов:

### <a name="vpn-gateway"></a>VPN-шлюз

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Номера SKU общедоступных IP-адресов

Для избыточных в пределах зоны и зональных шлюзов применяется номер SKU *Стандартный* для общедоступного IP-ресурса Azure. Конфигурация общедоступного IP-ресурса Azure определяет, является ли развертываемый шлюз избыточным в пределах зоны или зональным. При создании общедоступного IP-ресурса с помощью SKU *Базовый* у шлюза не будет избыточности в пределах зоны, в ресурсы шлюза будут региональными.

### <a name="pipzrg"></a>Шлюзы, избыточные в пределах зоны

При создании общедоступного IP-адреса с помощью SKU **Стандартный** для общедоступного IP-адреса без указания зоны поведение зависит от типа шлюза — VPN-шлюз или шлюз ExpressRoute. 

* Если используется VPN-шлюз, для обеспечения избыточности в пределах зоны два экземпляра шлюза будут развернуты в любых двух из этих трех зон. 
* Если используется шлюз ExpressRoute и допускается наличие нескольких экземпляров, шлюз может развертываться во всех трех зонах.

### <a name="pipzg"></a>Зональные шлюзы

При создании общедоступного IP-адреса с помощью номера SKU **Стандартный** для общедоступных IP-адресов и указании зоны (1, 2 или 3) все экземпляры шлюза будут развернуты в одной зоне.

### <a name="piprg"></a>Региональные шлюзы

При создании общедоступного IP-адреса с помощью номера SKU **Базовый** для общедоступных IP-адресов шлюз развертывается как региональный и не имеет встроенной избыточности в пределах зоны.

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

## <a name="enroll"></a>1. Регистрация для использования предварительной версии

Перед настройкой избыточного в пределах зоны или зонального шлюза сначала необходимо самостоятельно зарегистрировать подписку в предварительной версии. По завершении подготовки подписки вы увидите новые номера SKU шлюза во всех регионах Azure AZ. 

Убедитесь, что вы вошли в учетную запись Azure и используете подписку, которую нужно включить в список разрешенных для этой предварительной версии. Используйте следующий пример для регистрации:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Чтобы проверить регистрацию функции AllowVMSSVirtualNetworkGateway в подписке, выполните следующую команду:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Результат будет выглядеть приблизительно как в приведенном примере.

![подготовлено](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Объявление переменных

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

## <a name="configure"></a>3. Создание виртуальной сети

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

## <a name="gwsub"></a>4. Добавление подсети шлюза

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
## <a name="publicip"></a>5. Запрос общедоступного IP-адреса
 
На этом шаге выберите инструкции, которые применяются к создаваемому шлюзу. Выбор зон для развертывания шлюзов зависит от зон, указанных для общедоступных IP-адресов.

### <a name="ipzoneredundant"></a>Для шлюзов, избыточных в пределах зоны

Запросите общедоступный IP-адрес с номером SKU PublicIpaddress типа **Стандартный** и не указывайте никакую зону. В этом случае созданный стандартный общедоступный IP-адрес будет общедоступным IP-адресом, избыточным в пределах зоны.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard
```

### <a name="ipzonalgw"></a>Для зональных шлюзов

Запросите общедоступный IP-адрес с номером SKU PublicIpaddress типа **Стандартный**. Укажите зону (1, 2 или 3). Все экземпляры шлюза будут развернуты в этой зоне.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Для региональных шлюзов

Запросите общедоступный IP-адрес с номером SKU PublicIpaddress типа **Базовый**. В этом случае шлюз развертывается как региональный и не имеет встроенной избыточности в пределах зоны. Экземпляры шлюза создаются в любых зонах.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Создание IP-конфигурации

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Создание шлюза

Создайте шлюз виртуальной сети.

>[!NOTE]
>В данный момент указать номер SKU шлюза невозможно. По умолчанию будет использоваться номер SKU ErGw1AZ для шлюза ExpressRoute и VpnGw1AZ для VPN-шлюза.
>

### <a name="for-expressroute"></a>Для шлюза ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Для VPN-шлюза

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Отправка отзыва

Ваши отзывы важны для нас! Отправьте сообщение по адресу aznetworkgateways@microsoft.com, чтобы написать о проблемах или оставить отзыв (положительный или отрицательный) об избыточных в пределах зоны и зональных VPN-шлюзах и шлюзах Express Route. В строке темы заключите название компании в скобки []. Если вы сообщаете о проблеме, укажите идентификатор подписки.

## <a name="faq"></a>Часто задаваемые вопросы

### <a name="how-do-i-sign-up-for-the-preview"></a>Как зарегистрироваться для получения предварительной версии?

Вы можете пройти [самостоятельную регистрацию](#enroll) с помощью команд PowerShell, указанных в этой статье.

### <a name="what-will-change-when-i-enroll"></a>Что изменится после регистрации?

Во время использования предварительной версии вы можете развертывать шлюзы с избыточностью в пределах зоны. Это означает, что все экземпляры шлюзов будут развернуты в зонах доступности Azure, а каждая зона доступности является сочетанием домена сбоя и домена обновления. В результате шлюзы становятся более надежными, доступными и устойчивыми к сбоям на уровне зоны.

### <a name="what-regions-are-available-for-the-preview"></a>В каких регионах доступна предварительная версия?

Избыточные в пределах зоны и зональные шлюзы доступны в производственных или общедоступных регионах Azure.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Будет ли выставляться счет за использование этой предварительной версии?

При использовании предварительной версии счета за шлюзы выставляться не будут. Однако для развертывания не действует соглашение об уровне обслуживания. Нас очень интересует ваше мнение.

> [!NOTE]
> За использование шлюзов ExpressRoute плата не взимается. Однако счет будет выставляться за сам канал (не шлюз).

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>В каких регионах можно испробовать предварительную версию?

Общедоступная предварительная версия доступна в центральной части США и Центральной Франции (регионах Azure с зонами доступности уже в продаже). В будущем шлюзы, избыточные в пределах зоны, будут доступны и в других общедоступных регионах Azure.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Можно ли изменить существующие шлюзы виртуальной сети на избыточные в пределах зоны или зональные шлюзы?

Сейчас миграция существующих шлюзов виртуальной сети на избыточные в пределах зоны или зональные шлюзы не поддерживается. Однако вы можете удалить существующий шлюз и повторно создать избыточный в пределах зоны или зональный шлюз.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Можно ли развертывать VPN-шлюзы и шлюзы Express Route в одной виртуальной сети?

В общедоступной предварительной версии поддерживается сосуществование VPN-шлюзов и шлюзов Express Route в одной виртуальной сети. При этом следует учитывать приведенные далее требования и ограничения.

* Необходимо зарезервировать диапазон IP-адресов /27 для подсети шлюза.
* Избыточные в пределах зоны или зональные шлюзы Express Route могут сосуществовать только с избыточными в пределах зоны или зональными VPN-шлюзами.
* Избыточный в пределах зоны или зональный шлюз Express Route следует развертывать до развертывания избыточного в пределах зоны или зонального VPN-шлюза.
* Избыточный в пределах зоны или зональный шлюз Express Route можно подключать максимум к четырем каналам.

## <a name="next-steps"></a>Дополнительная информация

Ваши отзывы важны для нас! Отправьте сообщение по адресу aznetworkgateways@microsoft.com, чтобы написать о проблемах или оставить отзыв (положительный или отрицательный) об избыточных в пределах зоны и зональных VPN-шлюзах и шлюзах Express Route. В строке темы заключите название компании в скобки []. Если вы сообщаете о проблеме, укажите идентификатор подписки.