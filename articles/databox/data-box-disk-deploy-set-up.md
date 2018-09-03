---
title: Настройка диска Microsoft Azure Data Box | Документация Майкрософт
description: Используйте это руководство, чтобы узнать, как настроить диск Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/28/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6fcc7823a7e2f2f1e280622a1fa05d4417a71546
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143488"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Руководство. Распаковка, подключение и разблокировка диска Azure Data Box

Это руководство описывает, как распаковать, подключить и разблокировать диск Azure Data Box.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Распаковка диска Data Box.
> * Подключение и разблокировка диска Data Box.

## <a name="prerequisites"></a>Предварительные требования

Перед тем как начать, убедитесь в следующем.

1. Вы изучили [Tutorial: Order an Azure Data Box Disk (Preview)](data-box-disk-deploy-ordered.md) (Руководство. Заказ диска Azure Data Box (предварительная версия)).
2. Состояние ваших дисков и заданий на портале обновилось до **Доставлено**.
3. У вас имеется главный компьютер, на котором можно установить средство разблокировки диска Data Box. На главном компьютере должно быть следующее ПО:
    - [поддерживаемая операционная система](data-box-disk-system-requirements.md);
    - [установлен Windows PowerShell 4](https://www.microsoft.com/download/details.aspx?id=40855);
    - [установлена платформа .NET Framework 4.5.1](https://www.microsoft.com/download/details.aspx?id=30653);
    - [включено шифрование диска BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server);
    - [установлен пакет Windows Management Framework 4](https://www.microsoft.com/en-us/download/details.aspx?id=40855). 

## <a name="unpack-your-disks"></a>Распаковка дисков

 Выполните следующие действия для распаковки дисков.

1. Диски Data Box отправляются в небольшой транспортировочной коробке. Откройте коробку и удалите ее содержимое. Убедитесь, что в коробке имеется 1–5 твердотельных дисков (SSD) и соединительный кабель USB для каждого диска. Осмотрите коробку на наличие каких-либо доказательств незаконного изменения или любых других очевидных повреждений. 

    ![Транспортировочная упаковка диска Data Box](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Если транспортировочная коробка имеет следы незаконного изменения или серьезно повреждена, не открывайте ее. Обратитесь в службу поддержки Майкрософт, чтобы она помогла оценить, находятся ли диски в хорошем состоянии или их необходимо заменить.
3. Убедитесь, что коробка имеет прозрачную упаковку, содержащую транспортную этикетку (в соответствие с действующей маркировкой) для обратной отправки. Если эта этикетка утеряна или повреждена, всегда можно загрузить и распечатать новую с портала Azure. 

    ![Транспортировочная этикетка диска Data Box](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Сохраните коробку и упаковочный пеноматериал на случай обратной отправки дисков.

## <a name="connect-and-unlock-your-disks"></a>Подключение и разблокировка дисков

Выполните следующие действия для подключения и разблокировки дисков.

1. Используйте прилагаемый кабель для подключения диска к компьютеру Windows с поддерживаемой ОС, как указано в предварительных требованиях. 

    ![Подключение диска Data Box](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. На портале Azure перейдите в раздел **Общие > Сведения об устройстве**. 
3. Нажмите кнопку **Download Data Box Disk unlock tool** (Установить средство разблокировки диска Data Box). 

    ![Загрузка средства разблокировки для диска Data Box](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. Извлеките средство на тот же компьютер, который будет использоваться для копирования данных.
5. Откройте окно командной строки или запустите Windows PowerShell от имени администратора на том же компьютере.
6. (Необязательно). Чтобы убедиться, что компьютер, используемый для разблокировки диска, соответствует требованиям операционной системы, выполните команду проверки системы. Результат выполнения команды показан ниже. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. На портале Azure перейдите в раздел **Общие > Сведения об устройстве**. Используйте значок копирования, чтобы скопировать ключ доступа.
8. Кликните `DataBoxDiskUnlock.exe` и укажите ключ доступа. Отобразится буква диска, присвоенная диску. Результат выполнения команды показан ниже.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. Повторите шаги 6–8 для любых будущих подключений дисков. Используйте команду справки, если требуется помощь со средством разблокировки диска Data Box.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. Когда диск будет разблокирован, можно просмотреть содержимое диска.    

    ![Содержимое диска Data Box](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве раскрыты следующие сведения о диске Azure Data Box.

> [!div class="checklist"]
> * Распаковка диска Data Box.
> * Подключение и разблокировка диска Data Box


Перейдите к следующему руководству, чтобы узнать, как копировать данные на диск Data Box.

> [!div class="nextstepaction"]
> [Tutorial: Copy data to Azure Data Box Disk and verify](./data-box-disk-deploy-copy-data.md) (Руководство. Копирование данных на диск Data Box)

