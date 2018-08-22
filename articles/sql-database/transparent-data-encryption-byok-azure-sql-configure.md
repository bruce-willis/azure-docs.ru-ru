---
title: PowerShell и интерфейс командной строки. Включение прозрачного шифрования данных SQL с собственным ключом (База данных SQL Azure) | Документация Майкрософт
description: Сведения о настройке прозрачного шифрования данных в Базе данных SQL Azure и хранилище данных с помощью PowerShell или интерфейса командной строки для шифрования данных при хранении.
services: sql-database
keywords: ''
documentationcenter: ''
author: aliceku
manager: craigg
editor: ''
ms.prod: ''
ms.reviewer: carlrab
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.devlang: azurecli, powershell
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 0dd0d322bacd807dc0cc59aeeb854c427a745bca
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043566"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-using-your-own-key-from-azure-key-vault"></a>PowerShell и интерфейс командной строки. Включение прозрачного шифрования данных с собственным ключом из Azure Key Vault

В этой статье рассматриваются способы применения ключа из Azure Key Vault для прозрачного шифрования данных (TDE) в Базе данных SQL или хранилище данных. Дополнительные сведения о TDE с поддержкой создания собственных ключей (BYOK) вы найдете в [этой статье](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Предварительные требования для PowerShell

- Подписка Azure и права администратора для нее.
- [Рекомендуется, но необязательно.] Аппаратный модуль безопасности (HSM) или локальное хранилище ключей для создания локальной копии материала ключа для предохранителя TDE.
- Установленная и запущенная среда Azure PowerShell 4.2.0 или более поздней версии. 
- Создайте Azure Key Vault и ключи для TDE.
   - [Инструкции для PowerShell и ключа из Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
   - [Инструкции по использованию аппаратного модуля безопасности (HSM) и Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started#a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm).
 - Чтобы использовать хранилище ключей для TDE, обязательно настройте для него следующее свойство:
   - [soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) (обратимое удаление).
   - [Как использовать обратимое удаление в Key Vault с помощью PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell) 
- Чтобы использовать ключ для TDE, обязательно настройте для него следующие атрибуты:
   - без даты окончания срока действия;
   - не отключено;
   - возможность выполнять операции *получения*, *упаковки ключа* и *распаковки ключа*.

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Шаг 1. Назначение серверу удостоверения Azure AD 

Если у вас есть существующий сервер, выполните следующие действия для присвоения этому серверу удостоверения Azure AD:

   ```powershell
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Если вы создаете новый сервер, используйте командлет [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) с тегом -Identity, чтобы добавить удостоверение Azure AD:

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Шаг 2. Предоставление серверу разрешений на доступ к Key Vault

Используйте командлет [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), чтобы предоставить серверу доступ к хранилищу ключей, прежде чем назначать ключ из этого хранилища для прозрачного шифрования данных.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Шаг 3. Добавление на сервер ключа из Key Vault и настройка предохранителя TDE

- Используйте командлет [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey), чтобы добавить на сервер ключ из Key Vault.
- Используйте командлет [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector), чтобы настроить ключ в качестве предохранителя TDE для всех ресурсов сервера.
- Используйте командлет [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector), чтобы проверить правильность настройки предохранителя TDE.

> [!Note]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.
> 

>[!Tip]
>Пример идентификатора KeyId из Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Шаг 4. Включение прозрачного шифрования данных 

Выполните командлет [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption), чтобы включить TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Теперь для базы данных или хранилища данных включено прозрачное шифрование данных с ключом шифрования из Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Шаг 5. Проверка состояния и активности шифрования

Используйте командлет [Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption), чтобы получить сведения о состоянии шифрования, и командлет [Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity), чтобы проверить ход выполнения шифрования для базы данных или хранилища данных.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Другие полезные командлеты PowerShell

- Командлет [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) позволяет отключить TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Командлет [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) возвращает список ключей из Key Vault, добавленных на указанный сервер.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Командлет [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) удаляет с сервера ключ Key Vault.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Устранение неполадок

При возникновении ошибок выполните следующие проверки.
- Если не удается найти хранилище ключей, с помощью командлета [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) убедитесь, что выбрана правильная подписка.

   ```powershell
   Get-AzureRmSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Если не удается добавить на сервер новый ключ или указать новый в качестве предохранителя TDE, проверьте следующее:
   - ключ не должен иметь дату окончания срока действия;
   - для ключа должны быть включены операции *получения*, *упаковки ключа* и *распаковки ключа*.

## <a name="next-steps"></a>Дополнительная информация

- Сведения о том, как заменить предохранитель TDE для сервера в соответствии с требованиями безопасности, приводятся в [этой статье](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Сведения о том, как удалить потенциально скомпрометированный предохранитель TDE в случае угрозы безопасности, вы найдете в [этой статье](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Предварительные требования для CLI

- Подписка Azure и права администратора для нее.
- [Рекомендуется, но необязательно.] Аппаратный модуль безопасности (HSM) или локальное хранилище ключей для создания локальной копии материала ключа для предохранителя TDE.
- Интерфейс командной строки версии 2.0 или более поздней версии. Сведения об установке последней версии и связывании ее с подпиской Azure можно найти в разделе [Установка Azure CLI 1.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
- Создайте Azure Key Vault и ключи для TDE.
   - [Управление Key Vault с помощью интерфейса командной строки 2.0](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2)
   - [Инструкции по использованию аппаратного модуля безопасности (HSM) и Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started#a-idhsmaif-you-want-to-use-a-hardware-security-module-hsm)
 - Чтобы использовать хранилище ключей для TDE, обязательно настройте для него следующее свойство:
   - [soft-delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) (обратимое удаление).
   - [Как использовать обратимое удаление в Key Vault с помощью CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli) 
- Чтобы использовать ключ для TDE, обязательно настройте для него следующие атрибуты:
   - без даты окончания срока действия;
   - не отключено;
   - возможность выполнять операции *получения*, *упаковки ключа* и *распаковки ключа*.
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>Шаг 1. Создание сервера и назначение ему удостоверения Azure AD
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -I 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Шаг 2. Предоставление серверу разрешений на доступ к Key Vault
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Шаг 3. Добавление на сервер ключа из Key Vault и настройка предохранителя TDE
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> Общая длина имени хранилища ключей и имени ключа не может превышать 94 символа.
> 

>[!Tip]
>Пример идентификатора KeyId из Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>Шаг 4. Включение прозрачного шифрования данных 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

Теперь для базы данных или хранилища данных включено прозрачное шифрование данных с ключом шифрования из Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Шаг 5. Проверка состояния и активности шифрования

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>Справочник по CLI для SQL

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

