---
title: Хранимые процедуры функции "Репликация входных данных" в Базе данных Azure для MariaDB
description: В этой статье описаны все хранимые процедуры, используемые для репликации входных данных.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: reference
ms.date: 09/24/2018
ms.openlocfilehash: 87c6fa783964c019841810ec38f342a5a44c0ee3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959093"
---
# <a name="azure-database-for-mariadb-data-in-replication-stored-procedures"></a>Хранимые процедуры функции "Репликация входных данных" в Базе данных Azure для MariaDB

Репликация входных данных позволяет синхронизировать данные сервера MariaDB, работающего локально, на виртуальных машинах или в службах баз данных, размещенных другими облачными поставщиками, со службой Базы данных Azure для MariaDB.

Следующие хранимые процедуры используются для установки или удаления репликации входных данных между главным экземпляром и репликой.

|**Имя хранимой процедуры**|**Входные параметры**|**Выходные параметры**|**Примечание об использовании**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Недоступно|Для передачи данных в режиме SSL передайте контекст сертификата ЦС в параметр master_ssl_ca. </br><br>Для передачи данных без использования SSL передайте пустую строку в параметр master_ssl_ca.|
|*mysql.az_replication _start*|Недоступно|Недоступно|Запускает георепликацию.|
|*mysql.az_replication _stop*|Недоступно|Недоступно|Останавливает георепликацию.|
|*mysql.az_replication _remove_master*|Недоступно|Недоступно|Удаляет отношение репликации между главным экземпляром и репликой.|
|*mysql.az_replication_skip_counter*|Недоступно|Недоступно|Пропускает одну ошибку репликации.|

Чтобы настроить репликацию входных данных между главным экземпляром и репликой Базы данных Azure для MariaDB, ознакомьтесь со статьей [Настройка Базы данных Azure для MariaDB для репликации входных данных](howto-data-in-replication.md).