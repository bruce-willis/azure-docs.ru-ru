---
title: Настройка политики экспорта для тома Azure NetApp Files | Документация Майкрософт
description: Описание настройки политики экспорта для управления доступом к тому Azure NetApp Files
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
ms.openlocfilehash: 08de7e2ca8cd993a0931f5b16cb9d6c9a04e53dc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010962"
---
# <a name="configure-export-policy-for-a-volume-optional"></a>Настройка политики экспорта для тома (необязательно)

При необходимости можно настроить политику экспорта для управления доступом к тому Azure NetApp Files. 

## <a name="steps"></a>Действия 

1.  Нажмите колонку **Создать политику экспорта** в колонке "Управление томом". 

2.  Чтобы создать правило политики экспорта, заполните следующие поля.   
    *  **Индекс**   
        Укажите номер индекса для правила.  
        Политика экспорта может содержать до пяти правил. Правила вычисляются в соответствии с их номером индекса в списке. Правила с более низкими номерами индекса вычисляются первыми. Например, сначала вычисляется правило с номером индекса 1, а затем правило с номером индекса 2. 

    * **Разрешенные клиенты**   
        Укажите значение в одном из следующих форматов.  
        * IPv4-адрес, например `10.1.12.24` 
        * IPv4-адрес и маска подсети, выраженная количеством бит, например `10.1.12.10/4`

    * **Access**  
        Выберите одно из следующих типов доступа.  
        * Нет доступа 
        * Чтение и запись
        * Только чтение

    * **Протоколы**   
        Укажите протокол, используемый для экспорта политики.   
        В настоящее время Azure NetApp Files поддерживает только протокол NFSv3.

    ![Экспортировать политику](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Дополнительная информация 
* [Управление томами](azure-netapp-files-manage-volumes.md)
* [Подключение и отключение тома для виртуальных машин](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Управление моментальными снимками](azure-netapp-files-manage-snapshots.md)
