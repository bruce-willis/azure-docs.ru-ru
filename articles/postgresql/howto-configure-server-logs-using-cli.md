---
title: Настройка журналов сервера для PostgreSQL и получение к ним доступа с помощью Azure CLI
description: В этой статье описывается, как настроить журналы сервера в базе данных Azure для PostgreSQL с помощью командной строки CLI Azure и получить доступ к этим журналам.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: af5a0843acced4611b219088b7662f9ea4b32beb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969588"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Настройка журналов сервера и получение к ним доступа с помощью Azure CLI
Вы можете скачать журналы ошибок сервера PostgreSQL с помощью интерфейса командной строки (Azure CLI). Но доступ к журналам транзакций не поддерживается. 

## <a name="prerequisites"></a>Предварительные требования
Прежде чем приступить к выполнению этого руководства, необходимы следующие компоненты:
- [сервер базы данных Azure для PostgreSQL](quickstart-create-server-database-azure-cli.md).
- Программа командной строки [Azure CLI 2.0](/cli/azure/install-azure-cli) или Azure Cloud Shell в браузере

## <a name="configure-logging-for-azure-database-for-postgresql"></a>Настройка ведения журнала для базы данных Azure для PostgreSQL
Можно настроить на сервере доступ к журналам запросов и журналам ошибок. Журналы ошибок могут содержать сведения об автоматической очистке, подключениях и контрольных точках.
1. Включите ведение журнала.
2. Чтобы включить ведение журнала запросов, обновите **log\_statement** и **log\_min\_duration\_statement**.
3. Обновите срок хранения.

Дополнительные сведения см. в статье [Настройка параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs-for-azure-database-for-postgresql-server"></a>Получение списка журналов для базы данных Azure для сервера PostgreSQL
Чтобы получить список доступных файлов журналов для сервера, выполните команду [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list).

Можно отобразить список файлов журнала для сервера **mydemoserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup**. Затем направьте список файлов журнала в текстовый файл с именем **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Скачивание журналов с сервера в локальную среду
Команда [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) позволяет скачать отдельные файлы журналов для сервера. 

В следующем примере в локальную среду скачивается определенный файл журнала для сервера **mydemoserver.postgres.database.azure.com** в группе ресурсов **myresourcegroup**.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Дополнительная информация
- См. дополнительные сведения о [журналах сервера в базе данных Azure для PostgreSQL](concepts-server-logs.md).
- См. дополнительные сведения о [настройке параметров конфигурации сервера с помощью Azure CLI](howto-configure-server-parameters-using-cli.md).
