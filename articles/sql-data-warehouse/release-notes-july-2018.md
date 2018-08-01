---
title: 'Хранилище данных SQL Azure: заметки о выпуске за июнь 2018 г. | Документация Майкрософт'
description: Заметки о выпуске для хранилища данных SQL Azure.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216740"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Что нового в Хранилище данных SQL Azure? Июль 2018 г.
Хранилище данных SQL Azure постоянно совершенствуется. В этой статье описаны новые возможности и изменения, вступившие в силу с июля 2018 г.


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