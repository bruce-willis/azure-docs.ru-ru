---
title: Замена диска ОС виртуальной машины Azure с помощью интерфейса командной строки | Документация Майкрософт
description: Изменение диска операционной системы, используемого виртуальной машиной Azure, с помощью интерфейса командной строки.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: 983c2e6d03735ba26f7660fc07dcf1a05ef88189
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960402"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-the-cli"></a>Изменение диска ОС виртуальной машины Azure с помощью интерфейса командной строки


Если у вас имеется виртуальная машина, но нужно заменить ее диск на резервный диск или другой диск ОС, для этого можно использовать Azure CLI. Нет необходимости удалять и повторно создавать виртуальную машину. Можно даже использовать управляемый диск в другой группе ресурсов, если он еще не используется.

Не нужно останавливать виртуальную машину или отменять ее распределение. Идентификатор ресурса управляемого диска может быть заменен идентификатором ресурса другого управляемого диска. 

Убедитесь, что тип и размер виртуальной машины совместимы с диском, который необходимо подключить. Например, если диск, который вы хотите использовать, размещен в хранилище уровня "Премиум", то виртуальная машина должна поддерживать это хранилище (например, она должна относиться к серии DS).

Для этой статьи требуется Azure CLI версии 2.0.25 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 


Выполните команду [az disk list](/cli/azure/disk#list) для получения списка дисков в группе ресурсов.

```azurecli-interactive
az disk list \
   -g myResourceGroupDisk \
   --query '[*].{diskId:id}' \
   --output table
```


Выполните команду [az vm stop](/cli/azure/vm#stop), чтобы остановить виртуальную машину или отменить ее распределение, прежде чем заменить диск.

```azurecli-interactive
az vm stop \
   -n myVM \
   -g myResourceGroup
```


Выполните команду [az vm update](/cli/azure/vm#az-vm-update), указав полный идентификатор ресурса нового диска в параметре `--osdisk`. 

```azurecli-interactive 
az vm update \
   -g myResourceGroup \
   -n myVM \
   --os-disk /subscriptions/<subscription ID>/resourceGroups/swap/providers/Microsoft.Compute/disks/myDisk 
   ```
   
Перезапустите виртуальную машину, выполнив команду [az vm start](/cli/azure/vm#start).

```azurecli-interactive
az vm start \
   -n myVM \
   -g myResourceGroup
```

   
**Дальнейшие действия**

Создание копии диска описывается в разделе [Моментальный снимок диска](snapshot-copy-managed-disk.md).