---
title: Настройка Azure ExpressRoute Direct | Документация Майкрософт
description: Эта страница поможет вам настроить ExpressRoute-Direct (предварительная версия)
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 6bf2da140f53d39812fb475130471d49750b9f4b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947794"
---
# <a name="how-to-configure-expressroute-direct-preview"></a>Настройка ExpressRoute Direct (предварительная версия)

ExpressRoute Direct дает возможность подключения непосредственно в глобальной сети корпорации Майкрософт в стратегически распределенных по всему миру расположениях пиринга. Дополнительные сведения см. в разделе [About ExpressRoute Direct](expressroute-erdirect-about.md) (Общие сведения о подключении ExpressRoute Direct).

> [!IMPORTANT]
> Продукт ExpressRoute Direct в настоящее время доступен в предварительной версии.
>
> Общедоступная предварительная версия предоставляется без соглашения об уровне обслуживания и не должна использоваться для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="resources"></a>1. Создание ресурса

1. Войдите в Azure и выберите подписку. Ресурс ExpressRoute Direct и каналы ExpressRoute должны находиться в одной подписке.

  ```powershell
  Connect-AzureRMAccount 

  Select-AzureRMSubscription -Subscription “<SubscriptionID or SubscriptionName>”
  ```
2. Выведите список всех расположений, где поддерживается ExpressRoute Direct.
  
  ```powershell
  Get-AzureRMExpressRoutePortLocations
  ```

  **Пример выходных данных**
  
  ```powershell
  Name                : Equinix-Ashburn-DC2
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
  ProvisioningState   : Succeeded
  Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-Dallas-DA3
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
  ProvisioningState   : Succeeded
  Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
  Contact             : support@equinix.com
  AvailableBandwidths : []

  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : []
  ```
3. Определение, имеет ли место в приведенном выше списке доступную пропускную способность

  ```powershell
  Get-AzureRMExpressRoutePortsLocations -Name "Equinix-San-Jose-SV1"
  ```

  **Пример выходных данных**

  ```powershell
  Name                : Equinix-San-Jose-SV1
  Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
  ProvisioningState   : Succeeded
  Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
  Contact             : support@equinix.com
  AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
  ```
4. Создание ресурса ExpressRoute Direct на основе выбранного выше расположения

  ExpressRoute Direct поддерживает инкапсуляцию как QinQ, так и Dot1Q. Если выбран QinQ, каждому каналу ExpressRoute динамически назначается S-тег и каналы будут уникальными в пределах ресурса ExpressRoute Direct. Каждый C-тег в канале должен быть уникальным в пределах канала, но не для всего ExpressRoute Direct.  

  Если выбрана инкапсуляция Dot1Q, необходимо достичь уникальности C-тега (VLAN) в пределах всего ресурса ExpressRoute Direct.  

  > [!IMPORTANT]
  > ExpressRoute Direct может иметь только один тип инкапсуляции. После создания ExpressRoute Direct изменить инкапсуляцию невозможно.
  > 
 
  ```powershell 
  $ERDirect = New-AzureRMExpressRoutePort -Name $Name -ResourceGroupName -$RGName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
  ```

  > [!NOTE]
  > Атрибуту инкапсуляции также можно присвоить значение Dot1Q. 
  >

  **Пример выходных данных**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
  Circuits                   : []
  ```

## <a name="state"></a>2. Изменение параметра AdminState ссылок

Этот процесс следует использовать для проведения тестирования уровня 1, что в свою очередь обеспечит правильное исправление перекрестных подключений в каждом маршрутизаторе для основного и дополнительного подключения.

1. Получите подробные сведения об ExpressRoute Direct.

  ```powershell
  $ERDirect = Get-AzureRmExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  ```
2. Установите параметр ссылки как Enabled (включена). Повторите этот шаг, чтобы установить состояние каждой ссылки как Enabled (включена).

  Ссылки [0] — это основной порт, а ссылки [1] — это дополнительный порт.

  ```powershell
  $ERDirect.Links[0].AdminState = “Enabled”
  Set-AzureRmExpressRoutePort -ExpressRoutePort $ERDirect
  $ERDirect = Get-AzureRmExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
  $ERDirect.Links[1].AdminState = “Enabled”
  Set-AzureRmExpressRoutePort -ExpressRoutePort $ERDirect
  ```
  **Пример выходных данных**

  ```powershell
  Name                       : Contoso-Direct
  ResourceGroupName          : Contoso-Direct-rg
  Location                   : westcentralus
  Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
  Etag                       : W/"<etagnumber> "
  ResourceGuid               : <number>
  ProvisioningState          : Succeeded
  PeeringLocation            : Equinix-Seattle-SE2
  BandwidthInGbps            : 100
  ProvisionedBandwidthInGbps : 0
  Encapsulation              : QinQ
  Mtu                        : 1500
  EtherType                  : 0x8100
  AllocationDate             : Saturday, September 1, 2018
  Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
  Circuits                   : []
  ```

Используйте эту же процедуру с `AdminState = “Disabled”`, чтобы выключить порты.

## <a name="circuit"></a>3. Создание канала

По умолчанию можно создать до 10 каналов в подписке, в которой есть ресурс ExpressRoute Direct. Служба поддержки может увеличить это значение. Вы несете ответственность за отслеживание как подготовленной, так и используемой пропускной способности. Подготовленная пропускная способность — это сумма пропускных способностей всех каналов ресурса ExpressRoute Direct, а использованная пропускная способность — это физическое использование базовых физических интерфейсов.

Существуют дополнительные пропускные способности каналов, которые могут использоваться на ExpressRoute Direct только для поддержки сценариев, описанных выше. А именно: 40 Гбит/с и 100 Гбит/с.

Можно создавать каналы уровня "Стандартный" или "Премиум". Стандартные каналы включены в стоимость, в то время как стоимость каналов уровня "премиум" основана на выбранной пропускной способности. Каналы можно создавать только как измеренные, поскольку ExpressRoute Direct не поддерживает неограниченные.

Создайте канал в ресурсе ExpressRoute Direct.

```powershell
New-AzureRmExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 1.0 | 2.0 | 5.0 | 10.0 | 40.0 | 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
```

Другие значения пропускной способности включают в себя: 1.0, 2.0, 5.0, 10.0 и 40.0

**Пример выходных данных**

```powershell
Name                             : ExpressRoute-Direct-ckt
ResourceGroupName                : Contoso-Direct-rg
Location                         : westcentralus
Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
Etag                             : W/"<etagnumber>"
ProvisioningState                : Succeeded
Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             : 
ServiceProviderProperties        : null
ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
BandwidthInGbps                  : 10
Stag                             : 2
ServiceKey                       : <number>
Peerings                         : []
Authorizations                   : []
AllowClassicOperations           : False
GatewayManagerEtag     
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения см. в разделе [About ExpressRoute Direct](expressroute-erdirect-about.md) (Общие сведения о подключении ExpressRoute Direct).