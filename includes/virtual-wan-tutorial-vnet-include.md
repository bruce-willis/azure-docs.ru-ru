---
title: включение файла
description: включение файла
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004191"
---
Если у вас еще нет виртуальной сети, вы можете быстро создать ее с помощью PowerShell. Кроме того, вы можете создать виртуальную сеть с помощью портала Azure.

* Убедитесь, что адресное пространство для создаваемой виртуальной сети не пересекается с диапазонами адресов для других виртуальных сетей, к которым вы хотите подключиться, или с вашими локальными адресными пространствами сети. 
* Если у вас уже есть виртуальная сеть, убедитесь, что она соответствует требуемым критериям и в ней нет виртуального сетевого шлюза.

Вы можете легко создать свою виртуальную сеть, нажав кнопку "Попробовать" в этой статье, чтобы открыть консоль PowerShell. Задайте значения, затем скопируйте и вставьте команды в окно консоли.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Настройте команды PowerShell, а затем создайте группу ресурсов.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>Создание виртуальной сети

Настройте команды PowerShell, чтобы создать виртуальную сеть, совместимую с вашей средой.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```