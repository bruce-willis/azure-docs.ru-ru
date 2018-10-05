---
title: Восстановление резервной копии в Управляемый экземпляр Базы данных SQL Azure | Документация Майкрософт
description: Восстановление резервной копии базы данных в Управляемый экземпляр Базы данных SQL Azure с помощью SSMS.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 6cad73c8b8fa6a2fa95a6ea0c1fdb5d4114ffd41
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47180105"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Восстановление резервной копии базы данных в Управляемый экземпляр Базы данных SQL Azure

В этом руководстве описано, как восстановить резервную копию базы данных, хранящейся в хранилище BLOB-объектов Azure, в Управляемый экземпляр с помощью стандартного файла резервной копии Wide World Importers. Использование этого метода сопряжено с некоторым простоем. 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

Сведения об использовании Azure Database Migration Service (DMS) для миграции см. в статье о [миграции SQL Server в управляемый экземпляр базы данных SQL Azure](../dms/tutorial-sql-server-to-managed-instance.md). Дополнительные сведения о методах миграции см. в разделе [Перенос экземпляра SQL Server в Управляемый экземпляр Базы данных SQL Azure](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве выполняются следующие требования.
- В качестве начальной точки используются ресурсы, созданные в кратком руководстве [Создание Управляемого экземпляра SQL Azure](sql-database-managed-instance-get-started.md).
- Требуется последняя версия [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) на локальном клиентском компьютере.
- Требуется подключение к Управляемому экземпляру с помощью SQL Server Management Studio. Различные возможности подключения см. в следующих кратких руководствах.
  - [Connect to an Azure SQL Database Managed Instance from an Azure VM](sql-database-managed-instance-configure-vm.md) (Подключение к Управляемому экземпляру Базы данных SQL Azure с виртуальной машины Azure).
  - [Connect to an Azure SQL Database Managed Instance from on-premises using a Point-to-Site connection](sql-database-managed-instance-configure-p2s.md) (Подключение к Управляемому экземпляру Базы данных SQL Azure из локальной среды с помощью подключения "точка — сайт").
- Используется предварительно настроенная учетная запись хранилища BLOB-объектов Azure, содержащая Wide World Importers — стандартный файл резервной копии (загруженный с https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak)).

> [!NOTE]
> Дополнительные сведения о резервном копировании и восстановлении базы данных SQL Server при помощи хранилища BLOB-объектов Azure и подписанного URL-адреса (SAS) см. в разделе [Резервное копирование в SQL Server по URL-адресу](sql-database-managed-instance-get-started-restore.md).

## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>Восстановление базы данных Wide World Importers из файла резервной копии

С помощью SSMS выполните следующие шаги для восстановления базы данных Wide World Importers в управляемый экземпляр из файла резервной копии.

1. Откройте SQL Server Management Studio (SSMS) и подключитесь к Управляемому экземпляру.
2. Откройте в SSMS окно нового запроса.
3. Воспользуйтесь следующим сценарием для создания учетных данных в Управляемом экземпляре с помощью предварительно настроенной учетной записи хранения и ключа SAS.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![Создание учетных данных](./media/sql-database-managed-instance-get-started-restore/credential.png)

    > [!NOTE]
    > Всегда удаляйте **?** в начале созданного ключа SAS.
  
3. Используйте следующий сценарий для проверки допустимости учетных данных и резервной копии SAS. Укажите URL-адрес контейнера, в котором находится файл резервной копии.

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![список файлов](./media/sql-database-managed-instance-get-started-restore/file-list.png)

4. Используйте приведенный ниже скрипт для восстановления базы данных Wide World Importers из файла резервной копии. Укажите URL-адрес контейнера, в котором находится этот файл.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![Восстановление](./media/sql-database-managed-instance-get-started-restore/restore.png)

5. Чтобы отслеживать состояние восстановления, выполните следующий запрос в новом сеансе запроса:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

6. По завершении восстановления просмотрите восстановленную базу данных в обозревателе объектов. 

## <a name="next-steps"></a>Дополнительная информация

- Сведения об устранении неполадок резервного копирования на URL-адрес см. в разделе [Резервное копирование SQL Server на URL-адрес — рекомендации и устранение неполадок](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Обзор параметров подключения см. в разделе [Подключение приложения к Управляемому экземпляру Базы данных SQL](sql-database-managed-instance-connect-app.md).
- Чтобы отправлять запросы с помощью одного из привычных средств или языков, ознакомьтесь с [краткими руководствами по подключению и созданию запросов к Базе данных SQL Azure](sql-database-connect-query.md).
