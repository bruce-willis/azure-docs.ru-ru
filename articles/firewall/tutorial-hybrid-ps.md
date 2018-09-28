---
title: Развертывание и настройка Брандмауэра Azure в гибридной сети с помощью Azure PowerShell
description: В этом руководстве вы можете узнать, как развернуть и настроить службу "Брандмауэр Azure" с помощью портала Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 919051a945d423a104b286e9c5703c5b749cf026
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946465"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Руководство по развертыванию и настройке Брандмауэра Azure в гибридной сети с помощью Azure PowerShell


Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка сетевой среды
> * Настройка и развертывание брандмауэра.
> * Создание маршрутов.
> * Создание виртуальных машин
> * тестирование брандмауэра.

При работе с этим руководством вы создадите три виртуальные сети:
- **VNet-Hub** — в этой виртуальной сети находится брандмауэр.
- **VNet-Spoke** — виртуальная сеть периферийных зон, которая представляет собой рабочую нагрузку, расположенную в Azure.
- **VNet-Onprem** — локальная виртуальная сеть. При фактическом развертывании ее можно подключить либо через соединение VPN, либо через Express Route. Для простоты в этом руководстве используется подключение к VPN-шлюзу, а виртуальная сеть, размещенная в Azure, используется для представления локальной сети.

![Брандмауэр в гибридной сети](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

## <a name="key-requirements"></a>Основные требования

Чтобы этот сценарий работал правильно, есть три ключевых требования:

- Определяемый пользователем маршрут в периферийной подсети, указывающий на IP-адрес Брандмауэра Azure как шлюз по умолчанию. Распространение маршрутов BGP должно быть **отключено** в этой таблице маршрутов.
- Определяемый пользователем маршрут в подсети шлюза центра должен указывать на IP-адрес брандмауэра в качестве следующего прыжка к периферийным сетям.
- В подсети Брандмауэра Azure не требуется ни один определяемый пользователем маршрут, так как он узнает о маршрутах из BGP.
- Не забудьте указать параметр **AllowGatewayTransit** при пиринге из VNet-Hub в VNet-Spoke и параметр **UseRemoteGateways** при пиринге из VNet-Spoke в VNet-Hub.

См. раздел [Создание маршрутов](#create-routes) в этом руководстве, чтобы узнать, как создаются эти маршруты.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="declare-the-variables"></a>Объявление переменных
В примере ниже объявлены переменные со значениями для этого руководства. В большинстве случаев эти значения вам нужно заменить собственными. Но вы можете использовать указанные переменные, чтобы ознакомиться с этим типом конфигурации. Измените переменные (при необходимости), а затем скопируйте и вставьте их в консоль PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke VNet

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the OnPrem VNet

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов, которая будет содержать все ресурсы, требуемые в рамках этого руководства.

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

## <a name="create-and-configure-the-firewall-hub-vnet"></a>Создание и настройка виртуальной сети центра брандмауэра

Определите подсети, которые будут включены в виртуальную сеть:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Теперь создайте виртуальную сеть центра брандмауэра:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```
Запросите выделение общедоступного IP-адреса для шлюза VPN, который будет создан для виртуальной сети. Учтите, что параметр *AllocationMethod* является **динамическим**. Указать необходимый IP-адрес нельзя. Он выделяется для шлюза VPN динамически. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```
## <a name="create-and-configure-the-spoke-vnet"></a>Создание и настройка виртуальной сети периферийных зон

Определите подсети, которые будут включены в виртуальную сеть периферийных зон:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Создайте периферийную виртуальную сеть:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="configure-and-deploy-the-firewall"></a>Настройка и развертывание брандмауэра

Теперь разверните брандмауэр в виртуальной сети центра.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Настройка правил сети

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80
$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *
$Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2,$Rule3 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```
### <a name="configure-an-application-rule"></a>Настройка правила приложения

```azurepowershell
$Rule4 = New-AzureRmFirewallApplicationRule -Name "AllowBing" -Protocol "Http:80","Https:443" `
   -SourceAddress $SNOnpremPrefix -TargetFqdn "bing.com"

$AppRuleCollection = New-AzureRmFirewallApplicationRuleCollection -Name RCApp01 -Priority 100 `
   -Rule $Rule4 -ActionType "Allow"
$Azfw.ApplicationRuleCollections = $AppRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw

```

## <a name="create-and-connect-the-vpn-gateways"></a>Создание и подключение шлюзов VPN

Локальная виртуальная сеть и сеть центра подключены через VPN-шлюзы.

### <a name="create-a-vpn-gateway-for-the-hub-vnet"></a>Создание VPN-шлюза для виртуальной сети центра

Создайте конфигурацию VPN-шлюза. Конфигурация VPN-шлюза определяет используемые подсеть и общедоступный IP-адрес.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Создайте VPN-шлюз для виртуальной сети центра. Для подключений типа VNet-to-VNet необходимо использовать тип VPN RouteBased. Создание VPN-шлюза часто занимает 45 минут и более, в зависимости от выбранного SKU VPN-шлюза.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-onprem-vnet"></a>Создание VPN-шлюза для локальной виртуальной сети

Создайте конфигурацию VPN-шлюза. Конфигурация VPN-шлюза определяет используемые подсеть и общедоступный IP-адрес.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Создайте VPN-шлюз для локальной виртуальной сети. Для подключений типа VNet-to-VNet необходимо использовать тип VPN RouteBased. Создание VPN-шлюза часто занимает 45 минут и более, в зависимости от выбранного SKU VPN-шлюза.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```
### <a name="create-the-vpn-connections"></a>Создание VPN-подключений
Теперь можно создать VPN-подключения между шлюзами виртуальной сети центра и локальной сети.

#### <a name="get-the-vpn-gateways"></a>Получение VPN-шлюзов
```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Создание подключений

Теперь создайте подключение между виртуальной сетью центра и локальной виртуальной сетью. Вы увидите ссылки на общий ключ в примерах. Можно использовать собственные значения для общего ключа. Важно, чтобы общий ключ в обоих подключениях был одинаковым. Создание подключения может занять некоторое время.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Создайте подключение между виртуальной сетью центра и локальной виртуальной сетью. Этот шаг похож на предыдущий, за исключением того, что вы создаете подключение из Vnet-Onprem к VNet-hub. Убедитесь, что общие ключи совпадают. Подключение установится через несколько минут.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
#### <a name="verify-the-connection"></a>Проверка подключения
 
Убедиться в успешном выполнении подключения можно с помощью командлета *Get-AzureRmVirtualNetworkGatewayConnection* с параметром *-Debug* или без него. Используйте командлет из следующего примера, подставив свои значения. При появлении запроса выберите **A**, чтобы выполнить команду **All** (Все). В примере параметр *-Name* — это имя подключения, которое требуется проверить.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

После завершения работы командлета просмотрите результаты. В следующем примере показано, что подключение установлено (состояние *Подключено*), а также указан объем полученных и отправленных данных в байтах.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="create-and-configure-the-onprem-vnet"></a>Создание и настройка локальной виртуальной сети

Определите подсети, которые будут включены в виртуальную сеть:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Теперь создайте виртуальную сеть локальной среды:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```
Запросите выделение общедоступного IP-адреса для шлюза, который будет создан для виртуальной сети. Учтите, что параметр *AllocationMethod* является **динамическим**. Указать необходимый IP-адрес нельзя. Он выделяется для шлюза динамически. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="peer-the-hub-and-spoke-vnets"></a>Установление пиринга между виртуальными сетями периферийных зон и центра

Теперь установите пиринг между виртуальными сетями периферийных зон и центра.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```
## <a name="create-routes"></a>Создание маршрутов

Создайте несколько маршрутов: 
- Маршрут от подсети шлюза центра до периферийной подсети через IP-адрес брандмауэра.
- Маршрут по умолчанию из периферийной подсети через IP-адрес брандмауэра.

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```
## <a name="create-virtual-machines"></a>Создание виртуальных машин

Теперь создайте виртуальные машины для периферийной рабочей нагрузки и локальной среды и поместите их в соответствующие подсети.

### <a name="create-the-workload-virtual-machine"></a>Создание виртуальной машины с рабочей нагрузкой
Создайте виртуальную машину в периферийной виртуальной сети, запустив IIS, без общедоступного IP-адреса и проверьте связь.
При появлении запроса введите имя пользователя и пароль для виртуальной машины.

```azurepowershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1

#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1
```

### <a name="create-the-onprem-virtual-machine"></a>Создание виртуальной машины в локальной сети
Это стандартная виртуальная машина, из которой можно подключиться к удаленному рабочему столу с помощью общедоступного IP-адреса. Оттуда вы можете подключиться к серверу локальной виртуальной сети через брандмауэр. При появлении запроса введите имя пользователя и пароль для виртуальной машины.
```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>тестирование брандмауэра.
Сначала получите частный IP-адрес для виртуальной машины **VM-spoke-01**.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

1. На портале Azure подключитесь к виртуальной машине **VM-Onprem**.
2. Откройте командную строку Windows PowerShell на виртуальной машине **VM-Onprem** и выполните проверку IP-адреса для **VM-talk-01**.

   Вы должны получить ответ.
1. Откройте веб-браузер в **VM-Onprem** и перейдите по адресу http://\<частный IP-адрес VM-spoke-01\>.

   Вы должны увидеть веб-страницу по умолчанию "Службы IIS".

3. На виртуальной машине **VM-Onprem** откройте удаленный рабочий стол и выполните подключение к **VM-talk-01** с помощью частного IP-адреса.

   Ваше соединение должно успешно выполниться, и вы сможете войти в систему, используя свое имя пользователя и пароль.

Итак, теперь вы убедились в том, что правила брандмауэра работают:

- Вы можете проверить связь сервера с периферийной виртуальной сетью.
- Вы можете перейти на веб-сервер в периферийной виртуальной сети.
- Вы можете подключиться к серверу в периферийной виртуальной сети по протоколу удаленного рабочего стола.

Затем измените действие коллекции сетевых правил брандмауэра на **Запретить**, чтобы убедиться, что правила брандмауэра работают должным образом. Выполните следующий скрипт, чтобы изменить действие коллекции правил на **Запретить**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

$rcApp = $azfw.GetApplicationRuleCollectionByName("RCApp01")
$rcApp.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```
Теперь снова запустите тесты. На этот раз все они должны завершиться сбоем. Закройте имеющиеся удаленные рабочие столы перед тестированием измененных правил.

## <a name="clean-up-resources"></a>Очистка ресурсов

Вы можете сохранить ресурсы брандмауэра для следующего руководства или, если он больше не нужен, удалить группу ресурсов **FW-Hybrid-Test**, чтобы удалить ресурсы, связанные с брандмауэром.


## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Настройка сетевой среды
> * Настройка и развертывание брандмауэра.
> * Создание маршрутов.
> * Создание виртуальных машин
> * тестирование брандмауэра.

Теперь вы можете отследить журналы Брандмауэра Azure.

> [!div class="nextstepaction"]
> [Руководство по мониторингу журналов Брандмауэра Azure](./tutorial-diagnostics.md)
