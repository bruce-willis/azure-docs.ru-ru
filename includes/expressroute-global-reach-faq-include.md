---
title: включение файла
description: включение файла
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 09/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b9c4cf6c90ef5507b318b4f13afb982aab151c79
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48874110"
---
### <a name="what-is-expressroute-global-reach"></a>Что такое ExpressRoute Global Reach?

ExpressRoute Global Reach — это служба Azure, которая соединяет локальные сети с помощью службы ExpressRoute через глобальную сеть Майкрософт. Например, если у вас есть один частный центр обработки данных в Калифорнии с подключением к ExpressRoute в Кремниевой долине и другой частный центр обработки данных в Техасе с подключением к ExpressRoute в Далласе, с помощью службы ExpressRoute Global Reach можно подключить эти два центра друг к другу через два канала ExpressRoute, и трафик между ними будет проходить через магистральную сеть Майкрософт.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Как включить или отключить ExpressRoute Global Reach?

ExpressRoute Global Reach включается путем соединения каналов ExpressRoute между собой. Чтобы отключить эту функцию, следует разъединить каналы. См. конфигурацию.

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Требуется ли ExpressRoute (цен. категория "Премиум") для ExpressRoute Global Reach?

Если каналы ExpressRoute находятся в одном геополитическом регионе, для их соединения ExpressRoute (цен. категория "Премиум") не требуется. Если два канала ExpressRoute находятся в разных геополитических регионах, чтобы обеспечить соединение между ними, ExpressRoute (цен. категория "Премиум") потребуется для обоих каналов. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Как будет тарифицироваться ExpressRoute Global Reach?

ExpressRoute обеспечивает возможность подключения из вашей локальной сети к облачным службам Майкрософт. ExpressRoute Global Reach позволяет соединять локальные сети между собой через с помощью существующих каналов ExpressRoute, используя глобальную сеть корпорации Майкрософт. Счета за ExpressRoute Global Reach выставляются отдельно от счетов за службу ExpressRoute. Взимается дополнительная плата за включение этой функции для каждого канала ExpressRoute. Счета за трафик между вашими локальными сетями, который обеспечивает ExpressRoute Global Reach, будут выставляться по исходящему тарифу на стороне источника и по входящему тарифу на стороне назначения. Тарифы зависят от зоны, в которой расположены каналы. См. <pricing page>.

### <a name="where-is-expressroute-global-reach-supported"></a>В каких регионах поддерживается ExpressRoute Global Reach?

ExpressRoute Global Reach поддерживается в следующих странах. Каналы ExpressRoute необходимо создавать в расположениях пиринга в этих странах.

* Австралия
* Гонконг, САР
* Ирландия
* Япония
* Нидерланды
* Великобритания
* США

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>У меня более двух локальных сетей, каждая из которых подключена к каналу ExpressRoute. Можно ли с помощью ExpressRoute Global Reach соединить все мои локальные сети вместе?

Да, это возможно, если каналы расположены в поддерживаемых странах. Необходимо одновременно подключить два канала ExpressRoute. Чтобы создать полносвязную сетевую топологию, необходимо перечислить все пары каналов и повторно настроить конфигурацию. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Можно ли включить ExpressRoute Global Reach между двумя каналами ExpressRoute в одном расположении пиринга?

Нет. Два канала должны находиться в разных расположениях пиринга. Если городской регион в поддерживаемой стране содержит несколько расположений пиринга ExpressRoute, можно соединить каналы ExpressRoute, созданные в разных расположениях пиринга в этом регионе. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Если включить ExpressRoute Global Reach между каналами X и Y, а также между каналами Y и Z, смогут ли мои локальные сети, подключенные к каналам X и Z, обмениваться данными между собой через сеть Майкрософт?

Нет. Для обеспечения связи между любыми двумя из ваших локальных сетей необходимо явным образом соединить соответствующие каналы ExpressRoute. В приведенном выше примере необходимо соединить канал X с каналом Z. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Какую пропускную способность сети можно ожидать между моими локальными сетями после включения ExpressRoute Global Reach?

Пропускная способность сети между локальными сетями, для которых включена служба ExpressRoute Global Reach, ограничивается меньшим из двух каналов ExpressRoute.
