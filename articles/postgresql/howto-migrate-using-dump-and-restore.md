---
title: Как создать дамп и выполнить восстановление в службе "База данных Azure для PostgreSQL"
description: Здесь объясняется, как извлечь базу данных PostgreSQL в файл дампа и восстановить ее из файла архива, созданного pg_dump в службе "База данных Azure для PostgreSQL".
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 586df8d72dc05104bbf589eabcf3bd2245c268c8
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737254"
---
# <a name="migrate-your-postgresql-database-using-dump-and-restore"></a>Перенос базы данных PostgreSQL с помощью дампа и ее восстановление
Можно извлечь базу данных PostgreSQL в файл дампа с помощью [pg_dump](https://www.postgresql.org/docs/9.3/static/app-pgdump.html) и с помощью [pg_restore](https://www.postgresql.org/docs/9.3/static/app-pgrestore.html) восстановить базу данных PostgreSQL из файла архива, созданного pg_dump.

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-portal.md) с правилами брандмауэра, разрешающими доступ к этом серверу и его базам данных;
- установленные программы командной строки [pg_dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html) и [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

Выполните указанные ниже действия, чтобы создать дамп базы данных PostgreSQL и восстановить ее.

## <a name="create-a-dump-file-using-pgdump-that-contains-the-data-to-be-loaded"></a>Создание файла дампа, содержащего загружаемые данные, с помощью pg_dump
Чтобы создать резервную копию базы данных PostgreSQL локально или на виртуальной машине, выполните следующую команду:
```bash
pg_dump -Fc -v --host=<host> --username=<name> --dbname=<database name> > <database>.dump
```
Например, если имеется локальный сервер с базой данных **testdb**.
```bash
pg_dump -Fc -v --host=localhost --username=masterlogin --dbname=testdb > testdb.dump
```

> [!IMPORTANT]
> Скопируйте файлы резервной копии в большой двоичный объект Azure или хранилище Azure и выполните восстановление из них, что должно быть намного быстрее, чем восстановление через Интернет.
> 

## <a name="restore-the-data-into-the-target-azure-database-for-postrgesql-using-pgrestore"></a>Восстановление данных в целевую базу данных Azure для PostrgeSQL с помощью pg_restore
После создания целевой базы данных можно воспользоваться командой pg_restore с параметром -d, --dbname, чтобы восстановить данные в целевую базу данных из файла дампа.
```bash
pg_restore -v --no-owner –-host=<server name> --port=<port> --username=<user@servername> --dbname=<target database name> <database>.dump
```
Если включить параметр --no-owner, все объекты, созданные во время восстановления, будут присвоены пользователю --username. Дополнительные сведения см. в официальной документации PostgreSQL по [pg_restore](https://www.postgresql.org/docs/9.6/static/app-pgrestore.html).

В этом примере восстановите данные из файла дампа **testdb.dump** в базу данных **mypgsqldb** на целевом сервере **mydemoserver.postgres.database.azure.com**. 
```bash
pg_restore -v --no-owner --host=mydemoserver.postgres.database.azure.com --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb testdb.dump
```

## <a name="next-steps"></a>Дополнительная информация
- Перенос базы данных PostgreSQL с помощью метода экспорта и импорта см. в [этой статье](howto-migrate-using-export-and-import.md).
- Дополнительные сведения о переносе баз данных в службу "База данных Azure для PostgreSQL" см. в [этой статье](http://aka.ms/datamigration).
