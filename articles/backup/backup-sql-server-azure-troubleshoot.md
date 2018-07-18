---
title: Руководство по устранению неполадок Azure Backup для виртуальных машин SQL Server | Документация Майкрософт
description: Сведения об устранении неполадок при резервном копировании виртуальных машин SQL Server в Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 1c87382c2aae70b022fb391f80f7c75b0a4e5fe6
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296961"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Устранении неполадок резервного копирования SQL Server в Azure

В этой статье приведены сведения об устранении неполадок для защиты виртуальных машин SQL Server в Azure (предварительная версия).

## <a name="public-preview-limitations"></a>Ограничения общедоступной предварительной версии

Ограничения общедоступной предварительной версии см. в [этом разделе](backup-azure-sql-database.md#public-preview-limitations).

## <a name="sql-server-permissions"></a>Разрешения SQL Server

Чтобы настроить защиту базы данных SQL Server на виртуальной машине, на этой виртуальной машине должно быть установлено расширение **AzureBackupWindowsWorkload**. Если вы получили сообщение об ошибке, **UserErrorSQLNoSysadminMembership**, это означает, что ваш экземпляр SQL не имеет необходимых разрешений на резервное копирование. Чтобы устранить эту ошибку, выполните действия, описанные в разделе [Установка разрешений для виртуальных машин SQL, не поддерживающих marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

## <a name="troubleshooting-errors"></a>Устранение ошибок

Используйте сведения в следующих таблицах для устранения неполадок и ошибок, возникающих при защите SQL Server в Azure.

## <a name="backup-failures"></a>Сбои резервного копирования

Следующие таблицы организованы в соответствии с кодом ошибки.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| This SQL database does not support the requested backup type. (Эта база данных SQL не поддерживает запрашиваемый тип резервного копирования.) | Возникает, когда модель восстановления базы данных не разрешает запрашиваемый тип резервного копирования. Ошибка может произойти в следующих ситуациях: <br/><ul><li>База данных, использующая простую модель восстановления, не позволяет создавать резервные копии журналов.</li><li>Разностные резервные копии и резервные копии журналов не допускаются для базы данных Master.</li></ul>Дополнительные сведения см. в статье [Модели восстановления (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server). | Если резервная копия журнала базы данных завершилась сбоем в простой модели восстановления, попробуйте один из следующих вариантов:<ul><li>Если база данных находится в режиме простого восстановления, отключите резервные копии журналов.</li><li>Используйте [документацию SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server), чтобы изменить модель восстановления базы данных на модель с полным или не полным протоколированием. </li><li> Если вы не хотите изменять модель восстановления, и у вас есть стандартная политика резервного копирования нескольких баз данных, которую нельзя изменить, игнорируйте ошибку. Полные и разностные резервные копии будут работать по расписанию. Резервные копии журналов будут пропущены, что ожидаемо в этом случае.</li></ul>Если это база данных Master, и вы настроили разностное резервное копирование или резервное копирование журналов, выполните одно из следующих действий:<ul><li>Используйте портал, чтобы изменить расписание политики резервного копирования для базы данных Master, на полное копирование.</li><li>Если у вас есть стандартная политика резервного копирования нескольких баз данных, которую нельзя изменить, игнорируйте ошибку. Ваше полное резервное копирование будет работать по расписанию. Разностное резервное копирование или резервное копирование журналов не произойдет, что ожидаемо в этом случае.</li></ul> |
| Operation canceled as a conflicting operation was already running on the same database. (Операция отменена, так как конфликтная операция уже выполняется в одной базе данных.) | См. [запись в блоге о резервных копиях и ограничениях восстановления](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database), которые запускаются одновременно.| [Используйте SQL Server Management Studio (SSMS) для мониторинга заданий резервного копирования.](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms) Если конфликтная операция завершится сбоем, перезапустите ее.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| SQL database does not exist. (База данных SQL не существует.) | База данных была либо удалена, либо переименована. | <ul><li>Проверьте, не была ли база данных случайно удалена или переименована.</li><li>Если база данных была случайно удалена, чтобы продолжить резервное копирование, восстановите базу данных в ее исходном расположении.</li><li>Если вы удалили базу данных и не нуждаетесь в будущих резервных копиях, то в хранилище служб восстановления выберите [удалить или сохранить данные](backup-azure-sql-database.md#manage-azure-backup-operations-for-sql-on-azure-vms), чтобы остановить резервное копирование.</li>|

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| Log chain is broken. (Цепочка журналов прерывается.) | База данных или виртуальная машина резервируются с использованием другого решения для резервного копирования, которое усекает цепочку журналов.|<ul><li>Проверьте, используется ли другое решение или скрипт резервного копирования. В этом случае остановите резервное копирование. </li><li>Если резервная копия была резервной копией журналов ad-hoc, активируйте полную резервную копию, чтобы запустить новую цепочку журналов. Для запланированных резервных копий журнала никаких действий не требуется, так как служба Azure Backup автоматически запускает полное резервное копирование, чтобы исправить эту проблему.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| Azure Backup is not able to connect to the SQL instance. (Azure Backup не может подключиться к экземпляру SQL.) | Служба Azure Backup не может подключиться к экземпляру SQL. | Используйте дополнительные сведения в меню ошибок портала Azure, чтобы сузить круг возможных причин проблемы. Дополнительные сведения см. в статье [Устранение неполадок при соединении с компонентом SQL Server Database Engine](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine).<br/><ul><li>Если параметры SQL по умолчанию не позволяют выполнять удаленные подключения, измените их. Чтобы изменить параметры, см. следующие ссылки.<ul><li>[https://msdn.microsoft.com/library/bb326495.aspx](https://msdn.microsoft.com/library/bb326495.aspx)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Если есть проблемы со входом, перейдите по ссылкам ниже, чтобы их исправить:<ul><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| First full backup is missing for this data source. (Полная резервная копия отсутствует для этого источника данных.) | Полная резервная копия для базы данных отсутствует. Разностные резервные копии и резервные копии журналов являются родительскими для полной резервной копии, поэтому перед их запуском необходимо сделать полную резервную копию. | Активируйте полное резервное копирование ad-hoc.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| Невозможно выполнить резервное копирование, так как журнал транзакций для источника данных заполнен. | Пространство журнала транзакций базы данных заполнено. | Чтобы устранить эту проблему, см. [документацию по SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |
| This SQL database does not support the requested backup type. (Эта база данных SQL не поддерживает запрашиваемый тип резервного копирования.) | Вторичные реплики группы доступности Always On не поддерживают полное и разностное резервное копирование. | <ul><li>Если вы инициировали резервное копирование ad-hoc, запускайте резервные копии на основном узле.</li><li>Если резервная копия была запланирована политикой, убедитесь, что основной узел зарегистрирован. Чтобы зарегистрировать узел, [выполните действия по обнаружению базы данных SQL Server](backup-azure-sql-database.md#discover-sql-server-databases).</li></ul> | 

## <a name="restore-failures"></a>Сбои при восстановлении

Следующие коды ошибок отображаются при сбое задания восстановления.

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite 

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| Database with same name already exists at the target location (База данных с тем же именем уже существует в целевом расположении) | В целевом расположении для восстановления уже имеется база данных с тем же именем.  | <ul><li>Измените имя целевой базы данных</li><li>или используйте параметр принудительной перезаписи на странице восстановления</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| Restore failed as the database could not be brought offline. (Не удалось выполнить восстановление, так как база данных не может быть отключена.) | При восстановлении целевая база данных должна быть отключена. Azure Backup не может переносить эти данные в автономном режиме. | Используйте дополнительные сведения в меню ошибок портала Azure, чтобы сузить круг возможных причин проблемы. Дополнительные сведения см. в [документации по SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |


###  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| Cannot find the server certificate with thumbprint on the target. (Не удается найти сертификат сервера с отпечатком на целевом сервере.) | База данных Master в экземпляре среды назначения не имеет действительного отпечатка шифрования. | Импортируйте действительный отпечаток сертификата, используемый в исходном экземпляре, в целевой экземпляр. |

## <a name="registration-failures"></a>Ошибки регистрации

Следующие коды ошибок предназначены для индикации ошибок регистрации.

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError 

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| Backup preference for SQL Always On Availability Group cannot be met as some nodes of the Availability Group are not registered. (Требование резервного копирования для группы доступности SQL Always On не может быть выполнено, поскольку некоторые узлы группы доступности не зарегистрированы.) | Узлы, необходимые для выполнения резервного копирования, не зарегистрированы или недоступны. | <ul><li>Убедитесь, что все узлы, необходимые для выполнения резервного копирования этой базы данных, зарегистрированы и работоспособны, а затем повторите операцию.</li><li>Измените предпочитаемый тип резервного копирования группы доступности SQL Always On.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| SQL server VM is either shutdown and not accessible to Azure Backup service. (Виртуальная машина SQL server либо отключена, либо недоступна службе Azure Backup.) | Виртуальная машина завершила работу. | Убедитесь, что SQL Server запущен. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Сообщение об ошибке | Возможные причины | Рекомендуемое действие |
|---|---|---|
| Служба Azure Backup использует гостевой агент виртуальной машины Azure VM для выполнения резервного копирования, но агент недоступен на целевом сервере. | Гостевой агент отключен или неисправен | [Установите гостевой агент виртуальной машины](../virtual-machines/extensions/agent-windows.md) вручную. |

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о службе Azure Backup для виртуальных машин SQL Server (предварительная версия) см. в [этом разделе](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).