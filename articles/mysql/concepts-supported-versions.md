---
title: Поддерживаемые версии в службе "База данных Azure для MySQL"
description: Описываются поддерживаемые версии в базе данных Azure для MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/23/2018
ms.openlocfilehash: c9a533ed9b9eb9ac53a02439b98a78954c7aaa11
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265254"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Поддерживаемые версии сервера базы данных Azure для MySQL
Служба "База данных Azure для MySQL" разработана на базе [MySQL Community Edition](https://www.mysql.com/products/community/) с использованием подсистемы InnoDB.  Сейчас база данных Azure для MySQL поддерживает перечисленные ниже версии.

## <a name="mysql-version-5639"></a>MySQL версии 5.6.39
Сведения об улучшениях и исправлениях в MySQL 5.6.39 см. в [документации](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) по MySQL.

## <a name="mysql-version-5721"></a>MySQL версии 5.7.21
Сведения об улучшениях и исправлениях в MySQL 5.7.21 см. в [документации](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) по MySQL.

> [!NOTE]
> В службе для перенаправления подключений к экземплярам сервера используется шлюз. После установки подключения в клиенте MySQL отображается версия MySQL, установленная в шлюзе, а не фактическая версия, которая работает на вашем экземпляре сервера MySQL. Чтобы определить версию MySQL на экземпляре сервера, выполните команду `SELECT VERSION();` в командной строке MySQL. 

## <a name="managing-updates-and-upgrades"></a>Управление обновлениями
Служба автоматически управляет установкой исправлений для обновления дополнительного номера версии. Обновление основных номеров версий не поддерживается (например, обновление MySQL 5.6 до MySQL 5.7).

## <a name="next-steps"></a>Дополнительная информация

Сведения о квотах и ограничениях для конкретных ресурсов с учетом вашего **уровня служб** представлены в статье [Уровни служб в базе данных Azure для MySQL](./concepts-pricing-tiers.md).
