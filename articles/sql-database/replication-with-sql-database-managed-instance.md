---
title: Репликация с использованием Управляемого экземпляра Базы данных SQL Azure | Документация Майкрософт
description: Сведения о репликации SQL Server с использованием Управляемого экземпляра Базы данных SQL Azure
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 95c27bcc99f08cb1e4998e43a6a2abd508bee0ac
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228172"
---
# <a name="replication-with-sql-database-managed-instance"></a>Репликация с использованием Управляемого экземпляра Базы данных SQL

Репликация доступна в общедоступной предварительной версии в [Управляемом экземпляре Базы данных SQL Azure](sql-database-managed-instance.md). В Управляемом экземпляре может размещаться база данных издателя, распространителя и подписчика.

## <a name="common-configurations"></a>Распространенные конфигурации

Как правило, и издатель, и распространитель должны находиться либо в облаке, либо в локальной среде. Поддерживаются следующие конфигурации:

- **Издатель с локальным распространителем в управляемом экземпляре**

   ![Replication-with-azure-sql-db-single-managed-instance-publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   Базы данных издателя и распространителя можно настроить на одном управляемом экземпляре.

- **Издатель с удаленным распространителем в управляемом экземпляре**

   ![Replication-with-azure-sql-db-separate-managed-instances-publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   Издателя и распространителя можно настроить в двух управляемых экземплярах. В этой конфигурации:

  - Оба управляемые экземпляры находятся в той же виртуальной сети.

  - Оба управляемые экземпляры находятся в том же расположении.

- **Локальные издатель и распространитель с подписчиком в управляемом экземпляре**

   ![Replication-from-on-premises-to-azure-sql-db-subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   В этой конфигурации подписчиком является База данных SQL Azure. Эта конфигурация поддерживает миграцию из локальной среды в Azure. В роли подписчика базе данных SQL не нужен Управляемый экземпляр, однако вы можете использовать Управляемый экземпляр Базы данных SQL в качестве шага при миграции из локальной среды в Azure. Дополнительные сведения о подписчиках Базы данных SQL Azure см. в статье [Replication to SQL Database single and pooled databases](replication-to-sql-database.md) (Репликация в одну базу данных и базы данных в составе пула службы "База данных SQL").

## <a name="requirements"></a>Требования

Издателю и распространителю в Базе данных SQL Azure необходимо:

- Управляемый экземпляр Базы данных SQL Azure.

   >[!NOTE]
   >Базы данных SQL Azure, не настроенные с помощью Управляемого экземпляра, могут быть только подписчиками.

- Все экземпляры SQL Server должны находиться в одной виртуальной сети.

- При подключении используется аутентификация SQL между участниками репликации.

- Общий ресурс учетной записи хранения Azure для рабочей папки репликации.

## <a name="features"></a>Функции

Поддерживает:

- Сочетание экземпляров транзакционной репликации и репликации моментальных снимков в локальной среде и Управляемых экземплярах Базы данных SQL Azure.

- Подписчики могут быть отдельными локальными базами данных в Базе данных SQL Azure или базами данных в эластичных пулах Базы данных SQL Azure.

- Односторонняя или двунаправленная репликация

## <a name="configure-publishing-and-distribution-example"></a>Настройка примера публикации и распространения

1. На портале [создайте Управляемый экземпляр Базы данных SQL Azure](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal).

1. [Создайте учетную запись хранения Azure](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) для рабочей папки. Обязательно скопируйте ключи к хранилищу данных. Дополнительные сведения см. в статье [Создание учетной записи хранения](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys).

1. Создайте базу данных для издателя.

   В следующих сценариях замените `<Publishing_DB>` именем этой базы данных.

1. Создайте пользователя базы данных с использованием аутентификации SQL для распространителя. Дополнительные сведения см. в разделе [Создание пользователей базы данных](http://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users). Установите надежный пароль.

   В примерах сценариев ниже используйте `<SQL_USER>` и `<PASSWORD>` в качестве имени пользователя и пароля для этой учетной записи базы данных SQL Server.

1. [Установите подключение к Управляемому экземпляру Базы данных SQL](http://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

1. Выполните следующий запрос, чтобы добавить распространителя и базу данных распространителя.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

1. Чтобы издатель использовал указанную базу данных распространителя, обновите и запустите следующий запрос.

   Замените `<SQL_USER>` и `<PASSWORD>` на имя пользователя и пароль учетной записи SQL Server.

   Замените `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` значением своей учетной записи хранения.  

   Замените `<STORAGE_CONNECTION_STRING>` строкой подключения из вкладки **Ключи доступа** вашей учетной записи хранения Microsoft Azure.

   Обновив следующий запрос, запустите его. 

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

1. Настройте издателя для репликации. 

    В следующем запросе замените `<Publishing_DB>` именем базы данных издателя.

    Замените `<Publication_Name>` именем публикации.

    Замените `<SQL_USER>` и `<PASSWORD>` на имя пользователя и пароль учетной записи SQL Server.

    Обновив запрос, запустите его, чтобы создать публикацию.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

1. Добавьте статью, подписку и агент принудительной подписки. 

   Чтобы добавить эти объекты, обновите следующий сценарий.

   Замените `<Object_Name>` именем объекта публикации.

   Замените `<Object_Schema>` именем исходной схемы. 

   Замените другие параметры в угловых скобках `<>` значениями, используемыми в предыдущем сценарии. 

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>', 
                @job_password = N'<PASSWORD>'
   GO
   ```

## <a name="limitations"></a>Ограничения

Следующие возможности не поддерживаются:

- обновляемые подписки;

- активная георепликация.

## <a name="see-also"></a>См. также

- [Общие сведения об Управляемом экземпляре](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
