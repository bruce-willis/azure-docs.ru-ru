---
title: 'Руководство по публикации предложения "Приложения Azure: управляемое приложение"'
description: В этой статье описываются требования к публикации управляемого приложения в Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: c8ead3dc34faefce0f113dee2074960fddfa11a1
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43144597"
---
# <a name="azure-applications-managed-application-offer-publishing-guide"></a>Руководство по публикации предложения "Приложения Azure: управляемое приложение"

Управляемое приложение является одним из основных способов публикации решения в Azure Marketplace. Сведения в этом руководстве помогут понять требования к этому предложению. 

Ниже приведены предложения транзакций с развертыванием и выставлением счетов через Marketplace. Пользователь видит призыв к действию — "Получить сейчас".

Тип предложения "Приложение Azure: управляемое приложение" используется в описанных ниже условиях.
- Вы развертываете решение на основе подписки для пользователя, применяя либо виртуальную машину, либо целое решение на основе IaaS.
- Вам или вашему клиенту требуется, чтобы решением управлял партнер.

>[!NOTE]
>Например, партнером может быть SI или поставщик управляемой службы (MSP).  

## <a name="managed-application-offer"></a>Предложение "Управляемое приложение"

|Требования |Сведения  |
|---------|---------|
|Развернуто в подписке Azure клиента | Управляемые приложения должны быть развернуты в подписке клиента и могут управляться третьей стороной | 
|Выставление счетов и ценообразование    |  Ресурсы будут подготовлены в подписке Azure клиента. Транзакции, связанные с виртуальными машинами с оплатой по мере использования (PAYGO), будут проводиться через корпорацию Майкрософт, а выставление счетов будет осуществляться по подписке Azure клиента (PAYGO). 
Несмотря на то, что в случае использования собственной лицензии корпорация Майкрософт будет выставлять счета на расходы на инфраструктуру по подписке клиента, вы будете напрямую выставлять клиенту счета на лицензирование ПО.        |
|Совместимый с Azure виртуальный жесткий диск (VHD)    |   Виртуальные машины должны быть созданы на платформе Windows или Linux.<ul> <li>Дополнительные сведения о создании виртуального жесткого диска Linux см. в разделе "Создание виртуального жесткого диска, совместимого с Azure (для Linux)" по адресу [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Дополнительные сведения о создании виртуального жесткого диска Windows см. в разделе "Создание виртуального жесткого диска, совместимого с Azure (на основе Windows)" по адресу [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |

>[!NOTE]
> Управляемые приложения должны быть развернуты в Marketplace. Если вас беспокоит общение с пользователями, то следует связываться с заинтересованными клиентам после включения общего доступа к данным о потенциальных клиентах.  


## <a name="next-steps"></a>Дальнейшие действия
Если это еще не сделано, 

- [зарегистрируйтесь](https://azuremarketplace.microsoft.com/sell) в Marketplace.

Если вы зарегистрированы и создаете предложение или работаете над существующим,

- [войдите на портал Cloud Partner](https://cloudpartner.azure.com), чтобы создать или завершить предложение.
