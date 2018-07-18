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
ms.date: 05/18/2018
ms.openlocfilehash: 2d62cd693d7a67faf836c645f8bd33de9afca949
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266114"
---
# <a name="azure-database-for-mysql-data-in-replication-stored-procedures"></a>Хранимые процедуры репликации входных данных базы данных Azure для MySQL

Репликация входных данных позволяет синхронизировать данные работающего локально сервера MySQL в виртуальных машинах или службах баз данных, размещенных другими облачными поставщиками, в службу базы данных Azure для MySQL.

Следующие хранимые процедуры используются для установки или удаления репликации входных данных между исходным экземпляром и репликой.

|**Имя хранимой процедуры**|**Входные параметры**|**Выходные параметры**|**Примечание об использовании**|
|-----|-----|-----|-----|
|*MySQL.az_replication_change_primary*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|Недоступно|Для передачи данных в режиме SSL передайте контекст сертификата ЦС в параметр master_ssl_ca. </br><br>Для передачи данных без использования SSL передайте пустую строку в параметр master_ssl_ca.|
|*mysql.az_replication _start*|Недоступно|Недоступно|Запускает георепликацию.|
|*mysql.az_replication _stop*|Недоступно|Недоступно|Останавливает георепликацию.|
|*mysql.az_replication _remove_primary*|Недоступно|Недоступно|Удаляет отношение репликации между основным экземпляром и репликой.|
|*mysql.az_replication_skip_counter*|Недоступно|Недоступно|Пропускает одну ошибку репликации.|

Чтобы настроить репликацию входных данных между основным экземпляром и репликой базы данных Azure для MySQL, ознакомьтесь со статьей [Настройка Базы данных Azure для MySQL для репликации входных данных](howto-data-in-replication.md).