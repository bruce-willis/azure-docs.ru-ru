---
title: Создание эластичных пулов и управление ими — база данных SQL Azure | Документы Майкрософт
description: Создание эластичных пулов в базе данных SQL Azure и управление ими.
services: sql-database
ms.service: sql-database
subservice: elastic-pool
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 6418694097c472afd6a2c706e55a9026ab03dcff
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162724"
---
# <a name="create-and-manage-elastic-pools-in-azure-sql-database"></a>Создание эластичных пулов в базе данных SQL Azure и управление ими

С эластичным пулом вы определяете количество ресурсов, которое необходимо эластичному пулу для обработки рабочей нагрузки баз данных, входящих в пул, и количество ресурсов для каждой базы данных в пуле. 

## <a name="azure-portal-manage-elastic-pools-and-pooled-databases"></a>Портал Azure: управление эластичными пулами и базами данных в пуле

Все параметры пула можно найти в колонке **Настройка пула**. Чтобы перейти к ней, найдите эластичный пул на портале и щелкните **Настроить пул** в верхней части колонки или в меню ресурсов слева.

Здесь можно внести любое сочетание следующих изменений и сохранить их массово:
1. Изменить уровень служб пула.
2. Увеличить или уменьшить уровень производительности (единиц DTU или виртуальных ядер) и размер хранилища.
3. Добавить базы данных в пул или удалить их.
4. Установить минимальное (гарантированное) и максимальное ограничения производительности для баз данных в пулах.
5. Просмотреть сводку затрат, чтобы узнать о любых изменениях в счете в результате выбора новых значений.

![Колонка настройки эластичного пула](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

## <a name="powershell-manage-elastic-pools-and-pooled-databases"></a>PowerShell: управление эластичными пулами и базами данных в пуле 

Для создания эластичных пулов и баз данных в пуле для базы данных SQL и управления ими с помощью Azure PowerShell используйте приведенные ниже командлеты PowerShell. Если вам нужно выполнить установку или обновление PowerShell, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Сведения о создании логических серверов для эластичного пула и управлении ими см. в [этой статье](sql-database-logical-servers.md). Сведения о создании правил брандмауэра и управлении ими с помощью PowerShell см. в [этой статье](sql-database-firewall-configure.md#manage-firewall-rules-using-azure-powershell).

> [!TIP]
> Образцы скриптов PowerShell см. в статьях [Создание эластичных пулов и перемещение баз данных между пулами и из пула с помощью PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) и [Отслеживание и масштабирование эластичного пула SQL в Базе данных SQL Azure с помощью PowerShell](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Командлет | ОПИСАНИЕ |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Создает пул эластичных баз данных на логическом сервере SQL.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Получает пулы эластичных баз данных и значения их свойств на логическом сервере SQL.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Изменяет свойства пула эластичных баз данных на логическом сервере SQL. Например, используйте свойство **StorageMB** для изменения максимального размера хранилища эластичного пула.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Удаляет пул эластичных баз данных на логическом сервере SQL.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Получает состояние операций в эластичном пуле на логическом сервере SQL.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Создает новую базу данных в существующем пуле или отдельную базу данных. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Получает одну или несколько баз данных.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Определяет свойства базы данных или перемещает ее в эластичный пул, из пула либо между пулами.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Удаляет базу данных.|


> [!TIP]
> Создание большого количества баз данных в эластичном пуле может занять некоторое время, если эта операция выполняется с помощью портала или командлетов PowerShell, которые создают базы данных поочередно. Сведения об автоматическом создании баз данных в эластичном пуле см. в документе [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="azure-cli-manage-elastic-pools-and-pooled-databases"></a>Azure CLI: управление эластичными пулами и базами данных в пуле

Для создания эластичных пулов базы данных SQL с помощью [Azure CLI](/cli/azure) используйте приведенные ниже команды [Azure CLI для базы данных SQL](/cli/azure/sql/db). Запускайте интерфейс командной строки в браузере с помощью [Cloud Shell](/azure/cloud-shell/overview) либо [установите](/cli/azure/install-azure-cli) его на платформе macOS, Linux или Windows.

> [!TIP]
> Примеры скриптов для Azure CLI см. в статьях [Перемещение базы данных Azure SQL в эластичном пуле SQL с помощью интерфейса командной строки](scripts/sql-database-move-database-between-pools-cli.md) и [Масштабирование эластичного пула SQL в Базе данных SQL Azure с помощью Azure CLI](scripts/sql-database-scale-pool-cli.md).
>

| Командлет | ОПИСАНИЕ |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Создает эластичный пул.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Возвращает список эластичных пулов на сервере.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Возвращает список баз данных в пуле эластичных баз данных.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Также содержит параметры доступных DTU пула, ограничений хранилища и параметры отдельных баз данных. Чтобы снизить уровень детализации, ограничения дополнительного хранилища и параметры каждой базы данных скрыты по умолчанию.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Обновляет эластичный пул.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Удаляет эластичный пул.|

## <a name="transact-sql-manage-pooled-databases"></a>Transact-SQL: управление базами данных в пуле

Для создания и перемещения баз данных в существующих эластичных пулах или для возвращения сведений о пуле эластичных баз данных SQL с помощью Transact-SQL используйте следующие команды T-SQL. Можно выполнить эти команды на портале Azure, в [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) или любой другой программе, которая может подключаться к серверу базы данных SQL Azure и передавать команды Transact-SQL. Сведения о создании правил брандмауэра и управлении ими с помощью Transact-SQL см. в [этой статье](sql-database-firewall-configure.md#manage-firewall-rules-using-transact-sql).

> [!IMPORTANT]
> С помощью Transact-SQL невозможно создать, обновить или удалить пул эластичных баз данных SQL Azure. Вы можете добавить или удалить базы данных из эластичного пула, а также вернуть сведения о существующих эластичных пулах, используя динамические административные представления.
>

| Get-Help | ОПИСАНИЕ |
| --- | --- |
|[CREATE DATABASE (база данных SQL Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Создает новую базу данных в существующем пуле или отдельную базу данных. Для создания базы данных требуется подключение к базе данных master.|
| [ALTER DATABASE (база данных SQL Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Перемещает базы данных из, в или между эластичными пулами.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Удаляет базу данных.|
|[sys.elastic_pool_resource_stats (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Возвращает статистику использования ресурсов для всех пулов эластичных баз данных на логическом сервере. Для каждого пула эластичной базы данных имеется одна строка на каждые 15 секунд окна отчета (четыре строки в минуту). Сюда входят сведения об использовании ЦП, хранилища, операциях ввода-вывода, журнал, а также использование параллельных запросов и сеансов всеми базами данных в пуле.|
|[sys.database_service_objectives (база данных SQL Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Возвращает сведения о выпуске (уровень служб), целевой службе (ценовую категорию), а также имя эластичного пула (при наличии) для базы данных SQL Azure или хранилища данных SQL Azure. В системе базы данных master на сервере базы данных SQL Azure возвращает сведения обо всех базах данных. Для использования хранилища данных SQL Azure необходимо подключиться к базе данных master.|

## <a name="rest-api-manage-elastic-pools-and-pooled-databases"></a>REST API: управление эластичными пулами и базами данных в пуле

Для создания эластичных пулов и базах данных в пуле для базы данных SQL и управления ими используйте эти запросы REST API.

| Get-Help | ОПИСАНИЕ |
| --- | --- |
|[Создание или обновление эластичных пулов](/rest/api/sql/elasticpools/createorupdate)|Создает эластичный пул или обновляет имеющийся.|
|[Удаление эластичных пулов](/rest/api/sql/elasticpools/delete)|Удаляет эластичный пул.|
|[Получение эластичных пулов](/rest/api/sql/elasticpools/get)|Получает эластичный пул.|
|[Список эластичных пулов на сервере](/rest/api/sql/elasticpools/listbyserver)|Возвращает список эластичных пулов на сервере.|
|[Обновление эластичных пулов](/rest/api/sql/elasticpools/update)|Обновляет имеющийся эластичный пул.|
|[Действия эластичного пула](/rest/api/sql/elasticpoolactivities)|Возвращает действия эластичного пула.|
|[Действия базы данных в эластичном пуле](/rest/api/sql/elasticpooldatabaseactivities)|Возвращает действия баз данных в эластичном пуле.|
|[Создание или обновление баз данных](/rest/api/sql/databases/createorupdate)|Создает новую базу данных или обновляет имеющуюся.|
|[Базы данных: получение](/rest/api/sql/databases/get)|Получает базу данных.|
|[Базы данных: вывод списка по эластичному пулу](/rest/api/sql/databases/listbyelasticpool)|Возвращает список баз данных в пуле эластичных баз данных.|
|[Базы данных: вывод списка по серверу](/rest/api/sql/databases/listbyserver)|Возвращает список баз данных на сервере.|
|[Базы данных: обновление](/rest/api/sql/databases/update)|Обновляет имеющуюся базу данных.|

## <a name="next-steps"></a>Дополнительная информация

* Мы рекомендуем просмотреть [видеокурс о возможностях эластичной базы данных SQL Azure на сайте Microsoft Virtual Academy](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554).
* Дополнительные сведения о шаблонах разработки для приложений SaaS, использующих пулы эластичных БД, см. в статье [Шаблоны разработки для мультитенантных приложений SaaS с использованием базы данных Azure SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* Дополнительные сведения об эластичных пулах см. в статье [Общие сведения о приложении SaaS Wingtip](sql-database-wtp-overview.md).
