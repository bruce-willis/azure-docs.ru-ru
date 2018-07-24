---
title: Настройка пула емкости для Azure NetApp Files | Документация Майкрософт
description: Описание настройки пула емкости, чтобы иметь возможность создавать в нем тома.
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
ms.openlocfilehash: 0e9203f5b4e2a9043e242b804c82017cf6fc3ee1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010809"
---
# <a name="set-up-a-capacity-pool"></a>Настройка пула емкости
Настройка пула емкости позволяет создавать в нем тома.  

## <a name="before-you-begin"></a>Перед началом работы 
Должна быть создана учетная запись NetApp.   

[Создание учетной записи NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Действия 

1. Перейдите в колонку управления учетной записью NetApp, а затем в навигационной панели выберите **Пулы емкости**.

2. Нажмите **+ Добавить пулы** для создания нового пула емкости.   
    Появится окно New Capacity Pool (Новый пул емкости).

3. Укажите следующую информацию для нового пула емкости.  
  * **Имя**  
    Придумайте название для пула емкости.  
    Название пула емкости должно быть уникальным для каждой учетной записи NetApp.

  * **Уровень службы**   
    В этом поле отображается целевая производительность пула емкости.  
    В настоящее время доступен только уровень службы "Премиум". 

  *  **Размер**      
      Укажите приобретаемый размер пула емкости.        
      Минимальный размер пула емкости равен 4 ТиБ. Можно создавать пул с размером, кратным 4 ТиБ.   
      
      ![Новый пул емкости](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Последовательно выберите **ОК**.

## <a name="next-steps"></a>Дополнительная информация 

1. [Создание тома для Azure NetApp Files](azure-netapp-files-create-volumes.md)
2. [Настройка политики экспорта для тома (необязательно)](azure-netapp-files-configure-export-policy.md)

