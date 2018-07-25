---
title: 'Руководство по публикации предложения "Приложения Azure: шаблон решения Azure"'
description: В этой статье описываются требования к публикации шаблона приложения в Marketplace.
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
ms.openlocfilehash: 44d081a0666aa37ec0bf8eeac540b7a7f4f4f904
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057886"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Руководство по публикации предложения "Приложения Azure: шаблон решения Azure"

Шаблоны решений являются одним из основных способов публикации решения в Azure Marketplace. Сведения в этом руководстве помогут понять требования к этому предложению. 

Ниже приведены предложения транзакций с развертыванием и выставлением счетов через Marketplace. Пользователь видит призыв к действию — "Получить сейчас".

Тип предложения "Приложение Azure: шаблон решения" используется, если в решении требуются дополнительные возможности автоматизации развертывания и настройки, недоступные обычной виртуальной машине. Вы можете автоматизировать подготовку одной или нескольких виртуальных машин с помощью шаблонов решения для приложений Azure. Вы также можете подготавливать ресурсы сети и хранилища. Тип предложения "Приложения Azure: шаблоны решений" предоставляет преимущества автоматизации для отдельных виртуальных машин и целых решений на основе IaaS.

## <a name="requirements-for-solution-templates"></a>Требования к шаблонам решений

|Требования |Сведения  |
|---------|---------|
|Выставление счетов и ценообразование    |  Ресурсы будут подготовлены в подписке Azure клиента. Транзакции, связанные с виртуальными машинами с оплатой по мере использования (PAYGO), будут проводиться через корпорацию Майкрософт, а выставление счетов будет осуществляться по подписке Azure клиента (PAYGO). 
Несмотря на то, что в случае использования собственной лицензии корпорация Майкрософт будет выставлять счета на расходы на инфраструктуру по подписке клиента, вы будете напрямую выставлять клиенту счета на лицензирование ПО.        |
|Совместимый с Azure виртуальный жесткий диск (VHD)    |   Виртуальные машины должны быть созданы на платформе Windows или Linux.<ul> <li>Дополнительные сведения о создании виртуального жесткого диска Linux см. в разделе "Создание виртуального жесткого диска, совместимого с Azure (для Linux)" по адресу [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#2-create-an-azure-compatible-vhd-linux-based).</li> <li>Дополнительные сведения о создании виртуального жесткого диска Windows см. в разделе "Создание виртуального жесткого диска, совместимого с Azure (на основе Windows)" по адресу [docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation#3-create-an-azure-compatible-vhd-windows-based).</li> </ul>      |



## <a name="next-steps"></a>Дальнейшие действия
Если это еще не сделано, 

- [зарегистрируйтесь](https://azuremarketplace.microsoft.com/sell) в Marketplace.

Если вы зарегистрированы и создаете предложение или работаете над существующим,

- [войдите на портал Cloud Partner](https://cloudpartner.azure.com), чтобы создать или завершить предложение.
