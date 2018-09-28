---
title: Журналы сервера в базе данных Azure для MariaDB
description: Описание журналов, доступных в базе данных Azure для MariaDB, и параметров для включения различных уровней ведения журналов.
author: rachel-msft
ms.author: raagyema
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d982af64517ed452d907f62a39e975d472951392
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992989"
---
# <a name="server-logs-in-azure-database-for-mariadb"></a>Журналы сервера в базе данных Azure для MariaDB
В базе данных Azure для MariaDB пользователям доступен журнал медленных запросов. Доступ к журналам транзакций не поддерживается. Журнал медленных запросов можно использовать для выявления проблем с производительностью при устранении неполадок.

Дополнительные сведения об этих журналах см. в документации к MariaDB по [журналам медленных запросов](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="access-server-logs"></a>Доступ к журналам сервера
Чтобы просмотреть и скачать журналы сервера в базе данных Azure для MariaDB, можно воспользоваться порталом Azure или Azure CLI.

На портале Azure выберите нужный сервер базы данных Azure для MariaDB. В разделе **Мониторинг** найдите страницу **Журналы сервера**.

<!-- For more information on Azure CLI, see [Configure and access server logs using Azure CLI](howto-configure-server-logs-in-cli.md).-->

## <a name="log-retention"></a>Хранение журналов
Журналы доступны в течение семи дней с момента создания. Если общий объем доступных журналов превышает 7 ГБ, по мере необходимости удаляются самые старые файлы.

Новый файл журнала создается каждые 24 часа или при достижении размера файла 7 ГБ.

## <a name="configure-logging"></a>Настройка журнала
Журнал медленных запросов по умолчанию отключен. Чтобы включить его, установите для параметра slow_query_log значение "ON" (Включено).

Вы можете настроить еще несколько параметров.

- **long_query_time.** Если запрос занимает больше времени, чем задано значением long_query_time (в секундах), информация о нем заносится в журнал. По умолчанию это 10 секунд.
- **log_slow_admin_statements.** Указывает, нужно ли сохранять в журнал slow_query_log административные инструкции, например ALTER_TABLE и ANALYZE_TABLE.
- **log_queries_not_using_indexes**. Указывает, нужно ли сохранять в журнал slow_query_log запросы, не использующие индексы.
- **log_throttle_queries_not_using_indexes.** Ограничивает число не использующих индексы запросов, сохраняемых в журнале медленных запросов. Этот параметр применяется, только если log_queries_not_using_indexes имеет значение "ON" (Включено).

Полное описание параметров журнала медленных запросов см. в [соответствующей документации к MariaDB](https://mariadb.com/kb/en/library/slow-query-log-overview/).

## <a name="next-steps"></a>Дальнейшие действия
- [Настройка и использование журналов сервера с помощью портала Azure](howto-configure-server-logs-portal.md).
