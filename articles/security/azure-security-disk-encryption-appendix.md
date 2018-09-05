---
title: Шифрование дисков Azure для виртуальных машин IaaS под управлением Windows и Linux | Документация Майкрософт
description: Эта статья является приложением к статье о шифровании дисков Microsoft Azure для виртуальных машин IaaS под управлением Windows и Linux.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 98bbcb84-8e6c-4eb2-8490-c2a0c67aad79
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2018
ms.author: mstewart
ms.openlocfilehash: 9efd8730af292e6f720c3bacd5707c48f0eab7ac
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42887939"
---
# <a name="appendix-for-azure-disk-encryption"></a>Приложение к статье о шифровании дисков Azure 
Эта статья является приложением к статье [Шифрование дисков Azure для виртуальных машин IaaS](azure-security-disk-encryption-overview.md). Чтобы понять контекст этого приложения, сначала вам нужно ознакомиться с основной статьей. Здесь описывается, как подготовить предварительно зашифрованные виртуальные жесткие диски и выполнить другие задачи.

## <a name="connect-to-your-subscription"></a>Подключение к подписке
Прежде чем начать, ознакомьтесь со статьей [Предварительные требования для шифрования дисков Azure](azure-security-disk-encryption-prerequisites.md). Убедившись, что выполнены все предварительные требования, подключитесь к своей подписке, выполнив следующие командлеты.

### <a name="bkmk_ConnectPSH"></a> Подключение к подписке с помощью PowerShell

1. Запустите сеанс Azure PowerShell и войдите в учетную запись Azure, используя следующую команду.

     ```powershell
     Connect-AzureRmAccount 
     ```
2. Если у вас несколько подписок и нужно указать, какая из них будет использоваться, введите приведенную ниже команду, чтобы увидеть подписки своей учетной записи.
     
     ```powershell
     Get-AzureRmSubscription
     ```
3. Чтобы указать подписку, которую нужно использовать, введите следующую команду.
 
     ```powershell
      Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Чтобы проверить, правильно ли настроена подписка, введите следующую команду.
     
     ```powershell
     Get-AzureRmSubscription
     ```
5. При необходимости подключитесь к Azure AD с помощью [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Чтобы убедиться, что командлеты шифрования дисков Azure установлены, введите следующую команду.
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. При необходимости ознакомьтесь со статьями [Начало работы с Azure PowerShell](/powershell/azure/get-started-azureps) и [AzureAD](/powershell/module/azuread).

### <a name="bkmk_ConnectCLI"></a> Подключение к подписке Azure с помощью Azure CLI

1. Войдите в Azure с помощью команды [az login](/cli/azure/authenticate-azure-cli#interactive-log-in). 
     
     ```azurecli
     az login
     ```

2. Если вы хотите использовать для входа определенный клиент, выполните команду:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Если у вас есть несколько подписок и нужно указать конкретную, получите список подписок с помощью команды [az account list](/cli/azure/account#az-account-list) и задайте требуемую, используя команду [az account set](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Проверьте номер установленной версии.
     
     ```azurecli
        az --version
     ``` 

5. При необходимости ознакомьтесь со статьей [Начало работы с Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Примеры сценариев PowerShell для шифрования дисков Azure 

- **Вывод списка всех зашифрованных виртуальных машин в подписке**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzureRmVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Вывод списка всех секретов шифрования дисков, используемых для шифрования виртуальных машин в хранилище ключей** 

     ```azurepowershell-interactive
     Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Выполнение сценария PowerShell для установки компонентов, необходимых при шифровании дисков Azure
Если вы уже знакомы с предварительными требованиями для шифрования дисков Azure, можно использовать [соответствующий сценарий PowerShell предварительных требований](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Пример использования этого сценария PowerShell см. в статье [Краткое руководство. Шифрование виртуальной машины IaaS под управлением Windows с помощью Azure PowerShell](quick-encrypt-vm-powershell.md). Вы можете удалить комментарии из раздела сценария, начиная со строки 211, чтобы шифровать все диски имеющихся виртуальных машин в имеющейся группе ресурсов. 

В следующей таблице показано, какие параметры могут использоваться в сценарии PowerShell: 


|Параметр|ОПИСАНИЕ|Необязательно|
|------|------|------|
|$resourceGroupName| Имя группы ресурсов, к которой принадлежит хранилище ключей.  При отсутствии группы ресурсов с таким именем — она будет создана.| Истина|
|$keyVaultName|Имя хранилища ключей, в котором будут размещаться ключи шифрования. При отсутствии хранилища ключей с таким именем — оно будет создано.| Истина|
|$location|Расположение хранилища ключей. Убедитесь, что хранилище ключей и виртуальные машины, которые предстоит зашифровать, находятся в одном расположении. Получите список расположений с помощью команды `Get-AzureRMLocation`.|Истина|
|$subscriptionId|Идентификатор подписки Azure для использования.  Вы можете получить идентификатор подписки с помощью команды `Get-AzureRMSubscription`.|Истина|
|$aadAppName|Имя приложения Azure AD, которое будет использоваться для записи секретов в хранилище ключей. Будет создано приложение с таким именем (если оно еще не создано). Если это приложение уже есть, передайте параметр aadClientSecret в сценарий.|Ложь|
|$aadClientSecret|Секрет клиента приложения Azure AD, который был создан ранее.|Ложь|
|$keyEncryptionKeyName|Имя дополнительного ключа шифрования ключа в хранилище ключей. При отсутствии ключа с таким именем — он будет создан.|Ложь|


## <a name="resource-manager-templates"></a>Шаблоны диспетчера ресурсов

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Шифрование или расшифровка виртуальных машин без приложения Azure AD


- [Включение шифрования дисков на виртуальной машине IaaS под управлением Windows, которая уже существует или работает](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Отключение шифрования дисков на виртуальной машине IaaS под управлением Windows, которая уже есть или работает](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Включение шифрования дисков на виртуальной машине IaaS под управлением Linux, которая уже существует или работает](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
 -  [Отключение шифрования на работающей виртуальной машине Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Для виртуальных машин Linux отключение шифрования возможно только для томов данных.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Шифрование или расшифровка виртуальных машин с приложением Azure AD (предыдущий выпуск) 
 
- [Включение шифрования дисков на виртуальной машине IaaS под управлением Windows, которая уже существует или работает](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Включение шифрования дисков на виртуальной машине IaaS под управлением Linux, которая уже существует или работает](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Отключение шифрования дисков на работающей виртуальной машине IaaS под управлением Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Отключение шифрования на работающей виртуальной машине Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Для виртуальных машин Linux отключение шифрования возможно только для томов данных. 

- [Включение шифрования дисков на новой виртуальной машине IaaS с Windows из Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Этот шаблон создает зашифрованную виртуальную машину Windows на основе образа из коллекции Windows Server 2012.

- [Создание новой зашифрованной виртуальной машины IaaS Windows с управляемым диском на основе образа из коллекции](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Этот шаблон создает зашифрованную виртуальную машину Windows с управляемыми дисками на основе образа из коллекции Windows Server 2012.

- [Шаблон развертывания RHEL 7.2 с полным шифрованием диска и управляемыми дисками](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)
    - Этот шаблон создает полностью зашифрованную виртуальную машину RHEL 7.2 в Azure с использованием управляемых дисков. Он включает в себя зашифрованный диск ОС на 30 ГБ и зашифрованный массив размером 200 ГБ (RAID-0), установленный в /mnt/raidencrypted. Поддерживаемые дистрибутивы серверов Linux см. в разделе [Какие дистрибутивы Linux поддерживает шифрование дисков Azure?](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 

- [Шаблон развертывания RHEL 7.2 с полным шифрованием диска с неуправляемыми дисками](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel-unmanaged)
    - Этот шаблон создает в Azure виртуальную машину RHEL 7.2 с полным шифрованием с зашифрованным диском операционной системы размером 30 ГБ и массивом RAID-0 размером 200 ГБ, подключенным к /mnt/raidencrypted. Поддерживаемые дистрибутивы серверов Linux см. в разделе [Какие дистрибутивы Linux поддерживает шифрование дисков Azure?](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 

- [Включение шифрования дисков на предварительно зашифрованном виртуальном жестком диске для Windows или Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)

- [Создание нового зашифрованного управляемого диска из предварительно зашифрованного виртуального жесткого диска или большого двоичного объекта хранилища](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Создает зашифрованный управляемый диск на основе предварительно зашифрованного виртуального жесткого диска и его соответствующих параметров шифрования.

- [Включение шифрования диска на запущенной виртуальной машине под управлением Windows с использованием отпечатка сертификата клиента AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    
- [Включение шифрования диска в запущенном масштабируемом наборе виртуальных машин Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Включение шифрования диска в запущенном масштабируемом наборе виртуальных машин Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

 - [Развертывание масштабируемого набора виртуальных машин Linux с помощью Jumpbox и включение шифрования в этом наборе Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

 - [Развертывание масштабируемого набора виртуальных машин Windows с помощью Jumpbox и включение шифрования в этом наборе Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Отключение шифрования диска в запущенном масштабируемом наборе виртуальных машин Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Отключение шифрования диска в запущенном масштабируемом наборе виртуальных машин Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

## <a name="bkmk_preWin"></a> Подготовка предварительно зашифрованного виртуального жесткого диска Windows
В следующем разделе приведены инструкции по подготовке предварительно зашифрованного виртуального жесткого диска Windows к развертыванию в качестве зашифрованного виртуального жесткого диска в IaaS Azure. Используйте эти сведения, чтобы подготовить и загрузить новую виртуальную машину Windows (VHD) в Azure Site Recovery Azure или Azure. Дополнительные сведения о подготовке и отправке виртуального жесткого диска см. в статье [Отправка универсального диска VHD и создание виртуальных машин с его помощью в Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Обновление групповой политики с целью разрешить защиту ОС без TPM
Настройте параметр групповой политики BitLocker, который называется **Шифрование дисков BitLocker**. Для этого выберите **Политика локального компьютера** > **Конфигурация компьютера** > **Административные шаблоны** > **Компоненты Windows**. Задайте для этого параметра такое значение: **Диски операционной системы** > **Обязательная дополнительная проверка подлинности при запуске** > **Разрешить использование BitLocker без совместимого TPM**, как показано на рисунке ниже.

![Антивредоносное ПО Майкрософт в Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Установка компонентов BitLocker
Для Windows Server 2012 или более поздней версии используйте следующую команду.

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Для Windows Server 2008 R2 используйте следующую команду.

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Подготовка тома операционной системы для BitLocker с помощью `bdehdcfg`
Чтобы сжать раздел операционной системы и подготовить компьютер к использованию BitLocker, запустите компонент [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment#using-bitlocker-to-encrypt-volumescommand).

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Защита тома операционной системы с помощью BitLocker
Используйте команду [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx), чтобы включить шифрование на загрузочном томе с использованием внешнего предохранителя ключа. Можно также поместить внешний ключ (BEK-файл) на внешний диск или том. Шифрование будет включено для системного или загрузочного тома после перезагрузки.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Чтобы получить внешний ключ с использованием BitLocker, подготовьте виртуальную машину с отдельным виртуальным жестким диском для данных и ресурсов.

## <a name="bkmk_LinuxRunning"></a> Шифрование диска ОС на работающей виртуальной машине Linux

### <a name="prerequisites-for-os-disk-encryption"></a>Предварительные требования для шифрования диска ОС

* Виртуальная машина должна использовать дистрибутив, который совместим с шифрованием диска ОС. Список дистрибутивов приведен в разделе [Какие дистрибутивы Linux поддерживает шифрование дисков Azure?](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 
* Виртуальная машина должна быть создана из образа Marketplace в Azure Resource Manager.
* Виртуальная машина Azure по крайней мере с 4 ГБ ОЗУ (рекомендуемый размер — 7 ГБ).
* (Для RHEL и CentOS.) Отключите SELinux. Чтобы отключить SELinux на виртуальной машине, ознакомьтесь с разделом "4.4.2. Disabling SELinux" (4.4.2. Отключение SELinux) [руководства пользователя и администратора SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux).
* После отключения SELinux перезагрузите виртуальную машину по крайней мере один раз.

### <a name="steps"></a>Действия
1. Создайте виртуальную машину с помощью одного из дистрибутивов, указанных ранее.

 Для CentOS 7.2 шифрование диска операционной системы можно включить через специальный образ. Чтобы использовать этот образ, задайте при создании виртуальной машины для параметра SKU значение 7.2n.

 ```powershell
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Настройте виртуальную машину в соответствии с потребностями. Если вы собираетесь включить шифрование всех дисков (диска ОС и дисков данных), то диски данных необходимо указать и подключить в файле /etc/fstab.

 > [!NOTE]
 > Чтобы указать диски данных в файле /etc/fstab, используйте значение UUID=... (в этом случае не нужно указывать имя блочного устройства, например /dev/sdb1). В процессе шифрования порядок дисков на виртуальной машине изменится. Если на виртуальной машине используется определенный порядок блочных устройств, то при их подключении после шифрования произойдет сбой.

3. Выйдите из сеансов SSH.

4. Чтобы зашифровать ОС, задайте при включении шифрования для параметра volumeType значение **All** или **OS**.

 > [!NOTE]
 > Все пользовательские процессы, не запущенные как службы `systemd`, необходимо завершить с помощью `SIGKILL`. Перезагрузите виртуальную машину. В случае включения шифрования диска ОС на работающей виртуальной запланируйте период простоя.

5. Периодически отслеживайте ход выполнения шифрования, следуя инструкциям из [следующего раздела](#monitoring-os-encryption-progress).

6. Как только командлет Get-AzureRmVmDiskEncryptionStatus вернет значение VMRestartPending, перезапустите виртуальную машину, войдя в нее, либо с помощью портала, PowerShell или интерфейса командной строки.
    ```powershell
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Перед перезагрузкой рекомендуется сохранить [диагностические данные загрузки](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) виртуальной машины.

## <a name="monitoring-os-encryption-progress"></a>Мониторинг хода выполнения шифрования операционной системы
Мониторинг хода выполнения шифрования ОС можно выполнять тремя способами.

* Используйте командлет `Get-AzureRmVmDiskEncryptionStatus` и просмотрите поле ProgressMessage.
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 На виртуальных машинах с хранилищем класса Premium шифрование диска ОС длится 40–50 минут с момента появления сообщения "OS disk encryption started" (Начато шифрование диска ОС).

 В связи с [ошибкой № 388](https://github.com/Azure/WALinuxAgent/issues/388) в WALinuxAgent в некоторых дистрибутивах `OsVolumeEncrypted` и `DataVolumesEncrypted` отображаются как `Unknown`. В WALinuxAgent 2.1.5 и более поздних версиях эта проблема исправляется автоматически. Если в выходных данных отображается `Unknown`, состояние шифрования дисков можно проверить с помощью обозревателя ресурсов Azure.

 Откройте [обозреватель ресурсов Azure](https://resources.azure.com/) и на панели выбора слева разверните иерархическую структуру, как показано ниже.

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 В InstanceView прокрутите вниз, чтобы увидеть состояние шифрования дисков.

 ![Представление экземпляра виртуальной машины](./media/azure-security-disk-encryption/vm-instanceview.png)

* Просмотрите [диагностические данные загрузки](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Сообщения расширения шифрования дисков Azure должны начинаться с `[AzureDiskEncryption]`.

* Выполните вход на виртуальную машину с помощью протокола SSH и скопируйте журнал расширения, расположенный по следующему пути:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Мы советуем не входить на виртуальную машину, пока шифруется ее диск ОС. Копируйте журналы только в том случае, когда два других способа не дали результата.

## <a name="bkmk_preLinux"></a> Подготовка предварительно зашифрованного виртуального жесткого диска Linux
Шаги подготовки предварительно зашифрованных виртуальных жестких дисков могут отличаться в зависимости от дистрибутива. Доступны примеры для подготовки [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE) и [CentOS 7](#bkmk_CentOS). 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
Настройте шифрование во время установки дистрибутива следующим образом.

1. Во время настройки разделов дисков выберите **Configure encrypted volumes** (Настройка зашифрованных томов).

 ![Настройка Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Создайте отдельный загрузочный диск (незашифрованный). Зашифруйте корневой диск.

 ![Настройка Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Укажите парольную фразу. Это парольная фраза, которую вы передали в хранилище ключей.

 ![Настройка Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Завершите настройку разделов.

 ![Настройка Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. После загрузки виртуальной машины отобразится запрос парольной фразы. Введите парольную фразу, указанную на шаге 3.

 ![Настройка Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Подготовьте виртуальную машину к передаче в Azure, следуя [этим инструкциям](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Пока не выполняйте последний шаг (отзыв виртуальной машины).

Настройте шифрование для Azure, выполнив следующие шаги.

1. Создайте файл /usr/local/sbin/azure_crypt_key.sh с приведенным ниже содержимым. Обратите внимание на параметр KeyFileName — это имя файла парольной фразы, используемое Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Измените настройки шифрования в */etc/crypttab*. Результат будет выглядеть так:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Если вы редактировали файл *azure_crypt_key.sh* в Windows и скопировали его в Linux, выполните `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Добавьте в сценарий разрешения для исполняемого файла.
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Измените */etc/initramfs-tools/modules*, добавив приведенные ниже строки.
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Выполните `update-initramfs -u -k all` и обновите initramfs, чтобы `keyscript` вступил в действие.

7. Теперь виртуальную машину можно отозвать.

 ![Настройка Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Перейдите к следующему шагу и передайте свой виртуальный жесткий диск в Azure.

### <a name="bkmk_openSUSE"></a> openSUSE 13.2
Чтобы настроить шифрование во время установки дистрибутива, сделайте следующее:
1. При настройке разделов дисков выберите **Encrypt Volume Group** (Шифрование группы томов), а затем введите пароль. Это пароль, который потребуется передать в хранилище ключей.

 ![Настройка openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Загрузите виртуальную машину, используя указанный пароль.

 ![Настройка openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Подготовьте виртуальную машину для передачи в Azure, следуя инструкциям в разделе [Подготовка виртуальной машины SLES или openSUSE для Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Пока не выполняйте последний шаг (отзыв виртуальной машины).

Чтобы настроить шифрование для Azure, сделайте следующее:
1. Измените файл /etc/dracut.conf и добавьте приведенную ниже строку.
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Закомментируйте приведенные строки в конце файла /usr/lib/dracut/modules.d/90crypt/module-setup.sh.
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. В начале файла /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh добавьте следующую строку.
 ```
    DRACUT_SYSTEMD=0
 ```
Затем измените все вхождения
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
на:
```
    if [ 1 ]; then
```
4. Измените файл /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh, добавив следующий текст после строки # Open LUKS device.

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Выполните `/usr/sbin/dracut -f -v`, чтобы обновить initrd.

6. Теперь виртуальную машину можно отозвать и передать свой виртуальный жесткий диск в Azure.

### <a name="bkmk_CentOS"></a> CentOS 7
Чтобы настроить шифрование во время установки дистрибутива, сделайте следующее:
1. Во время настройки разделов дисков выберите **Encrypt my data** (Шифрование личных данных).

 ![Настройка CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Включите шифрование для корневого раздела (параметр **Encrypt**).

 ![Настройка CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Укажите парольную фразу. Это парольная фраза, которую потребуется передать в хранилище ключей.

 ![Настройка CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. После загрузки виртуальной машины отобразится запрос парольной фразы. Введите парольную фразу, указанную на шаге 3.

 ![Настройка CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Подготовьте виртуальную машину для передачи в Azure, следуя инструкциям в разделе "CentOS 7.0+" статьи [Подготовка виртуальной машины на основе CentOS для Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Пока не выполняйте последний шаг (отзыв виртуальной машины).

6. Теперь виртуальную машину можно отозвать и передать свой виртуальный жесткий диск в Azure.

Чтобы настроить шифрование для Azure, сделайте следующее:

1. Измените файл /etc/dracut.conf и добавьте приведенную ниже строку.
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Закомментируйте приведенные строки в конце файла /usr/lib/dracut/modules.d/90crypt/module-setup.sh.
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. В начале файла /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh добавьте следующую строку.
```
    DRACUT_SYSTEMD=0
```
Затем измените все вхождения
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
значение
```
    if [ 1 ]; then
```
4. Измените файл /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh, добавив следующий текст после строки # Open LUKS device.
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Выполните команду /usr/sbin/dracut -f -v, чтобы обновить initrd.

![Настройка CentOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Передача зашифрованного виртуального жесткого диска в учетную запись хранения Azure
После включения шифрования BitLocker или DM-Crypt следует передать локальный зашифрованный виртуальный жесткий диск в учетную запись хранения.
```powershell
    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Передача секрета для предварительно зашифрованной виртуальной машины в хранилище ключей
При шифровании с использованием приложения Azure AD (предыдущая версия) секрет шифрования дисков, полученный ранее, необходимо передать в хранилище ключей в качестве секрета. В хранилище ключей нужно включить разрешения для клиента Azure AD и шифрование дисков.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Секрет шифрования дисков, не зашифрованный с помощью ключа шифрования ключей
Используйте командлет [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret), чтобы настроить секрет в хранилище ключей. Если вы используете виртуальную машину Windows, то с помощью командлета `Set-AzureKeyVaultSecret` можно закодировать BEK-файл как строку Base64 и передать его в хранилище ключей. Если используется виртуальная машина Linux, то парольную фразу можно закодировать как строку Base64, а затем передать в хранилище ключей. Убедитесь также, что при создании секрета в хранилище ключей были установлены следующие теги.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


На следующем шаге используйте `$secretUrl`, чтобы [подключить диск ОС, не применяя ключ шифрования ключей](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Секрет шифрования дисков, зашифрованный с помощью ключа шифрования ключей
Перед передачей секрета в хранилище ключей его можно дополнительно зашифровать с помощью ключа шифрования ключей. Используйте [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) для создания оболочки, чтобы сначала зашифровать секрет с использованием ключа шифрования ключей. Эта операция возвращает значения, закодированные как строка URL-адреса в кодировке Base64, которые затем передаются в качестве секрета с помощью командлета [`Set-AzureKeyVaultSecret`](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret).

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

На следующем шаге мы используем `$KeyEncryptionKey` и `$secretUrl`, чтобы [подключить диск ОС, применив ключ шифрования ключей](#BKMK_URLKEK).

##  <a name="bkmk_SecretURL"></a> Указание URL-адреса секрета при подключении диска ОС

###  <a name="bkmk_URLnoKEK"></a>Без использования ключа шифрования ключа
При подключении диска ОС необходимо передать `$secretUrl`. Этот URL-адрес был создан в разделе "Секрет шифрования дисков, не зашифрованный с помощью ключа шифрования ключей".
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>С использованием ключа шифрования ключа
При подключении диска ОС передайте `$KeyEncryptionKey` и `$secretUrl`. Этот URL-адрес был создан в разделе "Секрет шифрования дисков, зашифрованный с помощью ключа шифрования ключей".
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
