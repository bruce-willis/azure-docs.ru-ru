---
title: Управление файловым пространством Базы данных SQL Azure | Документация Майкрософт
description: Здесь описывается управление файловым пространством в Базе данных SQL Azure и приведены примеры кода для определения необходимости сжатия базы данных и выполнения операции сжатия.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: how-to
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: 1ecc0ce08ef42f5f5935bca29e8269be2ea142f0
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39416007"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Управление файловым пространством в Базе данных SQL Azure

В этой статье описываются различные типы дискового пространства в Базе данных SQL Azure, а также действия, которые нужно предпринять, если файловым пространством, выделенным для баз данных и эластичных пулов, должен управлять клиент.

## <a name="overview"></a>Обзор

В Базе данных SQL Azure на портале Azure отображаются метрики размера хранилища, и следующие API измеряют число используемых страниц данных для баз данных и эластичных пулов:
- API метрик на основе Azure Resource Manager, включая PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric).
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database).
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database).

Существуют шаблоны рабочей нагрузки, в которых выделенное пространство в базовых файлах данных для баз данных становится больше, чем число использованных страниц данных в файлах данных. Этот сценарий может произойти, когда используемое пространство увеличивается, а данные впоследствии удаляются. При удалении данных выделенное файловое пространство автоматически не освобождается. В таких сценариях выделенное файловое пространство для базы данных или пула может превысить максимальные ограничения, установленные (или поддерживаемые) для базы данных. Это может предотвратить рост объема данных или изменение уровня производительности, несмотря на то что фактически используемое пространство базы данных меньше, чем максимальное ограничение. Для устранения этого необходимо сжать базу данных, чтобы уменьшить выделенное, но неиспользуемое в ней пространство.

Служба Базы данных SQL не сможет автоматически сжать файлы базы данных для освобождения неиспользуемого выделенного пространства из-за возможного влияния на производительность базы данных. Тем не менее, вы можете сжать файл в базе данных в любое время, выполнив действия, описанные в разделе [Освобождение неиспользуемого выделенного пространства](#reclaim-unused-allocated-space). 

> [!NOTE]
> В отличие от файлов данных, служба Базы данных SQL автоматически сжимает файлы журналов, так как эта операция не влияет на производительность базы данных.

## <a name="understanding-the-types-of-storage-space-for-a-database"></a>Основные сведения о типах дискового пространства для базы данных

Чтобы управлять файловым пространством, необходимо понимать следующие термины, относящиеся к хранилищу как одиночной базы данных, так и эластичного пула.

|Термин дискового пространства|Определение|Комментарии|
|---|---|---|
|**Место, занятое данными**|Объем пространства, используемого для хранения данных базы данных, в страницах по 8 КБ.|Как правило, это используемое пространство увеличивается (уменьшается) при операциях вставки (удаления). В некоторых случаях используемое пространство остается неизменным при операциях вставки или удаления в зависимости от объема и шаблона данных, участвующих в операции и фрагментации. Например, удаление одной строки на каждой странице данных не обязательно приведет к уменьшению используемого пространства.|
|**Выделенное пространство**|Объем форматированного файлового пространства, который стал доступным для хранения данных базы данных.|Выделенное пространство увеличивается автоматически, но никогда не уменьшается после удалений. Такое поведение гарантирует, что будущие вставки выполняются быстрее, так как пространство не нуждается в переформатировании.|
|**Выделенное, но неиспользуемое пространство**|Объем неиспользуемого файлового пространства, выделенного для базы данных.|Это разница между объемом выделенного пространства и используемого пространства, которая представляет максимальный объем пространства, который можно освободить путем сжатия файлов базы данных.|
|**Максимальный размер**|Максимальный объем пространства данных, который база данных может использовать.|Выделенное пространство данных не может превышать максимальный размер данных.|
||||

На следующей схеме показана связь между типами дискового пространства.

![типы дискового пространства и их связи](./media/sql-database-file-space-management/storage-types.png)

## <a name="query-a-database-for-storage-space-information"></a>Запрос базы данных для получения сведений о дисковом пространстве

Чтобы определить, имеете ли вы выделенное, но неиспользуемое пространство для отдельной базы данных, которое вы, возможно, захотите освободить, используйте следующие запросы.

### <a name="database-data-space-used"></a>Место, занятое данными базы данных
Измените следующий запрос, чтобы получить объем места, занятого данными базы данных, в МБ.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-allocated-and-allocated-space-unused"></a>Выделенное пространство для данных базы данных и неиспользуемое выделенное пространство
Измените следующий запрос, чтобы получить объем выделенного пространства для данных базы данных и неиспользуемого выделенного пространства.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-max-size"></a>Максимальный размер базы данных
Измените следующий запрос, чтобы получить максимальный размер базы данных в байтах.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Запрос эластичного пула для получения сведений о дисковом пространстве

Чтобы определить, имеете ли вы выделенное, но неиспользуемое пространство для данных в эластичном пуле и в каждой базе данных в составе пула, которое вы, возможно, захотите освободить, используйте следующие запросы:

### <a name="elastic-pool-data-space-used"></a>Используемое пространство данных эластичного пула
Измените следующий запрос, чтобы получить объем пространства, занятого данными эластичного пула, в МБ.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-allocated-and-allocated-space-unused"></a>Выделенное пространство для данных эластичного пула и неиспользуемое выделенное пространство

Измените следующий скрипт PowerShell, чтобы получить таблицу с общим выделенным пространством и неиспользуемым выделенным пространством для каждой базы данных в эластичном пуле. Таблица сортирует базы данных по объему неиспользованного выделенного пространства от наибольших к наименьшим.  

Результаты запроса для определения выделенного пространства для каждой базы данных в пуле могут суммироваться для представления общего выделенного пространства эластичного пула. Выделенное пространство эластичного пула не должно превышать максимальный размер эластичного пула.  

```powershell
# Resource group name
$resourceGroupName = "rg1" 
# Server name
$serverName = "ls2" 
# Elastic pool name
$poolName = "ep1"
# User name for server
$userName = "name"
# Password for server
$password = "password"

# Get list of databases in elastic pool
$databasesInPool = Get-AzureRmSqlElasticPoolDatabase `
    -ResourceGroupName $resourceGroupName `
    -ServerName $serverName `
    -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# For each database in the elastic pool,
# get its space allocated in MB and space allocated unused in MB.
# Requires SQL Server PowerShell module – see here to install.  
foreach ($database in $databasesInPool)
{
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn `
        -Database $database.DatabaseName `
        -Username $userName `
        -Password $password `
        -Query $sqlCommand)
}
# Display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort `
    -Property DatabaseDataSpaceAllocatedUnusedInMB `
    -Descending | Format-Table
```

В качестве примера выходных данных скрипта приведен следующий снимок экрана:

![пример выделенного и неиспользуемого пространства эластичного пула](./media/sql-database-file-space-management/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-max-size"></a>Максимальный размер эластичного пула

Используйте следующий запрос T-SQL, чтобы получить максимальный размер эластичной базы данных в МБ.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Освобождение неиспользуемого выделенного пространства

Когда вы определите, что у вас есть неиспользуемое выделенное пространство, которое нужно освободить, выполните следующую команду для сжатия выделенного пространства базы данных. 

> [!IMPORTANT]
> В эластичном пуле базы данных с наибольшим неиспользуемым выделенным пространством нужно сжать в первую очередь, чтобы быстро освободить файловое пространство.  

Чтобы сжать все файлы данных в указанной базе данных, используйте следующую команду:

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'<database_name>')
```

Дополнительные сведения об этой команде см. в статье [DBCC SHRINKDATABASE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

> [!IMPORTANT] 
> Рассмотрите возможность перестроения индексов базы данных. После сжатия файлов базы данных индексы могут стать фрагментированными и потерять оптимизацию производительности. В этом случае следует перестроить индексы. Дополнительные сведения о фрагментации и перестроении индексов см. в статье [Реорганизация и перестроение индексов](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Дополнительная информация

- Сведения о максимальных размерах базы данных см. в статьях:
  - [Ограничения ресурсов для отдельной базы данных в Базе данных SQL Azure при использовании модели приобретения на основе виртуальных ядер](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases)
  - [Ограничения ресурсов для одиночных баз данных в модели приобретения на основе DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-single-databases)
  - [Ограничения для эластичных пулов в службе "База данных SQL Azure" в модели приобретения на основе виртуальных ядер](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools)
  - [Ограничения ресурсов для эластичных пулов в модели приобретения на основе DTU](https://docs.microsoft.com/azure/sql-database/sql-database-dtu-resource-limits-elastic-pools)
- Дополнительные сведения о команде `SHRINKDATABASE` см. в статье [DBCC SHRINKDATABASE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql). 
- Дополнительные сведения о фрагментации и перестроении индексов см. в статье [Реорганизация и перестроение индексов](https://docs.microsoft.com/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).