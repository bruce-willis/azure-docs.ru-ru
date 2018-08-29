---
title: Резервное копирование для Azure Stack | Документация Майкрософт
description: Выполните резервное копирование по требованию для Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2018
ms.locfileid: "41947756"
---
# <a name="back-up-azure-stack"></a>Резервное копирование для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Выполните резервное копирование по требованию для Azure Stack. Инструкции по настройке среды PowerShell приведены в разделе [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md). Чтобы войти в Azure Stack, см. [Использование портала администрирования в Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-backup"></a>Запуск резервного копирования Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Запуск нового резервного копирования без отслеживания хода выполнения задания
Используйте команду Start-AzSBackup для немедленного запуска нового резервного копирования без отслеживания прогресса выполнения задания.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Запуск резервного копирования Azure Stack с отслеживанием хода выполнения задания
Запустите новое резервное копирование с помощью команды Start-AzSBackup с переменной -AsJob для отслеживания хода выполнения создания резервной копии.

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>Подтверждение завершения резервного копирования

### <a name="confirm-backup-has-completed-using-powershell"></a>Подтверждение завершения резервного копирования с помощью PowerShell
Используйте следующие команды PowerShell, чтобы убедиться, что резервное копирование успешно завершено:

```powershell
   Get-AzsBackup
```

В результате должно отобразиться следующее:

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>Подтверждение завершения резервного копирования с помощью портала администрирования
Используйте портал администрирования Azure Stack для проверки успешного завершения резервного копирования, выполнив следующие шаги:

1. Откройте [портал администрирования Azure Stack](azure-stack-manage-portals.md).
2. Выберите **Дополнительные службы** > **Резервное копирование инфраструктуры**. Выберите **Конфигурация** в колонке **Резервное копирование инфраструктуры**.
3. Найти **имя** и **дату завершения** резервного копирования в списке **доступных резервных копий**.
4. Убедитесь, что для **состояния** отображается значение **Выполнено**.

## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения о рабочем процессе по восстановлению после потери данных. См. дополнительные сведения по [восстановлению после масштабной потери данных](azure-stack-backup-recover-data.md).
