---
title: Виртуальная сеть для служб Azure | Документация Майкрософт
description: Сведения о преимуществах развертывания ресурсов в виртуальной сети. Ресурсы в виртуальной сети могут взаимодействовать друг с другом и локальным ресурсом, не передавая трафик через Интернет.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial
ms.openlocfilehash: 163a8f318608ba980d98209277371fb4d148c4ff
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902779"
---
# <a name="virtual-network-integration-for-azure-services"></a>Интеграция виртуальной сети для служб Azure

Интеграция служб Azure с виртуальной сетью Azure обеспечивает закрытый доступ к службе из виртуальных машин или вычислительных ресурсов в виртуальной сети.
Вы можете интегрировать службы Azure в виртуальной сети со следующими параметрами: непосредственное развертывание выделенных экземпляров службы в виртуальной сети. В этом случае закрытый доступ к этим службам сможет осуществляться в виртуальной сети и из локальных сетей.
Расширение виртуальной сети за счет добавления конечной точки службы. Конечные точки позволяют защитить ресурсы отдельной службы в виртуальной сети.

Чтобы интегрировать несколько служб Azure с виртуальной сетью, можно использовать одну или объединить несколько указанных выше схем. Например, вы можете развернуть в виртуальной сети службу HDInsight и защитить учетную запись хранения в подсети HDInsight с помощью конечных точек службы.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Развертывание служб Azure в виртуальных сетях

При развертывании выделенных служб Azure в [виртуальной сети](virtual-networks-overview.md) вы можете взаимодействовать с ресурсами служб в частном порядке через частные IP-адреса.

![Службы, развернутые в виртуальной сети](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Развертывание служб в виртуальной сети обеспечивает следующие возможности:

- Ресурсы в виртуальной сети могут взаимодействовать друг с другом в частном порядке через частные IP-адреса, например непосредственно передавать данные между службой HDInsight и сервером SQL Server, запущенным на виртуальной машине в виртуальной сети.
- Локальные ресурсы могут обращаться к ресурсам в виртуальной сети с помощью частных IP-адресов через [VPN-подключение типа "сеть —сеть" (VPN-шлюз)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) или [канал ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Виртуальные сети могут создавать [пиринговую связь](virtual-network-peering-overview.md), что позволяет ресурсам в виртуальной сети взаимодействовать друг с другом через частные IP-адреса.
- Служба Azure полностью управляет экземплярами служб в виртуальной сети. Это позволяет отслеживать работоспособность экземпляров и выполнять необходимое масштабирование на основе нагрузки.
- Экземпляры служб развертываются в подсети в виртуальной сети. Входящий и исходящий доступ к подсети должен быть открыт посредством [групп безопасности сети](security-overview.md#network-security-groups) для подсети согласно рекомендациям для служб.
- При необходимости для служб можно использовать [делегированную подсеть](virtual-network-manage-subnet.md#add-a-subnet), чтобы явно указать, что в этой подсети может размещаться определенная служба. Делегирование подсети прямо разрешает службе создавать в подсети необходимые для этой службы ресурсы.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Службы, которые можно развернуть в виртуальной сети

|Категория|Service|
|-|-|
| Службы вычислений | Виртуальные машины: [Windows](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [Linux](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Масштабируемые наборы виртуальных машин](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Облачная служба](https://msdn.microsoft.com/library/azure/jj156091): только виртуальная сеть (классическая).<br/> [Пакетная служба Azure](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)  |
| Сеть | [Шлюз приложений — WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN-шлюз](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Брандмауэр Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Виртуальные сетевые модули](/windowsserverdocs/WindowsServerDocs/networking/sdn/manage/Use-Network-Virtual-Appliances-on-a-VN.md) 
|Данные|[Кэш Redis](../redis-cache/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Управляемый экземпляр базы данных SQL Azure](../sql-database/sql-database-managed-instance-vnet-configuration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Удостоверение | [Доменные службы Azure Active Directory](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |
| Контейнеры | [Служба Azure Kubernetes (AKS)](../aks/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Экземпляр контейнера Azure (ACI)](http://www.aka.ms/acivnet)<br/>[Обработчик службы контейнеров Azure](https://github.com/Azure/acs-engine) с [подключаемым модулем](https://github.com/Azure/acs-engine/tree/master/examples/vnet) CNI для виртуальной сети Azure||
| Web | [Управление API](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Среда службы приложений](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>
<br/>


## <a name="service-endpoints-for-azure-services"></a>Конечные точки служб Azure

Некоторые службы Azure невозможно развернуть в виртуальной сети. Вы можете предоставить доступ к некоторым ресурсам службы только определенным подсетям виртуальной сети, включив конечные точки виртуальной сети.  Дополнительные сведения о конечных точках службы виртуальной сети и службах, для которых можно создать конечные точки, см. [здесь](virtual-network-service-endpoints-overview.md).
