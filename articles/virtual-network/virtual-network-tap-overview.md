---
title: Обзор TAP виртуальной сети Azure | Документы Майкрософт
description: Подробная информация о TAP виртуальной сети. TAP виртуальной сети предоставляет вам более подробную копию сетевого трафика виртуальной машины, который можно передавать в сборщик пакетов.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kaanan
ms.openlocfilehash: 7270ab6203cfa3602fc36bc6fa7d30cd622ce3a3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946601"
---
# <a name="virtual-network-tap"></a>TAP виртуальной сети

TAP (точка доступа к терминалу) виртуальной сети Azure позволяет непрерывно передавать сетевой трафик виртуальной машины в сборщик сетевых пакетов или средство аналитики. Сборщик сетевых пакетов или средство аналитики предоставляются партнером-разработчиком [сетевого виртуального устройства](https://azure.microsoft.com/solutions/network-appliances/). Список партнерских решений, работающих с TAP виртуальной сети, см. в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions).

> [!IMPORTANT]
> Сейчас TAP виртуальной сети находится на этапе предварительной версии для разработчиков и доступна только в западно-центральной части США. Чтобы использовать TAP виртуальной сети, необходимо зарегистрироваться в предварительной версии, отправив по адресу <azurevnettap@microsoft.com> электронное письмо со своим идентификатором подписки. После активации подписки на электронную почту будет отправлено письмо. Вы не сможете использовать функцию, пока не получите подтверждение по электронной почте. Предварительная версия для разработчиков предоставляется без соглашения об уровне обслуживания и не должна использоваться для производственных рабочих нагрузок. Некоторые функции могут не поддерживаться, иметь ограничения и быть доступными не во всех расположениях Azure. См. [дополнительные условия использования для предварительных версий Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="virtual-network-tap-partner-solutions"></a>Партнерские решения TAP виртуальной сети

### <a name="network-packet-brokers"></a>Брокеры сетевых пакетов

- [Big Switch Big Monitoring Fabric](https://www.bigswitch.com/products/big-monitoring-fabric/public-cloud/microsoft-azure)
- [Flowmon](https://www.flowmon.com/blog/azure-vtap)
- [Gigamon GigaSECURE](https://blog.gigamon.com/2018/09/13/why-microsofts-new-vtap-service-works-even-better-with-gigasecure-for-azure)
- [Ixia CloudLens](https://www.ixiacom.com/cloudlens/cloudlens-azure)

### <a name="security-analytics-networkapplication-performance-management"></a>Аналитика безопасности и управления производительностью сети или приложений

- [ExtraHop Reveal(x)](https://www.extrahop.com/company/tech-partners/microsoft/)
- [Fidelis Cybersecurity](https://www.fidelissecurity.com/technology-partners/microsoft-azure )
- [Netscout vSTREAM]( https://www.netscout.com/technology-partners/microsoft/azure-vtap)
- [Nubeva Prisms](https://www.nubeva.com/azurevtap)
- [Платформа RSA NetWitness®](https://www.rsa.com/azure)
- [Vectra Cognito](https://vectra.ai/microsoftazure)

Ниже показано, как работает TAP виртуальной сети. Конфигурацию TAP можно добавить в [сетевой интерфейс](virtual-network-network-interface.md), присоединенный к виртуальной машине, развернутой в вашей виртуальной сети. Местом назначения станет IP-адрес виртуальной сети в той же виртуальной сети, что и отслеживаемый сетевой интерфейс или [одноранговая виртуальная сеть](virtual-network-peering-overview.md). Для обеспечения высокой доступности решение сборщика для TAP виртуальной сети можно развернуть за [внутренней подсистемой балансировки нагрузки Azure](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#concepts). Инструкции по оценке параметров развертывания отдельного решения см. в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions).

![Как работает TAP виртуальной сети](./media/virtual-network-tap/architecture.png)

## <a name="prerequisites"></a>Предварительные требования

Перед созданием TAP виртуальной сети вы должны были получить письмо с подтверждением вашей регистрации в предварительной версии и создать одну или несколько виртуальных машин, используя модель развертывания [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) и партнерское решение для объединения трафика TAP в западно-центральной части США. Если в вашей виртуальной сети нет ни одного из партнерских решений, разверните одно из них, как указано в разделе о [партнерских решениях](#virtual-network-tap-partner-solutions). Один и тот же ресурс TAP виртуальной сети позволяет объединять трафик из разных сетевых интерфейсов в одной и той же или разных подписках. Если отслеживаемые сетевые интерфейсы находятся в разных подписках, обе подписки должны быть связаны с одним клиентом Azure Active Directory. Кроме того, отслеживаемые сетевые интерфейсы и конечная точка назначения для объединения трафика TAP могут находиться в одноранговых виртуальных сетях в одном и том же регионе. Если вы используете эту модель развертывания, перед настройкой TAP виртуальной сети убедитесь, что [пиринг виртуальных сетей](virtual-network-peering-overview.md) включен.

## <a name="permissions"></a>Разрешения

Учетные записи, используемые для применения конфигурации TAP к сетевым интерфейсам, должны быть назначены роли [участник сети](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) или [пользовательской роли](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json), которой назначены необходимые действия, перечисленные в следующей таблице:

| Действие | ИМЯ |
|---|---|
| Microsoft.Network/virtualNetworkTaps/* | Требуется для создания, обновления, чтения и удаления ресурса TAP виртуальной сети |
| Microsoft.Network/networkInterfaces/read | Требуется для чтения ресурса сетевого интерфейса, в котором будет настраиваться TAP |
| Microsoft.Network/tapConfigurations/* | Требуется для создания, обновления, чтения и удаления конфигурации TAP в сетевом интерфейсе |

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о [создании TAP виртуальной сети](tutorial-tap-virtual-network-cli.md).
