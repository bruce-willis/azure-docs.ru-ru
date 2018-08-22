---
title: PowerShell — удаление предохранителя TDE — База данных SQL Azure | Документация Майкрософт
description: Практическое руководство по реагированию на потенциальную компрометацию предохранителя TDE для базы данных SQL Azure или хранилища данных, для которых настроено прозрачное шифрование данных (TDE) с поддержкой создания собственных ключей (BYOK).
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: ''
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: feb187101ec02d6e765d6b025f518dc416f55b8b
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043553"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Удаление предохранителя TDE с помощью PowerShell
## <a name="prerequisites"></a>Предварительные требования
- Подписка Azure и права администратора для нее.
- Установленная и запущенная среда Azure PowerShell 4.2.0 или более поздней версии. 
- В этом практическом руководстве предполагается, что вы уже используете ключ из Azure Key Vault в качестве предохранителя TDE для Базы данных SQL Azure или хранилища данных. См. дополнительные сведения о [прозрачном шифровании данных с поддержкой создания собственных ключей](transparent-data-encryption-byok-azure-sql.md).

## <a name="overview"></a>Обзор
Это практическое руководство описывает, как правильно реагировать на потенциальную компрометацию предохранителя TDE для базы данных SQL Azure или хранилища данных, для которых настроено прозрачное шифрование данных (TDE) с поддержкой создания собственных ключей (BYOK). См. дополнительные сведения о [поддержке BYOK для TDE](transparent-data-encryption-byok-azure-sql.md). 

Следующие процедуры следует выполнять только в крайних случаях или тестовых средах. Внимательно изучите это практическое руководство, так как удаление используемых предохранителей TDE из Azure Key Vault может привести к **потере данных**. 

Если возникнут любые подозрения по поводу компрометации ключа, например путем несанкционированного доступа пользователей и (или) служб к этому ключу, его следует удалить.

Но при этом не забывайте, что сразу после удаления предохранителя TDE в Key Vault **блокируются все подключения к зашифрованным базам данных на сервере, и эти базы данных переходят в автономный режим, а затем удаляются в течение 24 часов**. Старые резервные копии, зашифрованные с помощью скомпрометированного ключа, становятся недоступными.

В этом практическом руководстве рассматриваются два подхода в зависимости от результата, которого вы хотите добиться с помощью реагирования:
- вы можете **сохранить доступность** Баз данных SQL Azure и (или) хранилищ данных;
- вы можете **заблокировать доступность** Баз данных SQL Azure и (или) хранилищ данных.

## <a name="to-keep-the-encrypted-resources-accessible"></a>Сохранение доступности зашифрованных ресурсов
1. Создайте [ключ в Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0). Убедитесь, что новый ключ успешно создан в другом хранилище. Нельзя использовать то же хранилище, где размещен скомпрометированный предохранитель TDE, так как управление доступом предоставляется на уровне хранилища. 
2. Добавьте на сервер новый ключ с помощью командлетов [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) и [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector), а затем настройте его в качестве нового предохранителя TDE для этого сервера.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Убедитесь, что сервер и все реплики обновлены для использования нового предохранителя TDE с помощью командлета [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector). 

   >[!NOTE]
   > Распространение нового предохранителя TDE на все базы данных-источники и базы данных-получатели для обновляемого сервера может занять несколько минут.
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Создайте [резервную копию нового ключа](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey) в Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Удалите скомпрометированный ключ из Key Vault с помощью командлета [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey). 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. В будущем вы сможете восстановить ключ в Key Vault с помощью командлета [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey):
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>Блокирование доступности зашифрованных ресурсов
1. Удалите все базы данных, которые зашифрованы с помощью потенциально скомпрометированного ключа.
Для файлов базы данных и журналов резервные копии создаются автоматически, поэтому вы всегда сможете восстановить базу данных на определенный момент времени, предоставив нужный ключ. Удаление баз данных перед удалением действующего предохранителя TDE нужно для того, чтобы предотвратить потерю данных из последних транзакций (до 10 минут). 
2. Создайте в Key Vault резервную копию материала ключа для предохранителя TDE.
3. Удалите потенциально скомпрометированный ключ из Key Vault.

## <a name="next-steps"></a>Дополнительная информация

- См. дополнительные сведения о том, как [заменить предохранитель TDE для сервера в соответствии с требованиями к безопасности с помощью PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).

- См. дополнительные сведения о [включении прозрачного шифрования данных с помощью собственного ключа из Key Vault с помощью PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
