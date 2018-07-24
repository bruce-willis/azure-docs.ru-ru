---
title: Общие сведения об иерархии хранилища Azure NetApp Files | Документация Майкрософт
description: Описание иерархии хранилища, включая учетные записи Azure NetApp Files, пулов емкости и томов.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 6f5ed4e7ede9a098d69b7a40f44dd60f9b400472
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010996"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Общие сведения об иерархии хранилища Azure NetApp Files

Перед созданием тома в Azure NetApp Files необходимо приобрести и настроить пул для подготовленной емкости.  Чтобы настроить емкость пула, необходимо иметь учетную запись NetApp. Понимание иерархии хранилища помогает настроить и управлять ресурсами Azure NetApp Files.

## <a name="azure_netapp_files_account"></a>Учетные записи NetApp

- Учетная запись NetApp служит в качестве административного группирования составных емкостей пулов.  
- Учетная запись NetApp отличается от учетной записи хранения Azure. 
- Учетная запись NetApp является региональной областью.   
- Можно иметь несколько учетных записей NetApp в регионе, но каждая учетная запись NetApp привязана только к одному региону.

## <a name="capacity_pools"></a>Пулы емкости

- Емкость пула определяется по его производительности.  
- Емкость предоставляется программами фиксированных номеров SKU, которые были приобретены (например, емкостью 4 ТБ).
- Пул емкости может содержать только один уровень службы.  
  В настоящее время доступен только уровень службы "Премиум".
- Каждый пул емкости принадлежит только одной учетной записи NetApp.  
- Пул емкости нельзя перемещать между различными учетными записями NetApp.   
  Например, в [концептуальной схеме иерархии хранения](#conceptual_diagram_of_storage_hierarchy) ниже, Capacity Pool 1 невозможно переместить из учётной записи NetApp восточной части США в учётную запись NetApp западной части США 2.  

## <a name="volumes"></a>Тома

- Том измеряется с использованием логической емкости и масштабируется до 100 ТБ в одном томе.
- Потребление емкости тома зависит от подготовленной емкости пула.
- Каждый том принадлежит только одному пулу, но пул может содержать несколько томов. 
- В пределах той же учетной записи NetApp можно перемещать том по пулах.    
  Например, в [концептуальной схеме иерархии хранения](#conceptual_diagram_of_storage_hierarchy) ниже, можно переместить тома из Capacity Pool 1 к Capacity Pool 2.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Концептуальная схема иерархии хранения 
В следующем примере показаны связи подписки Azure, учетных записей NetApp, пулов емкости и томов.   

![Концептуальная схема иерархии хранения](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Дополнительная информация

1. [Создание учетной записи NetApp](azure-netapp-files-create-netapp-account.md)
2. [Настройка пула емкости](azure-netapp-files-set-up-capacity-pool.md)
3. [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
4. [Настройка политики экспорта для тома (необязательно)](azure-netapp-files-configure-export-policy.md)