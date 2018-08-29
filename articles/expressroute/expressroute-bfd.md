---
title: Настройка обнаружения двунаправленной передачи через ExpressRoute | Документация Майкрософт
description: Этот документ содержит инструкции по настройке обнаружения двунаправленной передачи через частный пиринг по каналу ExpressRoute.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 8/17/2018
ms.author: rambala
ms.openlocfilehash: acac0a866d1f494830be45c23ebca6844431c9de
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40225449"
---
# <a name="configure-bfd-over-expressroute"></a>Настройка обнаружения двунаправленной передачи через ExpressRoute

ExpressRoute поддерживает обнаружение двунаправленной передачи (BFD) через частный пиринг. Включив BFD через ExpressRoute, можно ускорить обнаружение сбоя связи между устройствами Microsoft Enterprise Edge (MSEE) и маршрутизаторами, на которых завершается канал ExpressRoute (PE). Вы можете завершить ExpressRoute через клиентские или партнерские пограничные устройства маршрутизации (если вы используете управляемые подключения третьего уровня). В этом документе рассматривается необходимость в BFD и включение BFD через ExpressRoute.

## <a name="need-for-bfd"></a>Необходимость обнаружения двунаправленной передачи

На следующей схеме показаны преимущества включения BFD через канал ExpressRoute: [![1]][1]

Вы можете установить канал ExpressRoute, используя подключения второго уровня или управляемые подключения третьего уровня. В любом случае, если в пути подключения ExpressRoute есть одно или несколько устройств уровня 2, ответственность за обнаружение любых сбоев связи на этом пути лежит на BGP.

На устройствах MSEE для параметров проверки активности и времени удержания для BGP обычно настроены значения 60 и 180 секунд соответственно. Таким образом, после сбоя связи для его обнаружения и переключения трафика на альтернативное подключение потребуется около трех минут.

Таймеры BGP можно контролировать, настроив для BGP более низкое значение проверки активности и времени удержания на пограничном пиринговом устройстве клиента. Если таймеры BGP не совпадают между двумя пиринговыми устройствами, сеанс BGP между одноранговыми узлами будет использовать меньшее значение таймера. Минимальное значение проверки активности для BGP — три секунды, а наименьшее время удержания — десятки секунд. Тем не менее настройка таких значений для таймеров BGP менее предпочтительна, так как протокол активно потребляет ресурсы.

BFD может помочь в этом сценарии. BFD обеспечивает обнаружение сбоя связи с минимальными затратами в течение доли секунды. 


## <a name="enabling-bfd"></a>Включение BFD

BFD настроен по умолчанию во всех новых частных пиринговых интерфейсах ExpressRoute, созданных на устройствах MSEE. Таким образом, чтобы включить BFD, необходимо настроить его на ваших пограничных маршрутизаторах. Настройка BFD — двухэтапный процесс. Вам нужно настроить BFD в интерфейсе, а затем связать его с сеансом BGP.

Ниже приведен пример конфигурации пограничного маршрутизатора (используется Cisco IOS XE). 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Чтобы включить BFD в существующей частной пиринговой связи, необходимо выполнить сброс пиринга. Дополнительные сведения о сбросе пиринга для канала ExpressRoute см. [здесь][ResetPeering].
>

## <a name="bfd-timer-negotiation"></a>Согласование таймера BFD

Между одноранговыми узлами BFD скорость передачи определяет более медленный узел. Для интервалов передачи и получения BFD для MSEE установлено значение 300 миллисекунд. Настроив более высокие значения, вы можете принудительно увеличить длительность этих интервалов, но сделать их короче нельзя.

>[!NOTE]
>Если вы настроили геоизбыточные частные пиринговые каналы ExpressRoute или использовали VPN-подключение IPSec типа "сеть — сеть" в качестве резервного для частного пиринга ExpressRoute, включение BFD через частный пиринг помогает быстрее переключиться после сбоя подключения ExpressRoute. 
>

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения доступны в следующих источниках:

- [Создание и изменение канала ExpressRoute][CreateCircuit]
- [Создание и изменение маршрутизации для канала ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD ускоряет время обнаружения сбоя связи"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-howto-reset-peering






