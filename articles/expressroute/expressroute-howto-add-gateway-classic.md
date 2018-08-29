---
title: Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell в Azure (классическая модель) | Документация Майкрософт
description: Узнайте, как настроить шлюз виртуальной сети для виртуальной сети на основе классической модели развертывания с помощью командлетов PowerShell для настройки ExpressRoute.
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: ''
tags: azure-service-management
ms.assetid: 85ee0bc1-55be-4760-bfb4-34d9f2c96f30
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: charwen
ms.openlocfilehash: ec7e4a326bc0d5e9393db28c83c9d859a93dc8a3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146996"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell-classic"></a>Настройка шлюза виртуальной сети для ExpressRoute с помощью PowerShell (классическая модель)
> [!div class="op_single_selector"]
> * [Resource Manager — PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Классическая модель: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Видео — портал Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Эта статья содержит инструкции по добавлению, изменению размера и удалению шлюза виртуальной сети для существующей виртуальной сети. Приведенные действия по настройке предназначены для виртуальных сетей, созданных с помощью **классической модели развертывания**, которые будут использоваться в конфигурации ExpressRoute. 

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**О моделях развертывания Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="before-beginning"></a>Подготовка
Убедитесь, что вы установили командлеты Azure PowerShell, необходимые для этой конфигурации (1.0.2 или более поздней версии). Если вы еще не установили эти командлеты, необходимо сделать это перед началом настройки. Дополнительную информацию об установке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

[!INCLUDE [expressroute-gateway-classic-ps](../../includes/expressroute-gateway-classic-ps-include.md)]

## <a name="next-steps"></a>Дополнительная информация
После создания шлюза виртуальной сети вы можете связать виртуальную сеть с каналом ExpressRoute. Ознакомьтесь со статьей [Связывание виртуальной сети с каналом ExpressRoute](expressroute-howto-linkvnet-classic.md).

