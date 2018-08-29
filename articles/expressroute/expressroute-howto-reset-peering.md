---
title: Сброс пиринга для Azure ExpressRoute | Документация Майкрософт
description: Практическое руководство по отключению и включению пиринга для цепи ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/15/2018
ms.author: charwen
ms.openlocfilehash: b8c9bc1944e9ed0281616062a84958c953d08694
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180666"
---
# <a name="reset-expressroute-peerings"></a>Сброс пиринга для ExpressRoute

В этой статье рассматривается отключение и включение пиринга для цепи ExpressRoute с помощью PowerShell. После отключения пиринга сеанс BGP как основного, так и дополнительного подключения цепи ExpressRoute будет отключен. Будет разорван обмен данными с корпорацией Майкрософт через этот пиринг. После включении пиринга сеанс BGP как основного, так и дополнительного подключения цепи ExpressRoute будет восстановлен. Будет возобновлен обмен данными с корпорацией Майкрософт через этот пиринг. В цепи ExpressRoute можно индивидуально отключать и включать как пиринг Майкрософт, так и частный пиринг Azure. При первой настройке пиринг в цепи ExpressRoute будет включен по умолчанию. 

Существует несколько сценариев, в которых сброс пиринга для ExpressRoute может оказаться полезным.
* Протестируйте проектирование и реализацию аварийного восстановления. Например, существует две цепи ExpressRoute. Пиринг в первой цепи можно отключить и принудительно перевести весь сетевой трафик на вторую цепь.
* Включите обнаружение двунаправленной передачи (BFD) для частного пиринга Azure в цепи ExpressRoute. Если цепь ExpressRoute создается после 1 августа 2018 г., BFD будет включено по умолчанию. Если цепь была создана ранее приведенной даты, BFD включено не было. BFD можно включить путем отключения и повторного включения пиринга. Также необходимо обратить внимание на то, что BFD поддерживается только для частного пиринга Azure.


## <a name="reset-a-peering"></a>Сброс пиринга

1. Установите последнюю версию командлетов PowerShell для Azure Resource Manager. Дополнительные сведения см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи. Для подключения используйте следующий пример кода:

  ```powershell
  Connect-AzureRmAccount
  ```
3. Если у вас есть несколько подписок Azure, проверьте подписки для этой учетной записи.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Укажите подписку, которую нужно использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Для извлечения цепи ExpressRoute необходимо выполнить следующие команды.

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
  ```
6. Определите пиринг, который требуется включить или отключить. *Пиринг* является массивом. В следующем примере Peerings[0] — это частный пиринг Azure, а Peerings[1] — это пиринг корпорации Майкрософт.

  ```powershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
ServiceKey                       : ########-####-####-####-############
Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
Authorizations                   : []
AllowClassicOperations           : False
GatewayManagerEtag               :
  ```
7. Чтобы изменить состояние пиринга, выполните следующие команды.

  ```powershell
  $ckt.Peerings[0].State = "Disabled"
  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```
Теперь пиринг должен находиться в состоянии, установленном пользователем. 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения об устранении проблем с ExpressRoute можно получить в следующих статьях.
* [Проверка подключения ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Устранение проблем с производительностью сети](expressroute-troubleshooting-network-performance.md)
