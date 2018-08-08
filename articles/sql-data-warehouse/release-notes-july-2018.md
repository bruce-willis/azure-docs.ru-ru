---
title: 'Хранилище данных SQL Azure: заметки о выпуске за июнь 2018 г. | Документация Майкрософт'
description: Заметки о выпуске для хранилища данных SQL Azure.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: b410722ff444c19572f61996c4a4d059ae831f5f
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326087"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Что нового в Хранилище данных SQL Azure? Июль 2018 г.
Хранилище данных SQL Azure постоянно совершенствуется. В этой статье описаны новые возможности и изменения, вступившие в силу с июля 2018 г.

## <a name="lightning-fast-query-performance"></a>Высокая производительность запросов
[Хранилище данных SQL Azure](https://aka.ms/sqldw) задает новые показатели производительности благодаря введению мгновенного доступа к данным, который улучшает операции смешения. Мгновенный доступ к данным снижает затраты на операции перемещения данных за счет использования прямых собственных операций с данными между экземплярами SQL Server. Интеграция с ядром SQL Server непосредственно для перемещения данных означает, что хранилище данных SQL теперь на**67 % быстрее, чем Amazon Redshift**. Для измерения взята рабочая нагрузка, полученная из теста производительности [TPC Benchmark™ H (TPC-H)](http://www.tpc.org/tpch/), который является известным отраслевым стандартом.

![Хранилище данных SQL Azure — быстрее и дешевле, чем Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/eb3b908a-464d-4847-b384-9f296083a737.png)
<sub>Источник: [аналитический отчет Gigaom Research. Тест производительности хранилища данных в облаке](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

Помимо производительности среды выполнения в отчете [Gigaom Research](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) также содержится измерение соотношения цены и производительности для количественной оценки стоимости конкретных рабочих нагрузок в долларах США. Хранилище данных SQL было **не менее чем на 23 % дешевле**, чем Redshift для рабочих нагрузок в 30 ТБ. Благодаря способности Хранилища данных SQL Azure масштабировать вычислительные ресурсы, а также приостанавливать и возобновлять рабочие нагрузки, клиенты платят только в том случае, если служба используется, что еще больше снижает стоимость.
![Хранилище данных SQL Azure — быстрее и дешевле, чем Amazon Redshift](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/cb76447e-621e-414b-861e-732ffee5345a.png)
<sub>Источник: [аналитический отчет Gigaom Research. Тест производительности хранилища данных в облаке](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/)</sub>

###<a name="query-concurrency"></a>Параллелизм запросов
Хранилище данных SQL также гарантирует доступность данных в ваших организациях. Корпорация Майкрософт расширила службу для поддержки 128 одновременных запросов, чтобы больше пользователей могли запрашивать одну и ту же базу данных и не блокироваться другими запросами. Для сравнения в Amazon Redshift максимальное количество одновременных запросов составляет 50, что ограничивает доступ к данным в организации.

Хранилище данных SQL обеспечивает эти улучшения производительности и параллелизма запросов без увеличения цены и опирается на уникальную архитектуру с разделением хранилища и вычислительных ресурсов.

## <a name="finer-granularity-for-cross-region-and-server-restores"></a>Более детализированные данные для межобластного и межсерверного восстановления
Теперь восстановление в разных областях и на разных серверах возможно с помощью любой точки восстановления. Нет необходимости выбирать геоизбыточные резервные копии, которые создаются каждые 24 часа. Межобластное и межсерверное восстановление поддерживается для точек восстановления, определяемых как пользователем, так и автоматически, что обеспечивает большую степень детализации для дополнительной защиты данных. Имея несколько доступных точек восстановления, можно быть уверенным, что при восстановлении в разных регионах хранилище данных будет логически согласованным.

![Команда Restore — Хранилище данных SQL Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![Параметры восстановления — Хранилище данных SQL Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

Дополнительные сведения см. в записи блога [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Ускоренные и гибкие точки восстановления).

## <a name="20-minute-restorations"></a>20-минутное восстановление
Хранилище данных SQL теперь предоставляет возможность восстановления любого хранилища данных **менее чем за 20 минут** в том же регионе, независимо от размера базы данных. Время восстановления не меняется, независимо от того, производится восстановление на том же или другом логическом сервере в одной области. Кроме того, был улучшен процесс создания моментального снимка, чтобы сократить время, необходимое для создания точки восстановления. На нижнем уровне производительности (нижние параметры DWU) время создания моментального снимка *сократилось в 2 раза*.

Дополнительные сведения см. в записи блога [Accelerated and Flexible Restore Points](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/) (Ускоренные и гибкие точки восстановления).

## <a name="custom-restoration-configurations"></a>Пользовательские настройки восстановления
Теперь уровень производительности (DWU) можно изменить при восстановлении на портале Azure. Восстановление также можно проводить в обновленном хранилище данных 2-го поколения. При восстановлении на экземпляре 2-го поколения можно оценить влияние 2-го поколения перед [обновлением хранилища данных 1-го поколения](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation).

![Пользовательские настройки восстановления — Хранилище данных SQL Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
Хранимая процедура [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) часто используется средствами для получения метаданных о параметрах в пакете Transact-SQL. Процедура возвращает одну строку для каждого параметра в пакете с выведенным типом информации для этого параметра. 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

Результирующий набор включает в себя метаданные о параметре `@id` (показаны частичные результаты).
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```
## <a name="sprefreshsqlmodule"></a>SP_REFRESHSQLMODULE
Хранимая процедура [Sp_refreshsqlmodule](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-refreshsqlmodule-transact-sql) обновляет метаданные для объекта базы данных, если базовые метаданные устарели из-за изменения базовых объектов. Это может произойти, если базовые таблицы для представления изменены, а представление еще не создано заново. Это избавит вас от необходимости удаления и повторного создания зависимых объектов.

В приведенном ниже примере показано представление, которое является устаревшим из-за изменения базовой таблицы. Обратите внимание, что первый измененный столбец (от 1 до Mollie) имеет правильные данные, но недопустимое имя, а второй столбец отсутствует. 
```sql
CREATE TABLE base_table (Id INT);
GO

INSERT INTO base_table (Id) VALUES (1);
GO

CREATE VIEW base_view AS SELECT * FROM base_table;
GO

SELECT * FROM base_view;
GO

-- Id
-- ----
-- 1

DROP TABLE base_table;
GO

CREATE TABLE base_table (fname VARCHAR(10), lname VARCHAR(10));
GO

INSERT INTO base_table (fname, lname) VALUES ('Mollie', 'Gallegos');
GO

SELECT * FROM base_view;
GO

-- Id
-- ----------
-- Mollie

EXEC sp_refreshsqlmodule @Name = 'base_view';
GO

SELECT * FROM base_view;
GO

-- fname     | lname
-- ---------- ----------
-- Mollie    | Gallegos
```

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вам уже известны некоторые сведения о хранилище данных SQL, узнайте о том, как [его создать][create a SQL Data Warehouse]. Если вы раньше не работали с Azure, используйте [глоссарий Azure][Azure glossary], чтобы узнать значение новых терминов. Или ознакомьтесь со следующими ресурсами, посвященными хранилищу данных SQL.  

* [Истории успеха клиентов]
* [Блоги]
* [Запросы функций]
* [Видеоролики]
* [Блоги группы консультирования клиентов]
* [Форум Stack Overflow]
* [Twitter]


[Блоги]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Блоги группы консультирования клиентов]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Истории успеха клиентов]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Запросы функций]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Форум Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Видеоролики]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md