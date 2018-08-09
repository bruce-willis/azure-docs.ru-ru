---
title: Шифрование дисков Azure для виртуальных машин IaaS под управлением Linux | Документация Майкрософт
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин IaaS под управлением Linux.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 071ee0c3-2e6c-4ea9-bfc7-908865629144
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 92c9fce39a0fe068e29cf0a75b3c786891876dee
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39393001"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Linux 

Возможно несколько сценариев включения шифрования дисков, и последовательность действий для каждого может отличаться. В следующих разделах сценарии для виртуальных машин IaaS под управлением Linux описаны более подробно. Перед тем как использовать шифрование дисков Azure, необходимо выполнить [эти предварительные требования](azure-security-disk-encryption-prerequisites.md) и [дополнительные требования для виртуальных машин IaaS Linux](azure-security-disk-encryption-prerequisites.md#bkmk_LinuxPrereq).

Создайте [моментальный снимок](../virtual-machines/windows/snapshot-copy-managed-disk.md) и (или) резервную копию перед шифрованием дисков. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension с параметром -skipVmBackup. Дополнительные сведения см. в статье [Резервное копирование и восстановление зашифрованных виртуальных машин с помощью службы Azure Backup](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 >Чтобы убедиться, что секреты шифрования не пересекают региональные границы, шифрованию дисков Azure требуется, чтобы хранилище ключей и виртуальные машины были расположены в одном регионе. Создайте и используйте хранилище ключей, которое находится в том же регионе, что и виртуальная машина, которую нужно зашифровать.</br></br>

> Шифрование томов операционной системы Linux может занять несколько часов. Обычно для шифрования томов ОС Linux требуется больше времени, чем для томов данных. 

>Отключение шифрования на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  


## <a name="bkmk_NewLinux"></a> Развертывание новой виртуальной машины IaaS под управлением Linux с включенным шифрованием дисков 

1. Используйте [шаблон Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel) для создания новой зашифрованной виртуальной машины IaaS с ОС Linux. Шаблон создаст новую виртуальную машину RedHat Linux 7.2 с массивом RAID-0 на 200 ГБ и полным шифрованием дисков, использующую управляемые диски. В статье о [часто задаваемых вопросах](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) вы заметите, что некоторые дистрибутивы Linux поддерживают шифрование только дисков данных. Однако этот шаблон дает возможность ознакомиться с развертыванием шаблонов и проверкой состояния шифрования несколькими способами. 
 
1. На странице шаблона Azure Resource Manager нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите кнопку **Создать**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.

3. После развертывания шаблона проверьте состояние шифрования виртуальной машины предпочитаемым способом.
     - Проверьте состояние с помощью Azure CLI, используя команду [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Проверьте состояние с Azure PowerShell, используя командлет [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     - Выберите виртуальную машину, а затем щелкните **Диски** под заголовком **Параметры**, чтобы проверить состояние шифрования на портале. На диаграмме в разделе **Шифрование** вы увидите, включено ли оно. 

| Параметр | ОПИСАНИЕ |
| --- | --- |
| Идентификатор клиента AAD | Идентификатор клиента приложения Azure AD, которое имеет разрешения на запись секретов в хранилище ключей. |
| Секрет клиента AAD | Секрет клиента приложения Azure AD, которое имеет разрешения на запись секретов в ваше хранилище ключей. |
| Имя хранилища ключей | Имя хранилища ключей, в которое будет передан ключ. |
| Группа ресурсов хранилища ключей | Группа ресурсов хранилища ключей. |


## <a name="bkmk_RunningLinux"> </a> Включение шифрования на виртуальной машине IaaS под управлением Linux, которая уже существует или работает
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. 

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension, указав параметр -skipVmBackup. Команда Set-AzureRmVMDiskEncryptionExtension будет завершаться ошибкой при использовании с виртуальными машинами на основе управляемых дисков, пока не будет сделана резервная копия и указан определенный параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью Azure CLI 
Можно включить шифрование дисков для зашифрованного виртуального жесткого диска, установив программу командной строки [Azure CLI 2.0](/cli/azure). Его можно использовать в браузере с [Azure Cloud Shell](../cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell. Чтобы включить шифрование на виртуальных машинах IaaS под управлением Linux, которые уже существуют или работают в Azure, используйте следующие команды CLI.

Используйте команду [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure.

-  **Шифрование работающей виртуальной машины с использованием секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> </br>
Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Проверка того, что диски зашифрованы.** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте команду [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Отключение шифрования.** Чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a>Включение шифрования на существующей или работающей виртуальной машине Linux с помощью PowerShell
Используйте командлет [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure. 

-  **Шифрование работающей виртуальной машины с использованием секрета клиента.** Приведенный ниже сценарий инициализирует переменные и запускает командлет Set-AzureRmVMDiskEncryptionExtension. Группа ресурсов, виртуальная машина, хранилище ключей, приложение AAD и секрет клиента должны быть уже созданы в качестве необходимых компонентов. Замените MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID и My-AAD-client-secret на ваши значения. При шифровании дисков данных, а не диска операционной системы, может потребоваться добавить параметр -VolumeType. 

     ```azurepowershell-interactive
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента.** Шифрование дисков Azure позволяет указать существующий ключ из хранилища ключей для упаковки секретов шифрования диска, которые были сгенерированы при включении шифрования. Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault. При шифровании дисков данных, а не диска операционной системы, может потребоваться добавить параметр -VolumeType. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 
    
- **Проверка того, что диски зашифрованы.** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте командлет [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 
    
     ```azurepowershell-interactive 
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName MySecureRg -VMName MySecureVM
     ```
    
- **Отключение шифрования дисков.** Для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). Для виртуальных машин Linux отключение шифрования возможно только для томов данных.
     
     ```azurepowershell-interactive 
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Включение шифрования на существующей или работающей виртуальной машине с помощью шаблона

Включить шифрование дисков на существующих или работающих виртуальных машинах IaaS под управлением Linux в Azure можно с помощью [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите кнопку **Создать**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.

В следующей таблице перечислены параметры шаблона Resource Manager для существующих или работающих виртуальных машин, которые используют идентификатор клиента Azure AD.

| Параметр | ОПИСАНИЕ |
| --- | --- |
| AADClientID | Идентификатор клиента приложения Azure AD, которое имеет разрешения на запись секретов в хранилище ключей. |
| AADClientSecret | Секрет клиента приложения Azure AD, которое имеет разрешения на запись секретов в ваше хранилище ключей. |
| keyVaultName | Имя хранилища ключей, в которое будет передан ключ. Его можно получить с помощью команды интерфейса командной строки Azure `az keyvault show --name "MySecureVault" --query resourceGroup`. |
|  keyEncryptionKeyURL | URL-адрес ключа шифрования ключей, который используется для шифрования созданного ключа. Это необязательный параметр, если выбрать **nokek** из раскрывающегося списка UseExistingKek. Если из раскрывающегося списка UseExistingKek выбрано значение **kek**, то потребуется ввести значение _keyEncryptionKeyURL_. |
| volumeType | Тип тома, для которого будет выполняться шифрование. Допустимые поддерживаемые значения — _OS_ или _All_ (поддерживаемые дистрибутивы Linux и их версии для ОС и дисков данных можно просмотреть выше, в разделе предварительных требований). |
| sequenceVersion | Версия последовательности операций с BitLocker. Этот номер версии увеличивается каждый раз, когда шифрование диска выполняется на той же виртуальной машине. |
| vmName | Имя виртуальной машины, для которой будет выполняться шифрование. |
| парольная фраза | Введите надежную парольную фразу, которая будет служить ключом шифрования данных. |



## <a name="bkmk_EFA"> </a>Использование функции EncryptFormatAll для дисков данных на виртуальных машинах IaaS под управлением Linux
Параметр **EncryptFormatAll** сокращает время шифрования дисков данных Linux. Разделы, отвечающие определенным критериям, будут отформатированы (с текущей файловой системой). Затем они будут обратно подключены там, где они были подключены до выполнения команды. Если вы хотите исключить диск данных, который соответствует критериям, можно отключить его перед выполнением команды.

 После выполнения этой команды все диски, которые были подключены ранее, будут отформатированы. Уровень шифрования запускается поверх уже теперь пустого диска. Если выбран этот параметр, временный диск с ресурсами, подключенный к виртуальной машине, также будет зашифрован. Если временный диск сбрасывается, при следующей возможности он будет переформатирован и повторно зашифрован для виртуальной машины с помощью шифрования дисков Azure.

>[!WARNING]
> Параметр EncryptFormatAll не следует использовать, если в томах данных виртуальной машины есть требуемые данные. Вы можете исключить диски из шифрования, отключив их. Сначала следует попробовать параметр EncryptFormatAll на тестовой виртуальной машине, понять его функцию и последствия, а уже потом использовать на рабочих виртуальных машинах. Параметр EncryptFormatAll форматирует диск данных, и все данные на этом диске будут потеряны. Прежде чем продолжить, убедитесь, что диски, которые вы хотите исключить, правильно отключены. </br></br>
 >Если этот параметр устанавливается при обновлении параметров шифрования, это может привести к перезагрузке перед фактическим шифрованием. В этом случае необходимо также удалить из FSTAB-файла диск, который не нужно форматировать. Аналогично, перед началом операции шифрования нужно добавить в FSTAB-файл раздел, который нужно отформатировать и зашифровать. 

### <a name="bkmk_EFACriteria"> </a> Критерии EncryptFormatAll
Параметр проходит через все разделы и шифрует их, если они соответствуют **всем** из приведенных ниже критериев: 
- это не корневой или загрузочный раздел, а также не раздел операционной системы;
- этот раздел еще не зашифрован;
- это не том BEK;
- он подключен.


### <a name="bkmk_EFATemplate"> </a> Использование параметра EncryptFormatAll с шаблоном
Чтобы использовать параметр EncryptFormatAll, в любом готовом шаблоне Azure Resource Manager, который шифрует виртуальную машину Linux, измените поле **EncryptionOperation** для ресурса AzureDiskEncryption.

1. Например, используйте [шаблон Resource Manager для шифрования работающей виртуальной машины IaaS под управлением Linux](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).
3. Измените значение параметра **EncryptionOperation** с **EnableEncryption** на **EnableEncryptionFormatAl**.
4. Выберите подписку, группу ресурсов, расположение группы ресурсов, другие параметры, условия использования и соглашение. Нажмите кнопку **Создать**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.


### <a name="bkmk_EFAPSH"> </a> Использование параметра EncryptFormatAll с командлетом PowerShell
Используйте командлет [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) с параметром [EncryptFormatAll](https://www.powershellgallery.com/packages/AzureRM/5.0.0). 

**Шифрование на работающей виртуальной машине с использованием секрета клиента и параметра EncryptFormatAll.** Например, приведенный ниже сценарий инициализирует переменные и запускает командлет Set-AzureRmVMDiskEncryptionExtension с параметром EncryptFormatAll. Группа ресурсов, виртуальная машина, хранилище ключей, приложение AAD и секрет клиента должны быть уже созданы в качестве необходимых компонентов. Замените MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID и My-AAD-client-secret на ваши значения.
  
   ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Использование параметра EncryptFormatAll с диспетчером логических томов (LVM) 
Рекомендуем установить LVM-on-crypt. Для всех следующих примеров замените путь к устройству и точки подключения в соответствии с вашим вариантом использования. Эту настройку можно выполнить следующим образом.

- Добавить диски данных, которые составляют виртуальную машину.
- Форматировать, подключить и добавить эти диски в FSTAB-файл.

    1. Отформатируйте добавленный диск. Здесь мы используем символические ссылки, созданные Azure. Использование символических ссылок позволяет избежать проблем, связанных с изменениями имен устройств. Дополнительные сведения см. в статье [Устранение неполадок при изменении имени устройства виртуальной машины Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Подключите диски.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Добавьте сведения в FSTAB-файл.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Выполните командлет PowerShell Set-AzureRmVMDiskEncryptionExtension с параметром EncryptFormatAll для шифрования этих дисков.
         ```azurepowershell-interactive
         Set-AzureRmVMDiskEncryptionExtension -ResouceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Настройте диспетчер логических томов поверх новых дисков. Обратите внимание, что зашифрованные диски разблокируются после завершения загрузки виртуальной машины. Таким образом, подключение диспетчера логических томов также придется отложить.




## <a name="bkmk_VHDpre"> </a> Новые виртуальные машины IaaS, при создании которых используются зашифрованные виртуальные жесткие диски и ключи шифрования
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. В следующих разделах более подробно описаны шаблон Resource Manager и команды интерфейса командной строки. 

Следуйте инструкциям, приведенным в приложении к этой статье, чтобы подготовить предварительно зашифрованные образы, которые можно использовать в Azure. После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.

* [Подготовка предварительно зашифрованного виртуального жесткого диска Windows](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Подготовка предварительно зашифрованного виртуального жесткого диска Linux](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension, указав параметр -skipVmBackup. Команда Set-AzureRmVMDiskEncryptionExtension будет завершаться ошибкой при использовании с виртуальными машинами на основе управляемых дисков, пока не будет сделана резервная копия и указан определенный параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 



### <a name="bkmk_VHDprePSH"> </a> Использование Azure PowerShell для шифрования виртуальных машин IaaS с предварительно зашифрованными виртуальными жесткими дисками 
Можно включить шифрование дисков для зашифрованного виртуального жесткого диска с помощью командлета PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). Приведенный ниже пример имеет некоторые общие параметры. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```

### <a name="bkmk_VHDpreRM"> </a> Использование шаблона Resource Manager для шифрования виртуальных машин IaaS с предварительно зашифрованными виртуальными жесткими дисками 
Можно включить шифрование дисков для зашифрованного виртуального жесткого диска с помощью [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm).

1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите кнопку **Создать**, чтобы включить шифрование на новой виртуальной машине IaaS.

В следующей таблице перечислены параметры шаблона Resource Manager для зашифрованного виртуального жесткого диска.

| Параметр | ОПИСАНИЕ |
| --- | --- |
| newStorageAccountName | Имя учетной записи хранения, в которой будет размещен зашифрованный виртуальный жесткий диск ОС. Эта учетная запись хранения уже должна быть создана в той же группе ресурсов и в том же расположении, что и виртуальная машина. |
| osVhdUri | Универсальный код ресурса (URI) виртуального жесткого диска ОС в учетной записи хранения. |
| osType | Тип продукта ОС (Windows, Linux). |
| virtualNetworkName | Имя виртуальной сети, которой будет принадлежать сетевая карта виртуальной машины. Это имя уже должно быть создано в той же группе ресурсов и в том же расположении, что и виртуальная машина. |
| subnetName | Имя подсети виртуальной сети, которой будет принадлежать сетевая карта виртуальной машины. |
| vmSize | Размер виртуальной машины. Сейчас поддерживаются только серии A, D и G уровня "Стандартный". |
| keyVaultResourceID | Идентификатор ресурса, определяющий ресурс хранилища ключей в Azure Resource Manager. Его можно получить с помощью командлета PowerShell `(Get-AzureRmKeyVault -VaultName &lt;MyKeyVaultName&gt; -ResourceGroupName &lt;MyResourceGroupName&gt;).ResourceId` или с помощью команды Azure CLI `az keyvault show --name "MySecureVault" --query id`.|
| keyVaultSecretUrl | URL-адрес ключа шифрования дисков, настроенного в хранилище ключей. |
| keyVaultKekUrl | URL-адрес ключа шифрования ключей, с помощью которого шифруется созданный ключ шифрования дисков. |
| vmName | Имя виртуальной машины IaaS. |
## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Включение шифрования на добавленном диске данных
Новый диск данных можно добавить с помощью команды [az vm disk attach](../virtual-machines/linux/add-disk.md) или [через портал Azure](../virtual-machines/linux/attach-disk-portal.md). Прежде чем запустить шифрование, необходимо подключить только что присоединенный диск данных. Нужно запросить шифрование диска данных, так как диск будет неработоспособным во время шифрования. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Включение шифрования на добавленном диске данных с помощью интерфейса командной строки Azure
 Команда Azure CLI автоматически предоставит вам новую версию последовательности, когда вы запустите команду для включения шифрования. 
-  **Шифрование работающей виртуальной машины с использованием секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Включение шифрования на добавленном диске данных с помощью Azure PowerShell
 При использовании PowerShell для шифрования нового диска для Linux необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Приведенный ниже сценарий создает GUID для версии последовательности. 
 

-  **Шифрование работающей виртуальной машины с использованием секрета клиента.** Приведенный ниже сценарий инициализирует переменные и запускает командлет Set-AzureRmVMDiskEncryptionExtension. Группа ресурсов, виртуальная машина, хранилище ключей, приложение AAD и секрет клиента должны быть уже созданы в качестве необходимых компонентов. Замените MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID и My-AAD-client-secret на ваши значения. Для параметра -VolumeType устанавливаются диски данных, а не диск операционной системы. 

     ```azurepowershell-interactive
      $sequenceVersion = [Guid]::NewGuid();
      $rgName = 'MySecureRg';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
    ```
- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента.** Шифрование дисков Azure позволяет указать существующий ключ из хранилища ключей для упаковки секретов шифрования диска, которые были сгенерированы при включении шифрования. Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault. При шифровании дисков данных, а не диска операционной системы, может потребоваться добавить параметр -VolumeType. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 


## <a name="disable-encryption-for-linux-vms"></a>Отключение шифрования для виртуальных машин Linux
Вы можете отключить шифрование с помощью Azure PowerShell, Azure CLI или шаблона Resource Manager. 

>[!IMPORTANT]
>Отключение шифрования, выполняемого с помощью службы шифрования Azure, на виртуальных машинах Linux поддерживается только для томов данных. Если том операционной системы зашифрован, то отключение не поддерживается для томов данных и томов ОС.  

- **Отключение шифрования дисков с помощью Azure PowerShell.** Для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Отключение шифрования с помощью Azure CLI.** Для отключения шифрования используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Отключение шифрование с помощью шаблона Resource Manager.** Для отключения используйте шаблон [Отключение шифрования на работающей виртуальной машине Linux](https://aka.ms/decrypt-linuxvm).
     1. Нажмите кнопку **Развернуть в Azure**.
     2. Выберите подписку, группу ресурсов, расположение, виртуальную машину, условия использования и соглашение.
     3.  Нажмите кнопку **Покупка**, чтобы отключить шифрование дисков на работающей виртуальной Машине Windows. 


## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Включение шифрования дисков Azure для виртуальных машин Windows IaaS](azure-security-disk-encryption-windows.md)
