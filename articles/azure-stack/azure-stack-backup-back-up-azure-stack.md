---
title: Резервное копирование для Azure Stack | Документация Майкрософт
description: Выполните резервное копирование по требованию для Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242892"
---
# <a name="back-up-azure-stack"></a>Резервное копирование для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Выполните резервное копирование по требованию для Azure Stack. Инструкции по настройке среды PowerShell приведены в разделе [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md). Сведения о том, как войти в Azure Stack, см. в статье [Настройка среды оператора и вход в Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="start-azure-stack-backup"></a>Запуск резервного копирования Azure Stack

Запустите создание новой резервной копии с помощью команды Start-AzSBackup с переменной -AsJob для отслеживания хода выполнения. 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>Проверка завершения резервного копирования с помощью PowerShell

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- В результате должно отобразиться следующее:

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>Подтверждение завершения резервного копирования на портале администрирования

1. Откройте портал администрирования Azure Stack по адресу: [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).
2. Выберите **Дополнительные службы** > **Резервное копирование инфраструктуры**. Выберите **Конфигурация** в колонке **Резервное копирование инфраструктуры**.
3. Найти **имя** и **дату завершения** резервного копирования в списке **доступных резервных копий**.
4. Убедитесь, что для **состояния** отображается значение **Выполнено**.

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о рабочем процессе по восстановлению после потери данных. См. дополнительные сведения по [восстановлению после масштабной потери данных](azure-stack-backup-recover-data.md).