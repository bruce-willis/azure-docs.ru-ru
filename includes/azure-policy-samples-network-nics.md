---
title: включение файла
description: включение файла
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 841b95e3e1cff09a15b4158bc55d46f782d32d41
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47003906"
---
### <a name="network-interfaces"></a>Сетевые интерфейсы

|  |  |
|---------|---------|
| [Конкретная группа безопасности сети на каждой сетевой карте](../articles/governance/policy/samples/nsg-on-nic.md) | Требует, чтобы определенная группа безопасности сети использовалась для каждого виртуального сетевого интерфейса. Вы можете указать идентификатор требуемой группы безопасности сети. |
| [Использование утвержденной подсети для сетевых интерфейсов виртуальной машины](../articles/governance/policy/samples/use-approved-subnet-vm-nics.md) | Требует, чтобы сетевые интерфейсы использовали утвержденную подсеть. Нужно указать идентификатор утвержденной подсети. |
| [Использование утвержденной виртуальной сети для сетевых интерфейсов виртуальной машины](../articles/governance/policy/samples/use-approved-vnet-vm-nics.md) | Требует, чтобы сетевые интерфейсы использовали утвержденную виртуальную сеть. Вы можете указать идентификатор утвержденной виртуальной сети. |