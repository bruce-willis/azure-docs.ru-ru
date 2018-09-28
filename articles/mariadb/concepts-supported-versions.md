---
title: Поддерживаемые версии в службе "База данных Azure для MariaDB"
description: Описываются поддерживаемые версии в базе данных Azure для MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aa83b9955839aaa03aa2ebf46c9e464dc75c8d3a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977508"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Поддерживаемые версии сервера базы данных Azure для MariaDB
База данных Azure для MariaDB была разработана с открытым исходным кодом [сервера MariaDB](https://downloads.mariadb.org/) с использованием подсистемы InnoDB. Сейчас база данных Azure для MySQL поддерживает перечисленные ниже версии:

## <a name="mariadb-version-10217"></a>Версия MariaDB 10.2.17
Сведения об улучшениях и исправлениях в MariaDB 10.2.17 см. в [документации MariaDB](https://downloads.mariadb.org/mariadb/10.2.17/).

> [!NOTE]
> В службе для перенаправления подключений к экземплярам сервера используется шлюз. После установки подключения в клиенте MySQL отображается версия MariaDB, установленная в шлюзе, а не фактическая версия, которая работает на вашем экземпляре сервера MariaDB. Чтобы определить версию MariaDB на экземпляре сервера, выполните команду `SELECT VERSION();` в командной строке MySQL.

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
Служба автоматически управляет установкой исправлений для обновления дополнительного номера версии.

## <a name="next-steps"></a>Дополнительная информация
Сведения о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб** представлены в статье [Уровни служб в базе данных Azure для MySQL](./concepts-pricing-tiers.md).