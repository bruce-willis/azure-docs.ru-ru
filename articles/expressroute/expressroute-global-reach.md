---
title: Azure ExpressRoute Global Reach | Документация Майкрософт
description: В этой статье содержатся сведения о том, как связать каналы ExpressRoute, чтобы настроить частную сеть между локальными сетями и включить Global Reach.
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966821"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>Связывание каналов ExpressRoute для включения ExpressRoute Global Reach (предварительная версия)

ExpressRoute — частный и устойчивый способ подключения локальных сетей к Microsoft Cloud. Вы можете получить доступ ко многим облачным службам Майкрософт (таким как Azure, Office 365 и Dynamics 365) из центра обработки данных в частной или корпоративной сети. Например, у вас есть филиал в Сан-Франциско с каналом ExpressRoute в Кремниевой долине и другой филиал в Балтиморе с каналом ExpressRoute в Вашингтоне (округ Колумбия). 

У обоих филиалов может быть высокоскоростное подключение к ресурсам Azure в регионах "Восточная часть США" и "Западная часть США". Тем не менее филиалы не могут обмениваться данными напрямую. Другими словами, адреса 10.0.1.0/24 могут обмениваться данными с 10.0.3.0/24 и 10.0.4.0/24, но не с 10.0.2.0/24.

![без][1]

С помощью **ExpressRoute Global Reach** можно связать каналы ExpressRoute, чтобы настроить частную сеть данных между локальными сетями. В приведенном выше примере с добавлением ExpressRoute Global Reach офис в Сан-Франциско (10.0.1.0/24) может напрямую обмениваться данными с офисом в Балтиморе (10.0.2.0/24) через имеющиеся каналы ExpressRoute и через глобальную сеть Майкрософт. 

![с][2]

Сейчас ExpressRoute Global Reach поддерживается в следующих странах:

* США
* Великобритания 
* Япония

Каналы ExpressRoute необходимо создавать в [расположениях пиринга ExpressRoute](expressroute-locations.md) в странах, указанных выше. Чтобы включить ExpressRoute Global Reach между [разными геополитическими регионами](expressroute-locations.md), у каналов должен быть номер SKU уровня "Премиум".


## <a name="before-you-begin"></a>Перед началом работы

> [!IMPORTANT]
> Эта общедоступная предварительная версия предоставляется без соглашения об уровне обслуживания и не должна использоваться для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Перед началом настройки необходимо проверить следующие требования.

* Установите Azure PowerShell последней версии. См. руководство по [установке и настройке Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Ознакомьтесь с [рабочими процессами](expressroute-workflows.md) подготовки канала ExpressRoute.
* Убедитесь, что каналы ExpressRoute подготовлены.
* Убедитесь, что частный пиринг Azure настроен для каналов ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Вход в учетную запись Azure
Чтобы приступить к настройке, необходимо войти в учетную запись Azure. 

Откройте консоль PowerShell с повышенными привилегиями и подключитесь к своей учетной записи. Эта команда запрашивает учетные данные входа для вашей учетной записи Azure.  

```powershell
Connect-AzureRmAccount
```

Если у вас есть несколько подписок Azure, проверьте подписки для этой учетной записи.

```powershell
Get-AzureRmSubscription
```

Укажите подписку, которую нужно использовать.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Определение каналов ExpressRoute для конфигурации
Вы можете включить ExpressRoute Global Reach между любыми двумя каналами ExpressRoute, если они находятся в поддерживаемых странах и созданы в разных расположениях пиринга. Если подписке принадлежат оба канала, вы можете выбрать канал для выполнения конфигурации в следующих разделах. Если два канала находятся в разных подписках Azure, может потребоваться выполнить авторизацию из одной подписки Azure и передать ключ авторизации при выполнении команды конфигурации в другой подписке Azure.

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>Настройка подключения между локальными сетями

Используйте следующие команды, чтобы получить каналы 1 и 2. Эти два канала содержатся в одной подписке.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Выполните следующую команду для канала 1 и передайте полученный идентификатор частного пиринга канала 2.

Обратите внимание, что идентификатор частного пиринга выглядит следующим образом:

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

*-AddressPrefix* должен быть подсетью IPv4/29, например 10.0.0.0/29. Мы будем использовать IP-адреса в этой подсети, чтобы установить подключение между двумя каналами ExpressRoute. Не следует использовать адреса в этой подсети в виртуальных сетях Azure или в локальных сетях. 


Сохраните конфигурацию в канале 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

После завершения предыдущей операции должно появиться подключение между локальными сетями с обеих сторон через два канала ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Каналы ExpressRoute в разных подписках Azure

Если два канала находятся в разных подписках Azure, вам потребуется выполнить авторизацию. В следующей конфигурации авторизация создается в подписке канала 2, а ключ авторизации передается в канал 1.

Создайте ключ авторизации. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Запишите идентификатор частного пиринга канала 2, а также ключ авторизации.

Запустите следующую команду на канале 1. Передайте идентификатор частного пиринга канала 2, а также ключ авторизации.

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Сохраните конфигурацию в канале 1.

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

После завершения предыдущих операций должно появиться подключение между локальными сетями с обеих сторон через два канала ExpressRoute.

## <a name="to-get-and-verify-the-configuration"></a>Получение и проверка конфигурации

Используйте следующую команду для проверки конфигурации на канале, на котором они созданы. В этом примере используется канал 1 из предыдущего примера.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Если вы просто выполняете *$ckt1* в PowerShell, в выходных данных отобразится *CircuitConnectionStatus*. Таким образом вы узнаете, установлено ли подключение (состояние "Подключено" или "Нет подключения"). 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>Отключение подключения между локальными сетями

Чтобы отключить подключение, выполните команды на канале, на котором создана конфигурация. В этом примере используется канал 1 из предыдущих примеров.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Вы можете выполнить операцию Get для проверки состояния. 

После завершения предыдущей операции вы потеряете подключение к локальной сети через каналы ExpressRoute. 

## <a name="next-steps"></a>Дополнительная информация
1. [Получение дополнительных сведений об ExpressRoute Global Reach](expressroute-faqs.md#globalreach).
2. [Проверка подключения ExpressRoute](expressroute-troubleshooting-expressroute-overview.md).
3. [Связывание виртуальной сети Azure с каналом ExpressRoute](expressroute-howto-linkvnet-arm.md).


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "схема без Global Reach"
[2]: ./media/expressroute-global-reach/2.png "схема с Global Reach"