---
title: Управление файловым пространством Базы данных SQL Azure | Документация Майкрософт
description: Здесь описывается управление файловым пространством в Базе данных SQL Azure и приведены примеры кода для определения необходимости сжатия базы данных и выполнения операции сжатия.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: a46192c79d32ddf5f178541c3be128893e8f6109
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159947"
---
# <a name="manage-file-space-in-azure-sql-database"></a>Управление файловым пространством в Базе данных SQL Azure
В этой статье описываются различные типы дискового пространства в Базе данных SQL Azure, а также действия, которые нужно предпринять, если управление файловым пространством, выделенным для баз данных и эластичных пулов, должно осуществляться явным образом.

## <a name="overview"></a>Обзор

В Базе данных SQL Azure на портале Azure отображается большинство метрик дискового пространства, и следующие API измеряют число используемых страниц данных для баз данных и эластичных пулов:
- API метрик на основе Azure Resource Manager, включая PowerShell [get-metrics](https://docs.microsoft.com/powershell/module/azurerm.insights/get-azurermmetric).
- T-SQL: [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database).
- T-SQL: [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database).
- T-SQL: [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database).

Существуют шаблоны рабочей нагрузки, в которых распределение базовых файлов данных для баз данных может превысить объем используемых страниц данных.  Это может произойти, когда используемое пространство увеличивается, а данные затем удаляются.  Это объясняется тем, что выделенное файловое пространство автоматически не освобождается.  В таких случаях пространство, выделенное для базы данных или пула, может превышать поддерживаемые ограничения, предотвращать увеличение объема данных или изменение уровня служб и объема вычислительных ресурсов, а также требовать сжатия файлов базы данных для устранения проблемы.

Служба базы данных SQL не сможет автоматически сжать файлы данных для освобождения неиспользуемого выделенного пространства из-за возможного влияния на производительность базы данных.  Тем не менее клиенты могут сжать файлы данных самостоятельно в любое время, выполнив действия, описанные в разделе [Освобождение неиспользуемого выделенного пространства](#reclaim-unused-allocated-space). 

> [!NOTE]
> В отличие от файлов данных, служба Базы данных SQL автоматически сжимает файлы журналов, так как эта операция не влияет на производительность базы данных. 

## <a name="understanding-types-of-storage-space-for-a-database"></a>Основные сведения о типах дискового пространства для базы данных

Для управления файловым пространством базы данных важно разобраться со следующими объемами дискового пространства.

|Объем пространства базы данных|Определение|Комментарии|
|---|---|---|
|**Место, занятое данными**|Объем пространства, используемого для хранения данных базы данных, в страницах по 8 КБ.|Как правило, используемое пространство увеличивается (уменьшается) при операциях вставки (удаления). В некоторых случаях используемое пространство остается неизменным при операциях вставки или удаления в зависимости от объема и шаблона данных, участвующих в операции и фрагментации. Например, удаление одной строки на каждой странице данных не обязательно приведет к уменьшению используемого пространства.|
|**Выделенное пространство данных**|Объем форматированного файлового пространства, который стал доступным для хранения данных базы данных.|Объем выделенного пространства увеличивается автоматически, но никогда не уменьшается после удалений. Такое поведение гарантирует, что будущие вставки выполняются быстрее, так как пространство не нуждается в переформатировании.|
|**Выделенное, но неиспользуемое пространство данных**|Разница между объемом выделенного и используемого пространства данных.|Это количество представляет максимальный объем свободного пространства, которое можно освободить путем сжатия файлов данных базы данных.|
|**Максимальный размер данных**|Максимальный объем пространства, который можно использовать для хранения данных базы данных.|Объем выделенного пространства данных не может превышать максимальный размер данных.|
||||

На следующей схеме показана связь между разными типами дискового пространства для базы данных.

![типы дискового пространства и их связи](./media/sql-database-file-space-management/storage-types.png) 

## <a name="query-a-database-for-storage-space-information"></a>Запрос базы данных для получения сведений о дисковом пространстве

Следующие запросы можно использовать для определения объема дискового пространства для базы данных.  

### <a name="database-data-space-used"></a>Место, занятое данными базы данных
Измените следующий запрос, чтобы получить объем места, занятого данными базы данных.  Единицы результатов запроса указываются в МБ.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Выделенное пространство данных базы данных и неиспользуемое выделенное пространство
Используйте следующий запрос, чтобы получить объем выделенного пространства данных базы данных и неиспользуемого выделенного пространства.  Единицы результатов запроса указываются в МБ.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, 
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB 
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS'
```
 
### <a name="database-data-max-size"></a>Максимальный размер данных базы данных
Измените следующий запрос, чтобы получить максимальный размер данных базы данных.  Единицы результатов запроса указываются в байтах.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>Основные сведения о типах дискового пространства для эластичного пула

Для управления файловым пространством эластичного пула важно разобраться со следующими объемами дискового пространства.

|Объем пространства эластичных пулов|Определение|Комментарии|
|---|---|---|
|**Место, занятое данными**|Общий объем пространства данных, используемого всеми базами данных в эластичном пуле.||
|**Выделенное пространство данных**|Общий объем пространства данных, выделенного всеми базами данных в эластичном пуле.||
|**Выделенное, но неиспользуемое пространство данных**|Разница между объемом выделенного пространства данных и пространства данных, используемого всеми базами данных в эластичном пуле.|Это количество представляет максимальный объем выделенного для эластичного пула пространства, которое можно освободить путем сжатия файлов данных базы данных.|
|**Максимальный размер данных**|Максимальный объем пространства данных, который эластичный пул может использовать для всех своих баз данных.|Выделенное для эластичного пула пространство не должно превышать максимальный размер эластичного пула.  В этом случае выделенное неиспользуемое пространство можно освободить путем сжатия файлов данных базы данных.|
||||

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Запрос эластичного пула для получения сведений о дисковом пространстве

Следующие запросы можно использовать для определения объема дискового пространства для эластичного пула.  

### <a name="elastic-pool-data-space-used"></a>Используемое пространство данных эластичного пула
Измените следующий запрос, чтобы получить объем пространства, занятого данными эластичного пула.  Единицы результатов запроса указываются в МБ.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>Выделенное пространство данных эластичного пула и неиспользуемое выделенное пространство

Измените следующий скрипт PowerShell, чтобы получить таблицу с выделенным пространством и неиспользуемым выделенным пространством для каждой базы данных в эластичном пуле. Таблица сортирует базы данных по объему неиспользованного выделенного пространства от наибольших к наименьшим.  Единицы результатов запроса указываются в МБ.  

Результаты запроса для определения выделенного пространства для каждой базы данных в пуле могут суммироваться для представления общего пространства, выделенного для эластичного пула. Выделенное пространство эластичного пула не должно превышать максимальный размер эластичного пула.  

Сценарию PowerShell требуется модуль SQL Server PowerShell. Дополнительные сведения см. в разделе [Установка модуля SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/download-sql-server-ps-module?view=sql-server-2017).

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

### <a name="elastic-pool-data-max-size"></a>Максимальный размер данных эластичного пула

Измените следующий запрос T-SQL, чтобы получить максимальный размер данных эластичного пула.  Единицы результатов запроса указываются в МБ.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC
```

## <a name="reclaim-unused-allocated-space"></a>Освобождение неиспользуемого выделенного пространства

Определив, в каких базах данных необходимо освободить неиспользуемое выделенное пространство, измените следующую команду, чтобы сжать файлы данных для каждой базы данных.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1')
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
