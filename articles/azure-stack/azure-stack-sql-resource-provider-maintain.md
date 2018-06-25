---
title: Поддержка поставщика ресурсов SQL в Azure Stack | Документация Майкрософт
description: Сведения о поддержке службы поставщика ресурсов SQL в Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e7ddbe1235b3957a1e0cb7693ee728bfdbf9db6b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295665"
---
# <a name="maintenance-operations"></a>Операции обслуживания 
Поставщик ресурсов SQL представляет собой заблокированную виртуальную машину. Обновление безопасности виртуальной машины поставщика ресурсов может быть выполнено через конечную точку PowerShell Just Enough Administration (JEA) _DBAdapterMaintenance_. Для упрощения выполнения этих операций сценарий предоставляется вместе с пакетом установки поставщика ресурсов.

## <a name="patching-and-updating"></a>Установка исправлений и обновлений
Поставщик ресурсов SQL не обслуживается как часть Azure Stack, так как это дополнительный компонент. Корпорация Майкрософт будет предоставлять обновления поставщика ресурсов SQL при необходимости. Экземпляр поставщика ресурсов SQL создается на виртуальной машине _пользователя_ в рамках подписки поставщика по умолчанию. Таким образом он необходим для предоставления исправлений Windows, защиты от вирусов и т. д. Пакеты обновления Windows, предоставляемые в рамках цикла исправлений и обновлений, можно использовать для применения обновлений к виртуальной машине Windows. При выпуске обновленного адаптера предоставляется скрипт для применения обновления. Этот скрипт создает виртуальную машину поставщика ресурсов и переносит любое имеющееся состояние.

 ## <a name="backuprestoredisaster-recovery"></a>Резервное копирование, восстановление и аварийное восстановление
 Поставщик ресурсов SQL не участвует в процессе резервного копирования и аварийного восстановления Azure Stack, так как это дополнительный компонент. Для упрощения будут предоставлены скрипты:
- Архивация необходимых сведений о состоянии (хранимых в учетной записи хранения Azure Stack).
- Восстановление поставщика ресурсов в случае полного восстановления стека становится необходимостью.
Перед восстановлением поставщика ресурсов требуется восстановить серверы базы данных (если это необходимо).

## <a name="updating-sql-credentials"></a>Обновление учетных данных SQL
Вы несете ответственность за создание и обслуживание учетных записей системного администратора на серверах SQL. Поставщику ресурсов нужна учетная запись с этими привилегиями для управления базами данных от имени пользователей. Доступ к данным в этих базах данных не требуется. При необходимости обновить пароли на серверах SQL Server можно использовать функцию обновления интерфейса администратора поставщика ресурсов, чтобы изменить сохраненный пароль, используемый поставщиком ресурсов. Эти пароли сохраняются в Key Vault в экземпляре Azure Stack.

Чтобы изменить параметры, щелкните **Обзор** &gt; **ADMINISTRATIVE RESOURCES** (Административные ресурсы) &gt; **SQL Hosting Servers** (Серверы размещения SQL) &gt; **Имена входа SQL** и выберите имя входа. Сначала необходимо внести изменения в экземпляр SQL (и любые реплики, если необходимо). На панели **Параметры** щелкните **Пароль**.

![Обновление пароля администратора](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Смена секретов 
*Эти инструкции применяются только к интегрированным системам Azure Stack (1804 и более поздних версий). Не пытайтесь сменить секреты в версиях Azure Stack, предшествующих 1804.*

При использовании поставщиков ресурсов SQL и MySQL с интегрированными системами Azure Stack вы можете сменить следующие секреты инфраструктуры (развертывания):
- внешний SSL-сертификат, [предоставленный во время развертывания](azure-stack-pki-certs.md);
- пароль учетной записи локального администратора виртуальной машины поставщика ресурсов, предоставленный во время развертывания;
- пароль пользователя диагностики (dbadapterdiag) поставщика ресурсов.

### <a name="powershell-examples-for-rotating-secrets"></a>Примеры PowerShell для смены секретов

**Смена всех секретов одновременно**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Смена только пароля пользователя диагностики**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd 
```

**Смена пароля учетной записи локального администратора виртуальной машины**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Смена сертификата SSL**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd 
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Параметры SecretRotationSQLProvider.ps1

|Параметр|ОПИСАНИЕ|
|-----|-----|
|AzCredential|Учетные данные учетной записи администратора службы Azure Stack.|
|CloudAdminCredential|Учетные данные учетной записи домена администратора облака Azure Stack.|
|PrivilegedEndpoint|Привилегированная конечная точка для получения информации о метке Azure Stack (Get-AzureStackStampInformation).|
|DiagnosticsUserPassword|Пароль пользователя диагностики.|
|VMLocalCredential|Учетная запись локального администратора виртуальной машины MySQLAdapter.|
|DefaultSSLCertificatePassword|Пароль SSL-сертификата (*pfx) по умолчанию.|
|DependencyFilesLocalPath|Локальный путь к файлам зависимостей.|
|     |     |

### <a name="known-issues"></a>Известные проблемы
**Проблема**: не выполняется автоматический сбор журналов для смены секретов, если происходит сбой настраиваемого сценария смены секретов при его запуске.

**Возможное решение**: используйте командлет Get-AzsDBAdapterLogs, чтобы собрать все журналы поставщика ресурсов, включая AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log в каталоге C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Обновление ОС виртуальной машины
Существует несколько способов обновить виртуальную машину с Windows Server:
- Установка пакета последней версии поставщика ресурсов с помощью текущего исправленного образа основных компонентов Windows Server 2016.
- Установка пакета обновления Windows во время установки или обновления поставщика ресурсов.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Обновление определений Защитника Windows на виртуальной машине
Чтобы обновить определения Защитника, выполните следующие действия.

1. Загрузите обновление определений Защитника Windows на странице [обновлений определений для антивирусной программы "Защитник Windows" и других антивирусных программ корпорации Майкрософт](https://www.microsoft.com/en-us/wdsi/definitions).

    На этой странице из раздела "Загрузка и установка определений вручную" загрузите 64-битный файл антивирусной программы "Защитник Windows" для Windows 10 и Windows 8.1. 
    
    Прямая ссылка: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64.

2. Создайте сеанс PowerShell для конечной точки обслуживания виртуальной машины для адаптера поставщика ресурсов SQL.
3. Скопируйте файл обновления определений на компьютер адаптера базы данных, используя сеанс обслуживания конечной точки.
4. Во время сеанса обслуживания PowerShell вызовите команду _Update-DBAdapterWindowsDefenderDefinitions_.
5. После установки рекомендуем удалить использованный файл обновления определений. Его можно удалить во время сеанса обслуживания с помощью команды _Remove-ItemOnUserDrive)_.


Ниже приведен пример сценария для обновления определений Защитника (замените адрес или имя виртуальной машины на фактическое значение).

```powershell
# Set credentials for the RP VM local admin user
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession 
```


## <a name="collect-diagnostic-logs"></a>Сбор данных журналов диагностики
Поставщик ресурсов SQL представляет собой заблокированную виртуальную машину. При необходимости сбора данных журнала с виртуальной машины предоставляется конечная точка PowerShell Just Enough Administration (JEA) _DBAdapterDiagnostics_. С помощью этой конечной точки можно выполнить две команды:

- **Get-AzsDBAdapterLog**. Готовит ZIP-пакет, содержащий журналы диагностики RP, и помещает его в пользовательский диск для сеанса. Эту команду можно вызвать без параметров. Она будет собирать данные журнала за последние четыре часа.
- **Remove-AzsDBAdapterLog**. Очищает имеющиеся пакеты журналов на виртуальной машине поставщика ресурсов.

Во время развертывания поставщика ресурсов или обновления подключения к конечной точке диагностики для извлечения журналов поставщика ресурсов создается учетная запись пользователя с именем **dbadapterdiag**. Пароль этой учетной записи совпадает с паролем, предоставленным для учетной записи локального администратора во время развертывания или обновления.

Чтобы использовать эти команды, необходимо создать удаленный сеанс PowerShell для виртуальной машины поставщика ресурсов и вызвать команду. При необходимости вы можете указать параметры FromDate и ToDate. Если их не указать (или указать только один), в поле ToDate будет указано текущее время, а в поле FromDate — значение времени за четыре часа до текущего.

В следующем примере сценария показано использование этих команд.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>Дополнительная информация
[Добавление серверов размещения SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
