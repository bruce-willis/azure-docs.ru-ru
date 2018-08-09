---
title: Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Windows | Документация Майкрософт
description: В этой статье приведены инструкции по включению шифрования дисков Microsoft Azure для виртуальных машин IaaS под управлением Windows.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 8b3905c8-844f-4ec7-ad95-b386e9843053
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2018
ms.author: mstewart
ms.openlocfilehash: 772ab272603d0f8e0badf899c439592b20b7c8a3
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39392581"
---
#  <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Windows 

Возможно несколько сценариев включения шифрования дисков, и последовательность действий для каждого может отличаться. В следующих разделах сценарии для виртуальных машин IaaS под управлением Windows описаны более подробно. Чтобы использовать шифрование дисков Azure, нужно выполнить [эти предварительные требования](/articles/security/azure-security-disk-encryption-prerequisites.md). 

Создайте [моментальный снимок](../virtual-machines/windows/snapshot-copy-managed-disk.md) и (или) резервную копию перед шифрованием дисков. Резервные копии обеспечивают возможность восстановления в случае любого непредвиденного сбоя во время шифрования. Для виртуальных машин с управляемыми дисками необходимо создать резервную копию до начала шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension с параметром -skipVmBackup. Дополнительные сведения см. в статье [Резервное копирование и восстановление зашифрованных виртуальных машин с помощью службы Azure Backup](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
>Чтобы убедиться, что секреты шифрования не пересекают региональные границы, шифрованию дисков Azure требуется, чтобы хранилище ключей и виртуальные машины были расположены в одном регионе. Создайте и используйте хранилище ключей, которое находится в том же регионе, что и виртуальная машина, которую нужно зашифровать. 


## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Включение шифрования в новых виртуальных машинах IaaS, созданных с помощью Marketplace
Включить шифрование дисков на новой виртуальной машине IaaS под управлением Windows, созданной из образа Marketplace в Azure, можно с помощью шаблона Resource Manager. Этот шаблон создает зашифрованную виртуальную машину Windows на основе образа из коллекции Windows Server 2012.

1. На странице шаблона [Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите кнопку **Покупка**, чтобы развернуть новую виртуальную машину IaaS с включенным шифрованием.

3. После развертывания шаблона проверьте состояние шифрования виртуальной машины предпочитаемым способом.
     - Проверьте состояние с помощью Azure CLI, используя команду [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
         ```

     - Проверьте состояние с Azure PowerShell, используя командлет [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus). 

         ```azurepowershell-interactive
         Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
         ```

     -  Выберите виртуальную машину, а затем щелкните **Диски** под заголовком **Параметры**, чтобы проверить состояние шифрования на портале. На диаграмме в разделе **Шифрование** вы увидите, включено ли оно. 
           ![Портал Azure: шифрование дисков включено](./media/azure-security-disk-encryption/disk-encryption-fig2.png) В следующей таблице перечислены параметры шаблона Resource Manager для новых виртуальных машин, создаваемых с помощью Marketplace, которые используют идентификатор клиента Azure AD.

| Параметр | ОПИСАНИЕ | 
| --- | --- |
| adminUserName | Имя пользователя администратора виртуальной машины. |
| adminPassword | Пароль администратора виртуальной машины. |
| newStorageAccountName | Имя учетной записи хранения, в которой будут размещены виртуальные жесткие диски операционной системы и данных. |
| vmSize | Размер виртуальной машины. Сейчас поддерживаются только серии A, D и G уровня "Стандартный". |
| virtualNetworkName | Имя виртуальной сети, которой будет принадлежать сетевая карта виртуальной машины. |
| subnetName | Имя подсети виртуальной сети, которой будет принадлежать сетевая карта виртуальной машины. |
| AADClientID | Идентификатор клиента приложения Azure AD, которое имеет разрешения на запись секретов в ваше хранилище ключей. |
| AADClientSecret | Секрет клиента приложения Azure AD, которое имеет разрешения на запись секретов в ваше хранилище ключей. |
| keyVaultURL | URL-адрес хранилища ключей, в которое будет передан ключ BitLocker. Его можно получить с помощью командлета `(Get-AzureRmKeyVault -VaultName,-ResourceGroupName).VaultURI` или команды Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri`. |
| keyEncryptionKeyURL | URL-адрес ключа шифрования ключей, который используется для шифрования созданного ключа BitLocker (необязательно). </br> </br>KeyEncryptionKeyURL является необязательным параметром. Вы можете добавить собственный ключ шифрования ключей, чтобы дополнительно защитить ключ шифрования данных (секрет в виде парольной фразы) в своем хранилище ключей. |
| keyVaultResourceGroup | Группа ресурсов хранилища ключей. |
| vmName | Имя виртуальной машины, для которой будет выполняться шифрование. |


## <a name="bkmk_RunningWinVM"></a> Включение шифрования на виртуальной машине IaaS Windows, которая уже существует или работает
В этом сценарии шифрование можно включить с помощью шаблона, командлетов PowerShell или команд интерфейса командной строки. В следующих разделах более подробно описано, как включить шифрование дисков Azure. 

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension, указав параметр -skipVmBackup. Команда Set-AzureRmVMDiskEncryptionExtension будет завершаться ошибкой при использовании с виртуальными машинами на основе управляемых дисков, пока не будет сделана резервная копия и указан определенный параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Включение шифрования на существующих или работающих виртуальных машинах с помощью Azure PowerShell 
Используйте командлет [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension), чтобы включить шифрование на работающей виртуальной машине IaaS в Azure. Сведения о включении шифрования дисков Azure с помощью командлетов PowerShell см. в статьях [Explore Azure Disk Encryption with Azure Powershell](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/17/explore-azure-disk-encryption-with-azure-powershell.aspx) (Изучение возможностей шифрования дисков Azure с помощью PowerShell) и [Explore Azure Disk Encryption with Azure PowerShell – Part 2](http://blogs.msdn.com/b/azuresecurity/archive/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2.aspx) (Изучение возможностей шифрования дисков Azure с помощью PowerShell. Часть 2).

-  **Шифрование работающей виртуальной машины с использованием секрета клиента.** Приведенный ниже сценарий инициализирует переменные и запускает командлет Set-AzureRmVMDiskEncryptionExtension. Группа ресурсов, виртуальная машина, хранилище ключей, приложение AAD и секрет клиента должны быть уже созданы в качестве необходимых компонентов. Замените MySecureRg, MySecureVM, MySecureVault, My-AAD-client-ID и My-AAD-client-secret на ваши значения.
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
- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента.** Шифрование дисков Azure позволяет указать существующий ключ из хранилища ключей для упаковки секретов шифрования диска, которые были сгенерированы при включении шифрования. Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault. 

     ```azurepowershell-interactive
     $rgName = 'MySecureRg';
     $vmName = ‘MyExtraSecureVM’;
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
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName 'MySecureRg' -VMName 'MySecureVM'
     ```
    
- **Отключение шифрования дисков.** Для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a>Включение шифрования на существующих или работающих виртуальных машинах с помощью Azure CLI
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
     > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей]. </br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

- **Проверка того, что диски зашифрованы.** Чтобы проверить состояние шифрования виртуальной машины IaaS, используйте команду [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MySecureRg"
     ```

- **Отключение шифрования.** Чтобы отключить шифрование, используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
 
 > [!NOTE]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension, указав параметр -skipVmBackup. Эта команда будет завершаться ошибкой при использовании с виртуальными машинами на основе управляемых дисков, пока не будет сделана резервная копия и указан определенный параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 

### <a name="bkmk_RunningWinVMwRM"> </a>Использование шаблона Resource Manager
Включить шифрование дисков на существующих или работающих виртуальных машинах IaaS под управлением Windows в Azure можно с помощью [этого шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).


1. На странице шаблона быстрого запуска Azure нажмите кнопку **Deploy to Azure** (Развернуть в Azure).

2. Выберите подписку, группу ресурсов, расположение группы ресурсов, параметры, условия использования и соглашение. Нажмите кнопку **Покупка**, чтобы включить шифрование на существующей или работающей виртуальной машине IaaS.

В следующей таблице перечислены параметры шаблона Resource Manager для существующих или работающих виртуальных машин, которые используют идентификатор клиента Azure AD.

| Параметр | ОПИСАНИЕ |
| --- | --- |
| AADClientID | Идентификатор клиента приложения Azure AD, которое имеет разрешения на запись секретов в хранилище ключей. |
| AADClientSecret | Секрет клиента приложения Azure AD, которое имеет разрешения на запись секретов в хранилище ключей. |
| keyVaultName | Имя хранилища ключей, в которое будет передан ключ BitLocker. Его можно получить с помощью командлета `(Get-AzureRmKeyVault -ResourceGroupName <MyResourceGroupName>). Vaultname` или команды Azure CLI az keyvault list --resource-group "MySecureGroup" |ConvertFrom-JSON.|
|  keyEncryptionKeyURL | URL-адрес ключа шифрования ключей, который используется для шифрования созданного ключа BitLocker. Это необязательный параметр, если выбрать **nokek** из раскрывающегося списка UseExistingKek. Если из раскрывающегося списка UseExistingKek выбрано значение **kek**, то потребуется ввести значение _keyEncryptionKeyURL_. |
| volumeType | Тип тома, для которого будет выполняться шифрование. Допустимые значения: _OS_, _Data_ и _All_. |
| sequenceVersion | Версия последовательности операций с BitLocker. Этот номер версии увеличивается каждый раз, когда шифрование диска выполняется на той же виртуальной машине. |
| vmName | Имя виртуальной машины, для которой будет выполняться шифрование. |


## <a name="bkmk_VHDpre"> </a> Новые виртуальные машины IaaS, при создании которых используются зашифрованные виртуальные жесткие диски и ключи шифрования
В этом сценарии шифрование можно включить с помощью шаблона Resource Manager, командлетов PowerShell или команд интерфейса командной строки. В следующих разделах более подробно описаны шаблон Resource Manager и команды интерфейса командной строки. 

Следуйте инструкциям, приведенным в приложении к этой статье, чтобы подготовить предварительно зашифрованные образы, которые можно использовать в Azure. После создания образа выполните действия, описанные в следующем разделе, чтобы создать зашифрованную виртуальную машину Azure.

* [Подготовка предварительно зашифрованного виртуального жесткого диска Windows](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Подготовка предварительно зашифрованного виртуального жесткого диска Linux](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Нужно обязательно создать образ или резервную копию экземпляра виртуальной машины на основе управляемого диска вне шифрования дисков Azure и перед включением этой функции. Моментальный снимок управляемого диска можно создать с портала или использовать для этого [Azure Backup](../backup/backup-azure-vms-encryption.md). Резервные копии обеспечивают возможность восстановления при любом непредвиденном сбое во время шифрования. После создания резервной копии можно зашифровать управляемые диски с помощью командлета Set-AzureRmVMDiskEncryptionExtension, указав параметр -skipVmBackup. Команда Set-AzureRmVMDiskEncryptionExtension будет завершаться ошибкой при использовании с виртуальными машинами на основе управляемых дисков, пока не будет сделана резервная копия и указан определенный параметр. 
>
>Шифрование или отключение шифрования может привести к перезагрузке виртуальной машины. 



### <a name="bkmk_VHDprePSH"> </a> Шифрование виртуальных машин с предварительно зашифрованными виртуальными жесткими дисками с помощью Azure PowerShell
Можно включить шифрование дисков для зашифрованного виртуального жесткого диска с помощью командлета PowerShell [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk#examples). Приведенный ниже пример имеет некоторые общие параметры. 

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzureRmVM -VM $VirtualMachine -ResouceGroupName "MySecureRG"
```


### <a name="bkmk_VHDpreRM"> </a> Шифрование виртуальных машин IaaS с предварительно зашифрованными виртуальными жесткими дисками с использованием шаблона Resource Manager 
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
[Добавить новый диск на виртуальную машину Windows можно с помощью PowerShell](../virtual-machines/windows/attach-disk-ps.md) или [портала Azure](../virtual-machines/windows/attach-managed-disk-portal.md). 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Включение шифрования на добавленном диске данных с помощью Azure PowerShell
 При использовании Powershell для шифрования нового диска для виртуальных машин Windows необходимо указать новую версию последовательности. Версия последовательности должна быть уникальной. Приведенный ниже сценарий создает GUID для версии последовательности. В некоторых случаях новый добавленный диск данных может быть зашифрован автоматически с помощью расширения шифрования дисков Azure. В этом случае мы рекомендуем снова выполнить командлет Set-AzureRmVmDiskEncryptionExtension с новой версией последовательности.
 

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

      Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId  –SequenceVersion $sequenceVersion;
    ```
- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента.** Шифрование дисков Azure позволяет указать существующий ключ из хранилища ключей для упаковки секретов шифрования диска, которые были сгенерированы при включении шифрования. Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault. При шифровании дисков данных, а не диска операционной системы, может потребоваться добавить параметр -VolumeType. 

     ```azurepowershell-interactive
     $sequenceVersion = [Guid]::NewGuid();
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

     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Синтаксис значения параметра disk-encryption-keyvault — это полная строка идентификатора: /subscriptions/[ИД или GUID подписки]/resourceGroups/[имя группы ресурсов]/providers/Microsoft.KeyVault/vaults/[имя хранилища ключей].</br> Синтаксис значения параметра key-encryption-key —это полный универсальный код ресурса (URI) для ключа шифрования ключей: https://[имя хранилища ключей].vault.azure.net/keys/[имя KEK]/[ИД KEK]. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Включение шифрования на добавленном диске данных с помощью интерфейса командной строки Azure
 Команда Azure CLI автоматически предоставит вам новую версию последовательности, когда вы запустите команду для включения шифрования. 
-  **Шифрование работающей виртуальной машины с использованием секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Шифрование работающей виртуальной машины с применением ключа шифрования ключей для упаковки секрета клиента:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MySecureRg" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault"--key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Включение шифрования с использованием аутентификации на основе сертификата клиента Azure AD
Аутентификацию на основе сертификата клиента можно использовать с ключом шифрования ключей или без него. Для запуска сценариев требуется выполнить [предварительные требования для шифрования дисков Azure](azure-security-disk-encryption-prerequisites.md). Прежде чем использовать сценарии PowerShell, необходимо отправить сертификат в хранилище ключей и развернуть его на виртуальной машине. Если вы планируете использовать аутентификацию с ключом шифрования ключей, он также уже должен существовать. Дополнительные сведения см. в разделе [Аутентификация на основе сертификата (необязательно)](azure-security-disk-encryption-prerequisites.md#bkmk_Cert) статьи с предварительными требованиями.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Включение шифрования с использованием аутентификации на основе сертификата с помощью Azure PowerShell

```powershell
## Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = ‘MySecureVM’;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Включение шифрования с использованием аутентификации на основе сертификата и ключа шифрования ключей с помощью Azure PowerShell

```powershell
# Fill in 'MySecureRg', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$rgName = 'MySecureRg';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Отключение шифрования
Вы можете отключить шифрование с помощью Azure PowerShell, Azure CLI или шаблона Resource Manager. 

- **Отключение шифрования дисков с помощью Azure PowerShell.** Для отключения шифрования используйте командлет [Disable-AzureRmVMDiskEncryption](/powershell/module/azurerm.compute/disable-azurermvmdiskencryption). 
     ```azurepowershell-interactive
     Disable-AzureRmVMDiskEncryption -ResourceGroupName 'MySecureRG' -VMName 'MySecureVM'
     ```

- **Отключение шифрования с помощью Azure CLI.** Для отключения шифрования используйте команду [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable). 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MySecureRg" --volume-type [ALL, DATA, OS]
     ```
- **Отключение шифрования с помощью шаблона Resource Manager.** 

    1. Нажмите кнопку **Deploy to Azure** (Развернуть в Azure) на странице шаблона [отключения шифрования дисков на виртуальной машине под управлением Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm).
    2. Выберите подписку, группу ресурсов, расположение, виртуальную машину, условия использования и соглашение.
    3.  Нажмите кнопку **покупка**, чтобы отключить шифрование дисков на работающей виртуальной машине Windows. 

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Включение шифрования дисков Azure для виртуальных машин IaaS под управлением Linux](azure-security-disk-encryption-linux.md)