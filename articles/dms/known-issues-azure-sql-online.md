---
title: Статья об известных проблемах и ограничениях при сетевых миграциях в Базу данных SQL Azure | Документация Майкрософт
description: Узнайте об известных проблемах и ограничениях при сетевых миграциях в Базу данных SQL Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/24/2018
ms.openlocfilehash: 1f8e3ede4140ab5346285f7c247864f8ef8e2d48
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889160"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Известные проблемы и ограничения при сетевых миграциях в Базу данных SQL Azure

В следующих разделах описываются известные проблемы и ограничения, связанные с сетевыми миграциями из SQL Server в Базу данных SQL Azure.

- Перемещение темпоральных таблиц не поддерживается

    **Симптом**

    Если база данных-источник состоит из одной или нескольких темпоральных таблиц, перемещение базы данных завершится сбоем во время операции полной загрузки данных и вы увидите следующее сообщение:

    { "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error" (Ошибка перемещения базы данных), "errorEvents":"["Capture functionalities could not be set (Не удалось установить функциональные возможности отслеживания). RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 (Использование репликации не поддерживается в темпоральной таблице с системным управлением версиями: [Application.Cities]. Строка: 1. Столбец: 1)"]" }
 
   ![Примеры ошибок темпоральной таблицы](media\known-issues-azure-sql-online\dms-temporal-tables-errors.png)

   **Возможное решение**

   1. Найдите темпоральные таблицы в схеме источника, используя приведенный ниже запрос.
        ``` 
       select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
        ```
   2. Исключите эти таблицы из колонки **Настройка параметров миграции**, где вы указываете таблицы для перемещения.
   3. Перезапустите действие миграции.

    **Ресурсы**

    Дополнительные сведения см. в статье [Темпоральные таблицы](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
- При перемещении таблиц включается один или несколько столбцов с типом данных hierarchyid

    **Симптом**

    Вы можете увидеть исключение SQL, сообщающее, что ntext несовместим с hierarchyid во время операции полной загрузки данных:
     
    ![Пример ошибок hierarchyid](media\known-issues-azure-sql-online\dms-hierarchyid-errors.png)

    **Возможное решение**

    1. Найдите таблицы пользователя, включающие столбцы с типом данных hierarchyid, используя приведенный ниже запрос.

        ``` 
        select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
        ``` 

    2.  Исключите эти таблицы из колонки **Настройка параметров миграции**, где вы указываете таблицы для перемещения.
    3.  Перезапустите действие миграции.

- Сбои миграции с различными нарушениями целостности при активных триггерах в схеме во время операции полной загрузки данных или добавочной синхронизации данных

    **Возможное решение**
    1. В базе данных-источнике найдите триггеры, которые сейчас активны, используя приведенный ниже запрос:
        ```
        select * from sys.triggers where is_disabled =0
        ```
    2.  Отключите триггеры в вашей базе данных-источнике, выполнив действия, описанные в статье [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).
    3.  Перезапустите действие миграции.

- Поддержка типов данных LOB

    **Симптом**

    Данные могут быть усечены в целевом объекте, если длина столбца больших объектов (LOB) превышает 32 КБ. Вы можете проверить длину столбца LOB с помощью приведенного ниже запроса: 

    ``` 
    SELECT max(len(ColumnName)) as LEN from TableName
    ```

    **Возможное решение**

    Если у вас есть объект LOB, размер которого превышает 32 КБ, обратитесь в техническую службу по адресу [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com).

- Проблемы со столбцами меток времени

    **Симптом**

    DMS не переносит значение метки времени источника. Вместо этого в целевой таблице создается новая метка времени.

    **Возможное решение**

    Если нужно, чтобы служба DMS перенесла точное значение метки времени, хранящееся в исходной таблице, обратитесь в техническую службу [dmsfeedback@microsoft.com](mailto:dmsfeedback@microsoft.com).

- Ошибки перемещения данных не предоставляют дополнительные сведения в колонке подробностей о состоянии базы данных.

    **Симптом**

    Если в случае возникновения сбоев миграции в представлении сведений о состоянии базы данных щелкнуть ссылку **Ошибки миграции данных** в верхней ленте, могут не отобразиться дополнительные сведения о конкретных сбоях миграции.

     ![пример ошибок перемещения данных: нет сведений](media\known-issues-azure-sql-online\dms-data-migration-errors-no-details.png)

    **Возможное решение**

    Чтобы получить подробные сведения о конкретных сбоях, выполните следующие действия.

    1.  Закройте колонку подробностей о состоянии базы данных, чтобы отобразить экран действия миграции.

     ![экран действия миграции](media\known-issues-azure-sql-online\dms-migration-activity-screen.png)

    2. Щелкните **См. сведения об ошибке**, чтобы увидеть сообщения об ошибках миграции, которые помогут вам устранить их.
