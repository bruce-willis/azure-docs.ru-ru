---
title: Устранение неполадок загрузки BitLocker на виртуальной машине Azure | Документация Майкрософт
description: Узнайте, как устранить неполадки загрузки BitLocker на виртуальной машине Azure
services: virtual-machines-windows
documentationCenter: ''
authors: genli
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/31/2018
ms.author: genli
ms.openlocfilehash: 884959d7e59c351b4b6d8d09cb011fce88a4d30d
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43670012"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Ошибки загрузки BitLocker на виртуальной машине Azure

 В этой статье описаны ошибки BitLocker, которые могут возникнуть при запуске виртуальной машины Windows в Microsoft Azure.

> [!NOTE] 
> В Azure предлагаются две модели развертывания для создания ресурсов и работы с ними: [модель диспетчера ресурсов и классическая модель](../../azure-resource-manager/resource-manager-deployment-model.md). В этой статье описывается использование модели развертывания на основе диспетчера ресурсов. Для новых развертываний рекомендуется использовать эту модель развертывания вместо классической.

 ## <a name="symptom"></a>Симптом

 Виртуальная машина Windows не запускается. При проверке скриншотов в окне [Диагностика загрузки](boot-diagnostics.md) появляется одно из следующих сообщений об ошибке.

- Подключение USB-драйвера с ключом BitLocker

- Вы заблокированы. Введите ключ восстановления чтобы снова начать работу (макет клавиатуры: США). Неправильные входные данные вводились много раз, поэтому ваш компьютер был заблокирован для защиты вашей конфиденциальной информации. Чтобы получить ключ восстановления, перейдите в раздел http://windows.microsoft.com/recoverykeyfaq с другого компьютера или мобильного устройства. В случае необходимости ключ ID — XXXXXXX. Или можно вернуть компьютер в исходное состояние.

- Введите пароль для разблокировки этого диска [ ]. Нажмите клавишу Insert для просмотра пароля при вводе.
- Введите ключ восстановления. Загрузите ключ восстановления с USB-устройства.

## <a name="cause"></a>Причина:

Эта проблема может возникнуть если виртуальная машина не сможет найти файл ключа восстановления BitLocker (BEK) для дешифрования зашифрованного диска.

## <a name="solution"></a>Решение

Чтобы устранить эту проблему, остановите и освободите виртуальную машину, а затем перезапустите ее. Эта операция заставляет виртуальную машину извлекать файл BEK из Azure Key Vault, а затем помещать его на зашифрованный диск. 

Если с помощью этого метода не удается устранить проблему, выполните следующие действия для восстановления BEK-файла вручную.

1. Сделайте снимок системного диска затронутой виртуальной машины в качестве резервной копии. Дополнительные сведения см. в статье [Создание моментального снимка](./snapshot-copy-managed-disk.md).
2. [Подключение системного диска к виртуальной машине восстановления](./troubleshoot-recovery-disks-portal.md), которая шифруется с помощью BitLocker. Это необходимо для запуска команды [manage-bde](https://docs.microsoft.com/windows-server/administration/windows-commands/manage-bde), которая доступна только на виртуальных машинах, зашифрованных с помощью BitLocker.

    Когда вы подключаете управляемый диск, может появиться сообщение об ошибке "диск содержит параметры шифрования и поэтому не может использоваться в качестве диска данных". В этом случае запустите следующий сценарий, чтобы повторить попытку подключения этого диска.

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

    New-AzureRmDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzureRmDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzureRmDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzureRMVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzureRmVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzureRMVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Вы не можете подключить управляемый диск к виртуальной машине, которая была восстановлена из BLOB-объекта образа.

3. После подключения диска создайте удаленный рабочий стол для виртуальной машины восстановления, чтобы выполнять некоторые сценарии Azure PowerShell. Установите последнюю версию [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) на виртуальную машину восстановления.

4. Откройте расширенный сеанс Azure PowerShell (Запуск от имени администратора). Выполните приведенные ниже команды, чтобы войти в подписку Azure.

    ```Powershell
    Add-AzureRMAccount -SubscriptionID [SubscriptionID]
    ```

5. Выполните следующий сценарий, чтобы проверить имя BEK-файла.

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzureKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Ниже приведен пример выходных данных. Найдите имя BEK-файла для подключенного диска. В этом случае предположим, что имя подключенного диска — F, а имя BEK-файла — EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Если отображаются два дублированных тома, том, содержащий новую временную метку является текущим BEK-файлом, который используется виртуальной машиной восстановления.

    Если значение **Тип содержимого** — **зашифрованный BEK**, перейдите в меню [Сценарии ключа шифрования ключа (KEK)](#key-encryption-key-scenario).

    Теперь, когда имя BEK-файла для диска выбрано, необходимо создать секретное имя BEK-файла для разблокировки диска. 

6.  Скачайте BEK-файл на диск восстановления. Следующий пример сохраняет BEK-файл в папку C:\BEK. Перед запуском скриптов убедитесь, что путь `C:\BEK\` существует.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK"
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Чтобы разблокировать подключенный диск с помощью BEK-файла, выполните следующую команду.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    В этом примере диск F является подключенным диском ОС. Выберите правильное имя диска. 

    - Если диск был разблокирован с помощью BEK ключа можно считать, что проблема BItLocker разрешена. 

    - Если диск не разблокируется с помощью BEK-ключа, можно использовать приостановку защиты, чтобы временно отключить BitLocker, выполнив следующую команду.
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Если необходимо перестроить виртуальную машину с помощью диска dytem, ​​нужно полностью расшифровать диск. Для этого выполните следующую команду:

        ```powershell
        manage-bde -off F:
        ```
8.  Отсоедините диск от виртуальной машины восстановления, а затем снова присоедините его к затронутой виртуальной машине в качестве системного диска. Дополнительные сведения см. в разделе [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью Azure PowerShell](troubleshoot-recovery-disks.md).

### <a name="key-encryption-key-scenario"></a>Сценарий ключа шифрования ключа

Для сценария ключа шифрования ключа выполните следующие действия.

1. Убедитесь, что зарегистрированная учетная запись пользователя требует наличие "развернутого" разрешения в политиках доступа к хранилищу ключей в **USER|Key permissions|Cryptographic Operations|Unwrap Key**.
2. Сохраните следующие сценарии в PS1-файл.

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $authResult = $authContext.AcquireToken($resourceAppIdURI, $clientId, $redirectUri, "Auto")
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzureKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)
    ```
3. Задайте параметры. Сценарий будет обрабатывать секрет KEK для создания ключа BEK, а затем сохранит его в локальной папке на виртуальной машине восстановления.

4. После запуска сценария появится следующий результат.

        GAC    Version        Location                                                                              
        ---    -------        --------                                                                              
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...
        False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\AzureRM.profile\4.0.0\Microsoft.Identity...

    После завершения сценария отобразятся следующие выходные данные.

        VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
        version=2015-06-01 with -1-byte payload
        VERBOSE: received 360-byte response of content type application/json; charset=utf-8


5. Чтобы разблокировать подключенный диск с помощью BEK-файла, выполните следующую команду.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    В этом примере диск F является подключенным диском ОС. Выберите правильное имя диска. 

    - Если диск был разблокирован с помощью BEK ключа можно считать, что проблема BItLocker разрешена. 

    - Если диск не разблокируется с помощью BEK-ключа, можно использовать приостановку защиты, чтобы временно отключить BitLocker, выполнив следующую команду.
    
        ```powershell
        manage-bde -protectors -disable F: -rc 0
        ```      
    - Если необходимо перестроить виртуальную машину с помощью диска dytem, ​​нужно полностью расшифровать диск. Для этого выполните следующую команду:

        ```powershell
        manage-bde -off F:
        ```

6. Отсоедините диск от виртуальной машины восстановления, а затем снова присоедините его к затронутой виртуальной машине в качестве системного диска. Дополнительные сведения см. в разделе [Устранение неполадок с виртуальной машиной Windows при подключении диска операционной системы к виртуальной машине восстановления с помощью Azure PowerShell](troubleshoot-recovery-disks.md).
