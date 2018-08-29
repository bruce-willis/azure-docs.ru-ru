---
title: Включение резервного копирования для Azure Stack с помощью PowerShell | Документация Майкрософт
description: Включите службу резервного копирования инфраструктуры с помощью Windows PowerShell для восстановления Azure Stack в случае сбоя.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 8fe7f0ddd630cfca0242af6cc1d728bdef163352
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "41947461"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Включение резервного копирования для Azure Stack с помощью PowerShell

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Включите службу "Резервное копирование инфраструктуры" с помощью Windows PowerShell, чтобы периодически создавать резервные копии:
 - внутренней службы идентификации и корневого сертификата;
 - планов, предложений и подписок пользователей;
 - секретов Key Vault;
 - ролей и политик RBAC пользователей.

Чтобы включить резервное копирование, начать его и получить сведения о результатах резервного копирования через конечную точку управления оператором, вы можете использовать командлеты PowerShell.

## <a name="prepare-powershell-environment"></a>Подготовка среды PowerShell

Инструкции по настройке среды PowerShell приведены в разделе [Установка PowerShell для Azure Stack](azure-stack-powershell-install.md). Сведения о том, как войти в Azure Stack, см. в статье [Настройка среды оператора и вход в Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Предоставление прав на совместное использование резервной копии, учетных данных и ключа шифрования для включения резервного копирования

В том же сеансе PowerShell измените следующий сценарий PowerShell, добавив переменные среды. Выполните обновленный сценарий, чтобы предоставить службе архивации инфраструктуры права на совместное использование учетных данных и ключа шифрования.

| Переменная        | ОПИСАНИЕ   |
|---              |---                                        |
| $username       | Введите **имя пользователя**, указав домен и имя пользователя для расположения общего диска. У этого пользователя должны быть достаточные права на чтение и запись файлов. Например, `Contoso\backupshareuser`. |
| $password       | Введите **пароль** пользователя. |
| $sharepath      | Введите путь к **расположению хранилища резервных копий**. В качестве пути к общей папке на отдельном устройстве нужно использовать UNC-строку. UNC-строка указывает расположение ресурсов, например общих файлов или устройств. Чтобы обеспечить доступность резервных копий, устройство должно находиться в отдельном расположении. |
| $frequencyInHours | Частота, измеряемая в часах, определяет, как часто создаются резервные копии. По умолчанию используется значение 12. Планировщик поддерживает число, находящееся в диапазоне межу 4 и 12.|
| $retentionPeriodInDays | Период хранения в днях определяет, сколько дней резервные копии будут храниться во внешнем расположении. По умолчанию используется значение 7. Планировщик поддерживает число, находящееся в диапазоне межу 2 и 14. Резервные копии, период хранения которых превысил допустимое значение, автоматически удаляются из внешнего расположения.|
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Подтверждение параметров архивации

В этом же сеансе PowerShell выполните следующие команды:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
   ```

В результате должно отобразиться следующее.

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Обновление резервного набора данных
В этом же сеансе PowerShell можно обновить значения по умолчанию для срока хранения и частоту резервного копирования. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

В результате должно отобразиться следующее.

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>Дополнительная информация

 - Дополнительные сведения о резервном копировании см. в статье [Резервное копирование для Azure Stack](azure-stack-backup-back-up-azure-stack.md ).  
 - См. дополнительные сведения о [подтверждении завершения резервного копирования на портале администрирования](azure-stack-backup-back-up-azure-stack.md ).
