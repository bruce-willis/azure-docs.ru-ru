---
title: Создание таблицы маршрутов виртуального концентратора виртуальной глобальной сети Azure для управления сетевым виртуальным модулем | Документация Майкрософт
description: Таблица маршрутов виртуального концентратора виртуальной глобальной сети для маршрутизации трафика к сетевому виртуальному модулю.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 56321c7c2239c0f1f0e73126d90a521e3cafb853
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984200"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Создание таблицы маршрутов виртуального концентратора для маршрутизации трафика к сетевому виртуальному модулю

В этой статье показано, как маршрутизировать трафик с виртуального концентратора на сетевой виртуальный модуль. 

![Схема Виртуальной глобальной сети](./media/virtual-wan-route-table/vwanroute.png)

Из этой статьи вы узнаете о следующем.

* Создание глобальной сети.
* Создание концентратора.
* Создание виртуального концентратора сетевых подключений
* Создание маршрута концентратора
* Создание таблицы маршрутов
* Применение таблицы маршрутов

## <a name="before-you-begin"></a>Перед началом работы

Убедитесь, что вы выполнили следующие критерии:

1. У вас есть сетевой виртуальный модуль (NVA) — это стороннее программное обеспечение по вашему выбору, которое обычно предоставляется с Azure Marketplace (ссылка) в виртуальной сети.
2. У вас есть частный IP-адрес, присвоенный сетевому интерфейсу виртуального сетевого модуля. 
3. Невозможно развернуть виртуальный сетевой модуль (NVA) в виртуальном концентраторе. Он должен быть развернут в отдельной виртуальной сети (VNet). В этой статье виртуальная сеть обозначена как "DMZ VNet".
4. К "DMZ VNet" может быть подключена одна или несколько виртуальных сетей. В этой статье такие виртуальные сети обозначены как "косвенные виртуальные сети периферийных зон". Такие виртуальные сети могут подключаться к DMZ VNet с помощью пиринговой связи между виртуальными сетями.
5. Убедитесь, что у вас уже созданы две виртуальные сети. Они будут использоваться в качестве виртуальных сетей периферийных зон. В этой статье адресные пространства периферийных зон виртуальной сети — 10.0.2.0/24 и 10.0.3.0/24. Если вам нужна информация о том, как создать виртуальную сеть, см. раздел [Краткое руководство. Создание виртуальной сети с помощью PowerShell](../virtual-network/quick-create-powershell.md).
6. Убедитесь, что ни в одной из виртуальных сетей нет шлюзов виртуальных сетей.

## <a name="signin"></a>1. Вход

Убедитесь, что вы установили последнюю версию командлетов PowerShell для Resource Manager. Дополнительные сведения об установке командлетов PowerShell см. в статье [Overview of Azure PowerShell](/powershell/azure/overview) (Обзор Azure PowerShell). Это важно, так как более ранние версии командлетов не содержат текущие значения, необходимые в этом сценарии.

1. Откройте консоль PowerShell с повышенными привилегиями и войдите в свою учетную запись Azure. Этот командлет запрашивает учетные данные входа. После выполнения входа он загружает параметры учетной записи, чтобы они были доступны в Azure PowerShell.

  ```powershell
  Connect-AzureRmAccount
  ```
2. Получите список подписок Azure.

  ```powershell
  Get-AzureRmSubscription
  ```
3. Укажите подписку, которую нужно использовать.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```

## <a name="rg"></a>2. Создание ресурсов

1. Создайте группу ресурсов.

  ```powershell
  New-AzureRmResourceGroup -Location "West US" -Name "testRG"
  ```
2. Создание виртуальной глобальной сети.

  ```powershell
  $virtualWan = New-AzureRmVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
  ```
3. Создание виртуального концентратора.

  ```powershell
  New-AzureRmVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
  ```

## <a name="connections"></a>3. Создание подключений

Создайте подключения косвенных виртуальных сетей периферийных зон и DMZ VNet к виртуальному концентратору.

  ```powershell
  $remoteVirtualNetwork1= Get-AzureRmVirtualNetwork -Name “indirectspoke1” -ResourceGroupName “testRG”
  $remoteVirtualNetwork2= Get-AzureRmVirtualNetwork -Name “indirectspoke2” -ResourceGroupName “testRG”
  $remoteVirtualNetwork3= Get-AzureRmVirtualNetwork -Name “dmzvnet” -ResourceGroupName “testRG”

  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection1” -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection2” -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection3” -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. Создание маршрута виртуального концентратора

Для этой статьи адресные пространства косвенных виртуальных сетей периферийных зон: 10.0.2.0/24 и 10.0.3.0/24, а частный IP-адрес сетевого интерфейса DMZ NVA — 10.0.4.5.

```powershell
$route1 = New-AzureRmVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. Создание таблицы маршрутов виртуального концентратора

Создайте таблицу маршрутов виртуального концентратора, а затем примените к ней созданный маршрут.
 
```powershell
$routeTable = New-AzureRmVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. Фиксация изменений

Зафиксируйте изменения в виртуальном концентраторе.

```powershell
Set-AzureRmVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub” -RouteTable $routeTable
```

## <a name="cleanup"></a>Очистка ресурсов

Вы можете удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы с помощью командлета [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Замените myResourceGroup на имя вашей группы ресурсов и выполните следующую команду PowerShell:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о Виртуальной глобальной сети см. в [этой статье](virtual-wan-about.md).