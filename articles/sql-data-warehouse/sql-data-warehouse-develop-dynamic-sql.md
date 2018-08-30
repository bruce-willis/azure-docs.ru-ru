---
title: Применение динамического SQL в хранилище данных SQL Azure | Документация Майкрософт
description: Рекомендации по использованию динамического SQL в хранилище данных SQL для разработки решений.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 6793fba1476595918ac20c0484a661e3af7897d7
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247306"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Динамический SQL в хранилище данных SQL
Рекомендации по использованию динамического SQL в хранилище данных SQL для разработки решений.

## <a name="dynamic-sql-example"></a>Пример динамического SQL

При разработке кода приложения для хранилища данных SQL может возникнуть потребность в динамическом SQL для создания универсальных и гибких модульных решений. На данный момент хранилище данных SQL не поддерживает двоичные типы данных. Это может ограничивать размер строк, так как к двоичным относятся типы данных nvarchar(max) и varchar(max). Если вы использовали эти типы в коде приложения при создании очень больших строк, необходимо будет разбить код на фрагменты и вместо этого использовать инструкцию EXEC.

Вот простой пример:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Если строка короткая, то можно использовать [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) как обычно.

> [!NOTE]
> К инструкциям, выполняемым как динамический код SQL, по-прежнему будут применяться все правила проверки TSQL.
> 
> 

## <a name="next-steps"></a>Дополнительная информация
Дополнительные советы по разработке приведены в [обзоре разработки](sql-data-warehouse-overview-develop.md).

