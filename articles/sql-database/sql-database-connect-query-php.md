---
title: Использование PHP для создания запросов к базе данных SQL Azure | Документация Майкрософт
description: В этой статье показано, как использовать PHP для создания программы, которая подключается к базе данных SQL Azure, и создавать к ней запросы с помощью инструкций Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 2fc7b961df003fe05bc4ad4e49b9debb74c952fd
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063240"
---
# <a name="use-php-to-query-an-azure-sql-database"></a>Использование PHP для создания запросов к базе данных SQL Azure

В этом кратком руководстве показано, как использовать [PHP](http://php.net/manual/en/intro-whatis.php) для создания программы, которая подключается к Базе данных SQL Azure, а затем с помощью инструкций Transact-SQL выполнить запрос данных.

## <a name="prerequisites"></a>Предварительные требования

Ниже указаны требования для работы с этим кратким руководством.

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- [Правило брандмауэра уровня сервера](sql-database-get-started-portal-firewall.md) для общедоступного IP-адреса компьютера, на котором выполняются действия из этого краткого руководства.

- Убедитесь, что установлен компонент PHP и связанное программное обеспечение для вашей операционной системы.

    - **Mac OS.** Установите Homebrew, PHP, драйвер ODBC и SQLCMD, а затем драйвер PHP для SQL Server. См. [шаги 1.2, 1.3 и 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/mac/).
    - **Ubuntu.** Установите PHP и другие необходимые пакеты, а затем установите драйвер PHP для SQL Server. См. [шаги 1.2 и 2.1](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/).
    - **Windows.** Установите последнюю версию PHP для IIS Express, последнюю версию драйверов Майкрософт для SQL Server в IIS Express, Chocolatey, драйвер ODBC и SQLCMD. См. [шаги 1.2 и 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).    

## <a name="sql-server-connection-information"></a>Сведения о подключении SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
## <a name="insert-code-to-query-sql-database"></a>Вставка кода для отправки запроса к базе данных SQL

1. Создайте файл **sqltest.php** в предпочитаемом текстовом редакторе.  

2. Замените содержимое следующим кодом и добавьте соответствующие значения для сервера, базы данных, пользователя и пароля.

   ```PHP
   <?php
   $serverName = "your_server.database.windows.net";
   $connectionOptions = array(
       "Database" => "your_database",
       "Uid" => "your_username",
       "PWD" => "your_password"
   );
   //Establishes the connection
   $conn = sqlsrv_connect($serverName, $connectionOptions);
   $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid";
   $getResults= sqlsrv_query($conn, $tsql);
   echo ("Reading data from table" . PHP_EOL);
   if ($getResults == FALSE)
       echo (sqlsrv_errors());
   while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
   }
   sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Выполнение кода

1. В командной строке выполните следующие команды:

   ```php
   php sqltest.php
   ```

2. Убедитесь, что возвращены первые 20 строк, а затем закройте окно приложения.

## <a name="next-steps"></a>Дополнительная информация
- [Проектирование первой базы данных SQL Azure](sql-database-design-first-database.md)
- [Microsoft PHP Drivers for SQL Server](https://github.com/Microsoft/msphpsql/) (Драйверы Microsoft PHP для SQL Server)
- [Сообщите о проблемах или задайте вопросы](https://github.com/Microsoft/msphpsql/issues)
- [Пример логики повтора: отказоустойчивое подключение к SQL с помощью PHP][step-4-connect-resiliently-to-sql-with-php-p42h]


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php

