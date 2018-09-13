---
title: Устранение неполадок дисков, подключенных к виртуальным машинам Azure | Документация Майкрософт
description: Ссылки на ресурсы по устранению неполадок с виртуальными жесткими дисками виртуальной машины Azure.
services: storage
author: genlin
ms.service: storage
ms.topic: article
ms.date: 05/01/2018
ms.author: genli
ms.openlocfilehash: 81db3a819c28aab8f4b644a940eeb5e6c5ecf3d6
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307178"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Устранение неполадок дисков, подключенных к виртуальным машинам Azure 

В виртуальных машинах Azure для диска операционной системы и всех подключенных дисков данных используются виртуальные жесткие диски (VHD). VHD хранятся в одной или нескольких учетных записях хранения Azure как страничные BLOB-объекты. В этой статье приведены указания по устранению распространенных неполадок с VHD. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Устранение неполадок с удалением ресурсов хранилища виртуальной машины

В некоторых случаях может возникнуть ошибка при удалении ресурса хранилища, если виртуальная машина в развертывании Resource Manager содержит подключенные VHD. Помощь в устранении этой проблемы можно получить, ознакомившись с одной из следующих статей: 

  * Для виртуальных машин Linux: [Ошибки удаления хранилища в развертывании Resource Manager](../../virtual-machines/linux/storage-resource-deletion-errors.md).  
  * Для виртуальных машин Windows: [Ошибки удаления хранилища в развертывании Resource Manager](../../virtual-machines/windows/storage-resource-deletion-errors.md).  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Устранение непредвиденных перезагрузок виртуальных машин с подключенными VHD

Если происходят непредвиденные перезагрузки виртуальной машины с большим числом подключенных VHD, ознакомьтесь со следующими статьями:

  * Для виртуальных машин Linux: [Непредвиденные перезагрузки виртуальных машин с подключенными VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md).
  * Для виртуальных машин Windows: [Непредвиденные перезагрузки виртуальных машин с подключенными VHD](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md).
