---
title: Прозрачное шифрование данных для хранилища данных и Базы данных SQL Azure | Документация Майкрософт
description: Обзор прозрачного шифрования данных для хранилища данных и Базы данных SQL. В этом документе описаны преимущества и параметры конфигурации прозрачного шифрования, включая управляемое службой прозрачное шифрование данных и создание собственных ключей.
services: sql-database
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: carlrab
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: aliceku
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: 0ed05fd2d55f1c4c80bec9f64925be2eddddc067
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2018
ms.locfileid: "40043573"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Прозрачное шифрование данных для хранилища данных и Базы данных SQL

Прозрачное шифрование данных (TDE) помогает защищать Базу данных SQL Azure и хранилище данных Azure от вредоносных действий. Выполняется шифрование и расшифровка базы данных, связанных резервных копий и неактивных файлов журналов транзакций в реальном времени без необходимости изменения приложения. По умолчанию TDE включено для всех вновь развертываемых Баз данных SQL Azure. Прозрачное шифрование данных нельзя использовать для шифрования логической базы данных **master** в Базе данных SQL.  База данных **master** содержит объекты, которые необходимы для выполнения операций TDE для пользовательских баз данных.

TDE для более старых баз данных и (или) для Хранилища данных SQL Azure необходимо включать вручную.  

При использовании прозрачного шифрования данных хранилище всей базы данных шифруется с помощью симметричного ключа, который называется ключом шифрования базы данных. Этот ключ шифрования базы данных защищен предохранителем прозрачного шифрования данных. Предохранитель представляет собой управляемый службой сертификат (управляемое службой прозрачное шифрование данных) или асимметричный ключ, сохраненный в Azure Key Vault (создание собственных ключей). Предохранитель прозрачного шифрования данных настраивается на уровне сервера. 

При запуске базы данных зашифрованный ключ шифрования базы данных расшифровывается. Далее он используется в работе ядра СУБД SQL Server для расшифровки и повторного шифрования файлов баз данных. С помощью TDE выполняется шифрование и расшифровка ввода-вывода на уровне страниц данных в реальном времени. Каждая страница расшифровывается при считывании в память, а затем снова шифруется перед записью на диск. Общие сведения о прозрачном шифровании данных см. в [этой статье](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server на виртуальной машине Azure также может использовать асимметричный ключ из Key Vault. Этапы настройки в этом случае будут отличаться от настройки асимметричного ключа, сохраненного в Базе данных SQL. Дополнительные сведения см. в статье о [расширенном управлении ключами с помощью Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Управляемое службой прозрачное шифрование данных

В Azure для TDE ключ шифрования базы данных по умолчанию защищается встроенным сертификатом сервера. Для каждого сервера Azure используется уникальный встроенный сертификат. Если база данных находится в отношении георепликации, ключ родительского сервера базы данных-источника используется как для базы данных-источника, так и для базы данных-получателя. Если две базы данных подключены к одному и тому же серверу, то они совместно используют один встроенный сертификат. Корпорация Майкрософт автоматически изменяет эти сертификаты не реже, чем раз в 90 дней.

Корпорация Майкрософт гарантирует простое перемещение ключей и управление ими для георепликации и восстановления. 

> [!IMPORTANT]
> По умолчанию все создаваемые базы данных SQL шифруются с помощью управляемого службой прозрачного шифрования данных. Базы данных, созданные до мая 2017 г. и базы данных, созданные путем восстановления, георепликации или копирования, по умолчанию не шифруются.
>

## <a name="bring-your-own-key"></a>Создание собственных ключей

Поддержка создания собственных ключей позволяет вам самостоятельно управлять ключами для TDE и устанавливать, кто и когда может осуществлять к ним доступ. Облачная система управления внешними ключами Azure Key Vault стала первой службой управления ключами, в которой прозрачное шифрование данных интегрировано с поддержкой создания собственных ключей. Поддержка создания собственных ключей позволяет защитить ключ шифрования базы данных асимметричным ключом, хранящимся в Key Vault. Этот асимметричный ключ никогда не покидает Key Vault. Если у сервера есть разрешение на доступ к хранилищу ключей, сервер отправляет запросы на основные операции с ключом в соответствующем Key Vault. Асимметричный ключ настраивается на уровне сервера и наследуется всеми базами данных на этом сервере.

Теперь, благодаря поддержке создания собственных ключей, вы можете управлять такими задачами управления ключами, как смена ключа и настройка разрешений для хранилища ключей. Можно также удалить ключи и (или) включить аудит либо отчетность по всем ключам шифрования. Key Vault обеспечивает централизованное управление ключами и использует строго отслеживаемые аппаратные модули безопасности. В Key Vault поддерживается разделение задач управления ключами и данными для обеспечения соответствия нормативным требованиям. Чтобы больше узнать о Key Vault, перейдите к [странице документации по Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

Дополнительные сведения о TDE с поддержкой создания собственных ключей для Базы данных SQL и хранилища данных, можно найти в [этой статье](transparent-data-encryption-byok-azure-sql.md).

Чтобы реализовать TDE с созданием собственных ключей из Key Vault с помощью PowerShell, изучите [это практическое руководство](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Перемещение базы данных, защищенной прозрачным шифрованием данных

Для операций в пределах Azure базу данных не нужно расшифровывать. Параметры TDE прозрачно переносятся из базы данных-источника в базу данных-получатель. Это относится ко всем следующим операциям:
- геовосстановление;
- восстановление на определенный момент времени через интерфейс самообслуживания;
- восстановление удаленной базы данных;
- активная георепликация;
- создание копии базы данных.

При экспорте базы данных, защищенной прозрачным шифрованием данных, экспортируемое содержимое базы данных не шифруется. Экспортированное содержимое хранится в незашифрованных BACPAC-файлах. Не забудьте обеспечить достаточную защиту для BACPAC-файлов и включить TDE по завершении импорта базы данных.

Например, когда BACPAC-файл экспортируется из локального экземпляра SQL Server, импортированное содержимое новой базы данных не шифруется автоматически. Когда BACPAC-файл экспортируется в локальный экземпляр SQL Server, новая база данных также не шифруется автоматически.

Единственное исключение — экспорт из базы данных SQL в базу данных SQL. В этом случае для новой базы данных будет включено прозрачное шифрование данных, но сам BACPAC-файл по-прежнему не шифруется.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Управление прозрачным шифрованием данных на портале Azure

Чтобы настроить TDE на портале Azure, подключитесь от имени владельца, участника или администратора безопасности SQL Azure. 

Прозрачное шифрование данных настраивается на уровне базы данных. Чтобы включить TDE для базы данных, войдите на [портал Azure](https://portal.azure.com) с учетной записью администратора или участника Azure. Настройки прозрачного шифрования данных представлены в разделе информации о пользовательской базе данных. По умолчанию используется управляемое службой прозрачное шифрование данных. Для сервера, который содержит базу данных, автоматически создается сертификат TDE. 

![Управляемое службой прозрачное шифрование данных](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Главный ключ прозрачного шифрования данных ( также называется предохранителем прозрачного шифрования данных) задается на уровне сервера. Чтобы использовать TDE с поддержкой создания собственных ключей и защитить базы данных с помощью ключа, сохраненного в Key Vault, откройте параметры TDE для своего сервера. 

![Прозрачное шифрование данных с поддержкой создания собственных ключей](./media/transparent-data-encryption-azure-sql/tde-byok-support.png) 

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Управление прозрачным шифрованием данных с помощью PowerShell

Чтобы настроить TDE на портале Azure, подключитесь от имени владельца, участника или администратора безопасности SQL Azure. 

| Командлет | ОПИСАНИЕ |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |Включение или отключение TDE для базы данных|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |Получение сведений о состоянии TDE для базы данных |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |Проверка хода шифрования для базы данных |
| [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |Добавление ключа Key Vault в экземпляр SQL Server |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |Получение ключей Key Vault для экземпляра SQL Server  |
| [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |Настройка предохранителя TDE для экземпляра SQL Server |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |Получение предохранителя TDE |
| [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |Удаление ключа Key Vault из экземпляра SQL Server |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Управление прозрачным шифрованием данных с помощью Transact-SQL

Подключитесь к базе данных, указав имя входа администратора или члена роли **dbmanager** в базе данных master.

| Get-Help | ОПИСАНИЕ |
| --- | --- |
| [ALTER DATABASE (база данных SQL Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF — шифрование или расшифровка базы данных |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Возврат сведений о состоянии шифрования для базы данных и связанных с ней ключей шифрования |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Возврат сведений о состоянии для узла хранилища данных и связанных с ней ключей шифрования | 
|  | |

Transact-SQL не позволяет переключиться с предохранителя TDE на ключ из Key Vault. Используйте для этого портал Azure или PowerShell.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Управление прозрачным шифрованием данных с помощью REST API
 
Чтобы настроить TDE через REST API, подключитесь от имени владельца, участника или администратора безопасности SQL Azure. 

| Get-Help | ОПИСАНИЕ |
| --- | --- |
|[Создание или обновление сервера](/rest/api/sql/servers/createorupdate)|Добавление удостоверения Azure Active Directory для экземпляра SQL Server (используется для предоставления доступа к Key Vault)|
|[Создание или обновление ключа сервера](/rest/api/sql/serverkeys/createorupdate)|Добавление ключа Key Vault в экземпляр SQL Server|
|[Удаление ключа сервера](/rest/api/sql/serverkeys/delete)|Удаление ключа Key Vault из экземпляра SQL Server|
|[Получение ключей сервера](/rest/api/sql/serverkeys/get)|Получение определенного ключа Key Vault из экземпляра SQL Server|
|[Список ключей серверов, упорядоченный по серверам](/rest/api/sql/serverkeys/listbyserver)|Получение ключей Key Vault для экземпляра SQL Server |
|[Создание или обновление предохранителя шифрования](/rest/api/sql/encryptionprotectors/createorupdate)|Настройка предохранителя TDE для экземпляра SQL Server|
|[Получение предохранителя шифрования](/rest/api/sql/encryptionprotectors/get)|Получение предохранителя TDE для экземпляра SQL Server|
|[Список предохранителей шифрования, упорядоченный по серверам](/rest/api/sql/encryptionprotectors/listbyserver)|Получение всех предохранителей TDE для экземпляра SQL Server |
|[Создание или обновление конфигурации TDE](/rest/api/sql/transparentdataencryptions/createorupdate)|Включение или отключение TDE для базы данных|
|[Получение конфигурации TDE](/rest/api/sql/transparentdataencryptions/get)|Получение конфигурации TDE для базы данных|
|[Список результатов TDE, упорядоченный по конфигурации](/rest/api/sql/transparentdataencryptionactivities/ListByConfiguration)|Получение результата шифрования для базы данных|

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с общими сведениями о прозрачном шифровании данных ((https://docs.microsoft.com/sql/relational-databases/security/transparent-data-encryption).
- Дополнительные сведения о TDE с поддержкой создания собственных ключей для Базы данных SQL и хранилища данных, можно найти в [этой статье](transparent-data-encryption-byok-azure-sql.md).
- Чтобы реализовать TDE с созданием собственных ключей из Key Vault с помощью PowerShell, изучите [это практическое руководство](transparent-data-encryption-byok-azure-sql-configure.md).
- Чтобы больше узнать о Key Vault, перейдите к [странице документации по Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
