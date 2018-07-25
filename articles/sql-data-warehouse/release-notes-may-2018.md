---
title: Хранилище данных SQL Azure, заметки о выпуске за май 2018 г. | Документация Майкрософт
description: Заметки о выпуске для хранилища данных SQL Azure.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/28/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: ddcb985c370baf224d451aa6c1ec9b796e0140de
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38561410"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>Новые возможности Хранилища данных SQL Azure Май 2018 г. 
Хранилище данных SQL Azure постоянно совершенствуется. В этой статье описаны новые возможности и изменения, вступившие в силу с мая 2018 года. 

## <a name="gen-2-instances"></a>Экземпляры 2-го поколения
![ALT](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) Уровень хранилища данных SQL Azure 2-го поколения, оптимизированный для вычислений, задает новый стандарт производительности для облачных хранилищ данных. По сравнению с текущим поколением, клиенты получат повышение производительности в пять раз, улучшение параллелизма в четыре раза и рост вычислительной мощности в пять раз. Теперь для одного кластера поддерживается до 128 параллельных запросов, что превосходит возможности любого другого облачного хранилища данных.

Рохан Кумар (Rohan Kumar), корпоративный вице-президент службы данных Azure, посвятил этому обновлению запись [Turbocharge cloud analytics with Azure SQL Data Warehouse](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) (Ускорение облачной службы аналитики благодаря Хранилищу данных SQL Microsoft Azure) в своем блоге.

## <a name="auto-statistics"></a>Автоматическая статистика
Статистика критически важна для оптимизации при создании плана запроса в современных оптимизаторах на основе стоимости, таких как подсистема хранилища данных SQL. Если все запросы известны заранее, вполне возможно определить необходимые объекты статистики. Но если система постоянно сталкивается с новыми и произвольными запросами, что вполне типично для рабочих нагрузок хранилища данных, системным администраторам нелегко спрогнозировать необходимые объекты статистики. Это приводит к неоптимальным планам выполнения запросов и увеличению времени реакции на запросы. Для смягчения такой проблемы можно, среди прочего, заранее создать статистические объекты для всех столбцов в таблицах. Но этот процесс влечет за собой дополнительную нагрузку, так как поддержка объектов статистики при загрузке таблиц приводит к увеличению времени этой загрузки.

Хранилище данных SQL теперь поддерживает автоматическое создание объектов статистики, что повышает гибкость, производительность и упрощает работу для системных администраторов и разработчиков, сохраняя при этом высокое качество планов выполнения и оптимальное время отклика.

Чтобы включить или отключить автоматическое создание статистики в хранилище данных SQL, выполните следующую инструкцию:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Мы настоятельно рекомендуем установить для параметра `AUTO_CREATE_STATISTICS` значение `ON`.

> [!NOTE]
> Автоматическое создание статистики *включено по умолчанию* для всех новых хранилищ данных.
>  

Дополнительные сведения см. в статье [с описанием параметров ALTER DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options).

## <a name="rejected-row-support"></a>Поддержка отклоненных строк
Клиенты часто используют [PolyBase (внешние таблицы)](design-elt-data-loading.md) для загрузки данных в хранилище данных SQL Azure, так как они предоставляют высокую производительность и параллелизацию для загрузки данных. Модель PolyBase также используется по умолчанию при загрузке данных через [Фабрику данных Azure](http://azure.com/adf). 

Хранилище данных SQL добавляет возможность определить расположение для отклоненных строк с использованием параметра `REJECTED_ROW_LOCATION` в инструкции [CREATE EXTERNAL TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql). После выполнения [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) из внешней таблицы все строки, которые невозможно загрузить, будут сохраняться для дополнительного изучения в файл, расположенный рядом с источником данных. 

В записи блога [Load confidently with SQL Data Warehouse PolyBase Rejected Row Location](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) (Уверенная загрузка с указанием расположения для отклоненных строк PolyBase в хранилище данных SQL) этот механизм описан подробнее.

В следующем примере показано использование нового синтаксиса для отклоненных строк.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTER VIEW
Инструкция [ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) позволяет изменить ранее созданное представление без необходимости удалять, создавать заново и применять разрешения. 

В следующем примере изменяется ранее созданное представление.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
Функция [CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) возвращает строку, содержащую два или несколько тесно объединенных значений. Функция разделяет объединенные значения разделителем, указанным в первом аргументе. Функция `CONCAT_WS` очень удобна для создания выходных данных в формате значений, разделенных запятыми (CSV).

В следующем примере объединяется набор значений типа int, разделенных запятыми.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
Эта инструкция возвращает следующие результаты:
```
result
---------
1,2,3
```
В следующем примере объединяется набор значений смешанных типов с использованием разделителей-запятыми.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
Эта инструкция возвращает следующие результаты:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
Системная хранимая процедура [Sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) возвращает сведения о типах данных, поддерживаемых текущей средой. Как правило, она используется средствами, подключаемыми через ODBC, для изучения типов данных.

В следующем примере извлекаются сведения о всех типах данных, поддерживаемых хранилищем данных SQL.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Изменение в поведении запросов SELECT INTO с предложением ORDER BY
Хранилище данных SQL теперь блокирует все запросы `SELECT INTO`, содержащие предложение `ORDER BY`. Ранее такая операция считалась допустимой. Для ее выполнения данные создавались в памяти и затем помещались в целевую таблицу с изменением порядка в соответствии с формой таблицы.

### <a name="previous-behavior"></a>Прежнее поведение
Следующая инструкция будет выполнена успешно, но с дополнительными затратами на обработку.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Текущее поведение
Следующая инструкция вызывает ошибку с сообщением о том, что предложение `ORDER BY` в инструкции `SELECT INTO` не поддерживается.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
Инструкция возвращает такую ошибку:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>Состояние запроса SET PARSEONLY ON (изменение в поведении)
Используя синтаксис `SET PARSEONLY ON`, пользователь может потребовать, чтобы подсистема хранилища данных SQL проверяла синтаксис каждой инструкции T-SQL и возвращало сообщения об ошибках, не компилируя и не выполняя такие инструкции. Ранее в системном представлении [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) для таких инструкций сохранялось состояние `Running`. Теперь представление `sys.dm_pdw_exec_requests` возвращает правильное состояние `Complete`.