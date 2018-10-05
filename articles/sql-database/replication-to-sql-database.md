---
title: Репликация в Базу данных SQL Azure | Документация Майкрософт
description: Сведения об использовании репликации SQL Server с отдельными базами данных и базами данных в эластичных пулах службы "База данных SQL Azure".
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
ms.openlocfilehash: 360aa53a02beabccc685df97ad8549dfb88ff5c8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228255"
---
# <a name="replication-to-sql-database-single-and-pooled-databases"></a>Репликация в отдельную базу данных и в базы данных в составе пула службы "База данных SQL Azure"

На [логическом сервере](sql-database-logical-servers.md) в Базе данных SQL Azure репликацию SQL Server можно настроить как для отдельной базы данных, так и для баз данных в составе пула.  
 
 ### <a name="supported-configurations"></a>**Поддерживаемые конфигурации**  
 -  SQL Server может быть экземпляром SQL Server, работающим локально или на виртуальной машине Azure в облаке. Дополнительные сведения см. на странице [Что собой представляет SQL Server на виртуальных машинах Azure (Windows)](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/).  
 - База данных SQL Azure должна быть подписана на сообщения, отправляемые издателем SQL Server.  
 -  Базу данных распространителя и агенты репликации нельзя разместить в Базе данных SQL Azure.  
 - Поддерживается репликация моментальных снимков и односторонняя репликация транзакций. Одноранговая репликация транзакций и репликация слиянием не поддерживаются.
 - Репликация доступна в общедоступной предварительной версии в Управляемом экземпляре Базы данных SQL Azure. В Управляемом экземпляре может размещаться база данных издателя, распространителя и подписчика. Дополнительные сведения см. в статье [Replication to SQL Database single and pooled databases](replication-with-sql-database-managed-instance.md) (Репликация в одну базу данных и базы данных в составе пула службы "База данных SQL").
 
## <a name="versions"></a>Версии  
- У издателя и распространителя должна быть одна из следующих версий:  
 - SQL Server 2017 (14.x); 
 - SQL Server 2016 (13.x); 
 - SQL Server 2014 (12.x) с пакетом обновления 1 (SP1) и накопительным пакетом обновления 3 (CU3); 
 - SQL Server 2014 (12.x) RTM с накопительным пакетом обновления 10 (CU10); 
 - SQL Server 2012 (11.x) с пакетом обновления 2 (SP2) и накопительным пакетом обновления 8 (CU8) или пакетом обновления 3 (SP3). 
   
- Попытка настроить репликацию с использованием более старой версии может привести к ошибке MSSQL_REPL20084 (The process could not connect to Subscriber (Процессу не удалось подключиться к Подписчику)) или MSSQL_REPL40532 ("Не удается открыть сервер \<имя>, запрашиваемый с использованием имени для входа. Вход в систему не выполнен").  
- Чтобы использовать все функции Базы данных SQL Azure, необходимо использовать последние версии [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) и [SQL Server Data Tools](https://docs.microsoft.com//sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017).  
   
 ## <a name="remarks"></a>Примечания  
- Репликацию можно настроить с помощью [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) или путем выполнения инструкций Transact-SQL в издателе. Репликацию невозможно настроить с помощью портала Azure.  
- Чтобы подключиться к Базе данных SQL, во время репликации могут использоваться только имена входа для аутентификации SQL Server. 
- У реплицируемых таблиц должен быть первичный ключ.  
- У вас должна быть подписка Azure.  
- Подписчик базы данных SQL Azure может находиться в любом регионе.  
- Одна публикация на сервере SQL Server может поддерживать подписчиков и Базы данных SQL Azure, и SQL Server (локально и SQL Server на виртуальной машине Azure).  
- Управление репликацией, ее мониторинг и устранение неполадок должны выполняться на локальном SQL Server.  
- В Базе данных SQL Azure поддерживаются только принудительные подписки.  
- В **sp_addsubscription** для Базы данных SQL поддерживается только `@subscriber_type = 0`.  
- База данных SQL Azure не поддерживает двунаправленную, немедленную, обновляемую или одноранговую репликацию.      
   
 ## <a name="replication-architecture"></a>Архитектура репликации  
 ![Репликация в Базу данных SQL](./media/replication-to-sql-database/replication-to-sql-database.png)  
   
 ## <a name="scenarios"></a>Сценарии  
   
 ### <a name="typical-replication-scenario"></a>Распространенный сценарий репликации  
   
 1.  Создайте публикацию репликации транзакций в базе данных на локальном сервере SQL Server.  
   
 2.  На локальном сервере SQL Server используйте **мастер создания подписок** или инструкции Transact-SQL, чтобы создать принудительную подписку на Базу данных SQL Azure.  
   
 3.  Исходный набор данных обычно является моментальным снимком, который создается агентом моментальных снимков, а затем распределяется и применяется агентом распространения. Исходный набор данных может также предоставляться с использованием резервной копии или других средств, таких как SQL Server Integration Services.  
   
 ### <a name="data-migration-scenario"></a>Сценарий переноса данных  
   
 1.  Используйте репликацию транзакций для репликации данных из локальной базы данных SQL Server в Базу данных SQL Azure.  
   
 2.  Перенаправьте клиент или приложения среднего уровня, чтобы обновить копию Базы данных SQL Azure.  
   
 3.  Остановите обновление версии SQL Server таблицы и удалите публикацию.  
   
## <a name="limitations"></a>Ограничения  
В подписках Базы данных SQL Azure не поддерживаются следующие действия:  
-   копирование сопоставления групп файлов;  
-   копирование схем секционирования таблиц;  
-   копирование схем секционирования индексов;  
-   копирование определяемой пользователем статистики;  
-   копирование привязок по умолчанию;  
-   копирование привязок правил;  
-   копирование полнотекстовых индексов;  
-   копирование XSD XML;  
-   копирование XML-индексов;  
-   копирование разрешений;  
-   копирование пространственных индексов;  
-   копирование фильтруемых индексов;  
-   копирование атрибута сжатия данных;  
-   копирование атрибута разреженного столбца;  
-   конвертирование файлового потока в типы данных MAX;  
-   копирование hierarchyid в типы данных MAX;  
-   копирование пространственных типов в типы данных MAX;  
-   копирование расширенных свойств;  
-   копирование разрешений.  
   
### <a name="limitations-to-be-determined"></a>Ограничения, которые должны быть подтверждены 
-   Копирование параметров сортировки.  
-   Выполнение в сериализованной транзакции хранимой процедуры.  
   
## <a name="examples"></a>Примеры  
Создайте публикацию и принудительную подписку. Дополнительные сведения можно найти в разделе   
-   [Создание публикации](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)  
   
-   [Создание принудительной подписки](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/create-a-push-subscription) с использованием имени логического сервера базы данных SQL Azure в качестве подписчика (например, **N'azuresqldbdns.database.windows.net'**) и имя Базы данных SQL Azure в качестве целевой базы данных (например, **AdventureWorks**).  
   
 ## <a name="see-also"></a>См. также  
 - [Создание публикации](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)   
 - [Создание принудительной подписки](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/create-a-push-subscription)   
 - [Типы репликации](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)   
 - [Мониторинг (репликация)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)   
 - [Инициализация подписки](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  
