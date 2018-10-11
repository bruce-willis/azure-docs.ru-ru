---
title: Создание автоматически масштабируемого, избыточного в пределах зоны шлюза приложений с зарезервированным IP-адресом с помощью Azure PowerShell
description: Узнайте, как создать автоматически масштабируемый, избыточный в пределах зоны шлюз приложений с зарезервированным IP-адресом с помощью Azure PowerShell.
services: application-gateway
author: amitsriva
ms.service: application-gateway
ms.topic: tutorial
ms.date: 9/26/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: d86ce2e1bac2fb58df8df748381a00eac21e65cb
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48016940"
---
# <a name="tutorial-create-an-autoscaling-zone-redundant-application-gateway-with-a-reserved-virtual-ip-address-using-azure-powershell"></a>Руководство по созданию автоматически масштабируемого, избыточного в пределах зоны шлюза приложений с зарезервированным виртуальным IP-адресом с помощью Azure PowerShell

В этом руководстве объясняется, как создать шлюз приложений Azure, используя командлеты Azure PowerShell и модель развертывания с помощью Azure Resource Manager. Данное руководство посвящено различиям между новым номером SKU "Автомасштабирование" и номером SKU "Стандартный". В частности, рассматриваются компоненты, обеспечивающие поддержку автомасштабирования, избыточности в пределах зоны и зарезервированных виртуальных IP-адресов (статических IP-адресов).

Дополнительные сведения об избыточности в пределах зоны и автомасштабировании шлюза приложений см. в разделе [Автоматическое масштабирование и шлюз приложений, избыточный в пределах зоны (общедоступная предварительная версия)](application-gateway-autoscaling-zone-redundant.md).

> [!IMPORTANT]
> SKU автоматического масштабирования и шлюза приложений, избыточного в пределах зоны, на данный момент доступен в общедоступной предварительной версии. Предварительная версия предоставляется без соглашения об уровне обслуживания. Не рекомендуем использовать ее в рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка параметра конфигурации автомасштабирования.
> * Использование параметра зоны.
> * Использование статического виртуального IP-адреса.
> * Создание шлюза приложений


Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

Для работы с этим руководством требуется запустить Azure PowerShell в локальной среде. Необходим модуль Azure PowerShell 6.9.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). После проверки версии PowerShell выполните командлет `Login-AzureRmAccount`, чтобы создать подключение к Azure.

## <a name="sign-in-to-your-azure-account"></a>Вход в учетную запись Azure

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -Subscription "<sub name>"
```
## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте группу ресурсов в одном из доступных расположений.

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzureRmResourceGroup -Name $rg -Location $location
```

## <a name="create-a-vnet"></a>Создание виртуальной сети
Создайте виртуальную сеть с одной выделенной подсетью для автоматически масштабируемого шлюза приложений. В настоящее время в каждой выделенной подсети можно развернуть только один автоматически масштабируемый шлюз приложений.

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzureRmVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>Создание зарезервированного общедоступного IP-адреса

Следует указать **статический** метод распределения PublicIPAddress. Виртуальный IP-адрес автоматически масштабируемого шлюза приложений может быть только статическим. Динамические IP-адреса не поддерживаются. Поддерживается только номер SKU "Стандартный" для PublicIpAddress.

```azurepowershell
#Create static public IP
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>Получение сведений

Извлеките сведения о группе ресурсов, подсети и IP-адресе в локальный объект, чтобы создать сведения об IP-конфигурации шлюза приложений.

```azurepowershell
$resourceGroup = Get-AzureRmResourceGroup -Name $rg
$publicip = Get-AzureRmPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzureRmvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```
## <a name="configure-application-gateway-infrastructure"></a>Настройка инфраструктуры шлюза приложений
Настройте IP-конфигурацию, IP-конфигурацию внешнего интерфейса, внутренний пул, параметры HTTP, сертификат, порт, прослушиватель и правило в формате, идентичном конфигурации имеющегося шлюза приложений категории "Стандартный". В новом номере SKU используется та же объектная модель, что и в номере SKU "Стандартный".

```azurepowershell
$ipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzureRmApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzureRmApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "scrap" -AsPlainText -Force
$sslCert01 = New-AzureRmApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "D:\Networking\ApplicationGateway\scrap.pfx"
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>Настройка автомасштабирования

Теперь можно указать конфигурацию автомасштабирования для шлюза приложений. Поддерживаются два типа конфигурации автомасштабирования.

- **Режим фиксированной емкости**. В этом режиме шлюз приложений не применяет автомасштабирование и использует фиксированное число единиц масштабирования.

   ```azurepowershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```
- **Режим автомасштабирования**. В этом режиме шлюз приложений автоматически масштабируется в соответствии с шаблоном трафика приложений.

   ```azurepowershell
   $autoscaleConfig = New-AzureRmApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>Создание шлюза приложений

Создайте шлюз приложений и добавьте зоны избыточности. 

Настройка зон поддерживается только в регионах, где доступны зоны Azure. В регионах, где зоны Azure недоступны, использовать параметр зоны не следует. Шлюз приложений можно также развернуть в одной, двух или во всех трех зонах. Адрес PublicIPAddress для шлюза приложений в одной зоне должен быть привязан к той же зоне. Для шлюза приложений, избыточного в пределах в двух или трех зон, адрес PublicIPAddress также должен быть избыточным в пределах зоны, поэтому зона не указывается.

```azurepowershell
$appgw = New-AzureRmApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>Тестирование шлюза приложений

Используйте командлет [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress), чтобы получить общедоступный IP-адрес шлюза приложений. Скопируйте общедоступный IP-адрес или DNS-имя и вставьте его в адресную строку браузера.

`Get-AzureRmPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP`

## <a name="clean-up-resources"></a>Очистка ресурсов
Сначала просмотрите ресурсы, созданные вместе со шлюзом приложений. Затем вы можете удалить группу ресурсов, шлюз приложений и все связанные с ними ресурсы, если они больше не требуются. Для этого выполните команду `Remove-AzureRmResourceGroup`.

`Remove-AzureRmResourceGroup -Name $rg`

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Использование статического виртуального IP-адреса.
> * Настройка параметра конфигурации автомасштабирования.
> * Использование параметра зоны.
> * Создание шлюза приложений

> [!div class="nextstepaction"]
> [Создание шлюза приложений с правилами маршрутизации на основе URL-путей](./tutorial-url-route-powershell.md)
