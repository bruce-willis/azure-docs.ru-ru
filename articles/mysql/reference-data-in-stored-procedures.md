---
title: Хранимые процедуры репликации входных данных базы данных Azure для MySQL
description: В этой статье описаны все хранимые процедуры, используемые для репликации входных данных.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: fb1a1b31d90df0022e5973de3ae2f55fb4c36701
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665952"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Хранимые процедуры репликации входных данных базы данных Azure для MySQL

Репликация входных данных позволяет синхронизировать данные работающего локально сервера MySQL в виртуальных машинах или службах баз данных, размещенных другими облачными поставщиками, в службу базы данных Azure для MySQL.

Следующие хранимые процедуры используются для установки или удаления репликации входных данных между главным экземпляром и репликой.

|**Имя хранимой процедуры**|**Входные параметры**|**Выходные параметры**|**Примечание об использовании**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Недоступно|Для передачи данных в режиме SSL передайте контекст сертификата ЦС в параметр master_ssl_ca. </br><br>Для передачи данных без использования SSL передайте пустую строку в параметр master_ssl_ca.|
|*mysql.az_replication _start*|Недоступно|Недоступно|Запускает георепликацию.|
|*mysql.az_replication _stop*|Недоступно|Недоступно|Останавливает георепликацию.|
|*mysql.az_replication _remove_master*|Недоступно|Недоступно|Удаляет отношение репликации между главным экземпляром и репликой.|
|*mysql.az_replication_skip_counter*|Недоступно|Недоступно|Пропускает одну ошибку репликации.|

Чтобы настроить репликацию входных данных между главным экземпляром и репликой базы данных Azure для MySQL, ознакомьтесь со статьей [Настройка Базы данных Azure для MySQL для репликации входных данных](howto-data-in-replication.md).
