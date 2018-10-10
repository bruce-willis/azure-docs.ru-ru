---
title: Краткое руководство. Создание сервера Базы данных Azure для MariaDB с помощью Azure CLI
description: В этом кратком руководстве описывается создание сервера Базы данных Azure для MariaDB в группе ресурсов Azure с помощью Azure CLI.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: quickstart
ms.date: 09/24/2018
ms.custom: mvc
ms.openlocfilehash: 9e87dacb80aa7fc5f5b073e631fc06ae74b3ad00
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996610"
---
# <a name="create-an-azure-database-for-mariadb-server-using-azure-cli"></a>Создание сервера Базы данных Azure для MariaDB с помощью Azure CLI
В этом кратком руководстве описывается создание сервера Базы данных Azure для MariaDB в группе ресурсов Azure с помощью Azure CLI за 5 минут. Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов.

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этой статьей вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

Если вы используете несколько подписок, выберите соответствующую подписку, в которой находится ресурс либо в которой за него взимается плата. Выберите конкретный идентификатор подписки вашей учетной записи, выполнив команду [az account set](/cli/azure/account#az-account-set).
```azurecli-interactive
az account set --subscription 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Создайте [группу ресурсов Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) с помощью команды [az group create](/cli/azure/group#az-group-create). Группа ресурсов — это логический контейнер, в котором ресурсы Azure развертываются и администрируются как группа.

В следующем примере создается группа ресурсов с именем `myresourcegroup` в расположении `westus`.

```azurecli-interactive
az group create --name myresourcegroup --location westus
```

## <a name="create-an-azure-database-for-mariadb-server"></a>Создание сервера Базы данных Azure для MariaDB
Создайте сервер базы данных Azure для MariaDB, выполнив команду **[az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create)**. Сервер может управлять несколькими базами данных. Как правило, для каждого проекта и для каждого пользователя используется отдельная база данных.

**Параметр** | **Пример значения** | **Описание**
---|---|---
name | mydemoserver | Выберите уникальное имя, идентифицирующее сервер Базы данных Azure для MariaDB. Имя сервера может содержать только строчные буквы, цифры и знак дефиса (-). Его длина должна составлять от 3 до 63 символов.
resource-group | myresourcegroup | Укажите имя группы ресурсов Azure.
sku-name | GP_Gen5_2 | Имя номера SKU. В сокращенной записи соответствует схеме {ценовая категория}_{поколение вычислительных ресурсов}_{число виртуальных ядер}. Под этой таблицей приведены дополнительные сведения о параметре sku-name.
backup-retention | 7 | Срок хранения резервной копии. Указывается в днях. Можно указать от 7 до 35 дней. 
geo-redundant-backup | Отключено | Позволяет включить или отключить создание геоизбыточных резервных копий для этого сервера. Допустимые значения: Enabled, Disabled.
location | westus | Расположение сервера в Azure.
ssl-enforcement | Включено | Позволяет включить или отключить SSL для этого сервера. Допустимые значения: Enabled, Disabled.
storage-size | 51 200 | Объем хранилища сервера (в мегабайтах). Допустимое минимальное значение storage-size составляет 5120 МБ и может быть увеличено с шагом в 1024 МБ. Ознакомьтесь с документом о [ценовых категориях](./concepts-pricing-tiers.md), чтобы узнать больше об ограничениях размера хранилища. 
версия | 10.2 | Основной номер версии ядра СУБД MariaDB.
admin-user | myadmin | Имя для входа администратора. Не может иметь значение **azure_superuser**, **admin**, **administrator**, **root**, **guest** или **public**.
admin-password | Password123 | Пароль администратора. Пароль должен содержать от 8 до 128 символов. Пароль должен содержать знаки трех из следующих категорий: прописные латинские буквы, строчные латинские буквы, цифры и другие знаки.

Значение параметра sku-name соответствует соглашению {ценовая категория}\_{поколение вычислительных ресурсов}\_{количество виртуальных ядер}, как показано в примерах ниже:
+ `--sku-name B_Gen5_4` — ценовая категория "Базовый", поколение 5, 4 виртуальных ядра;
+ `--sku-name GP_Gen5_32` — "Общего назначения", поколение 5, 32 виртуальных ядра;
+ `--sku-name MO_Gen5_2` — "Оптимизированная для операций в памяти", поколение 5, 2 виртуальных ядра.

Допустимые значения для каждого региона и каждого уровня указаны в документации по [ценовым категориям](./concepts-pricing-tiers.md).

В примере ниже показано создание сервера с именем `mydemoserver` в группе ресурсов `myresourcegroup` в регионе "Западная часть США" с именем пользователя администратора сервера `myadmin`. Это сервер **5-го поколения** **общего назначения** с 2 **виртуальными ядрами**. Имя сервера сопоставляется с DNS-именем, и поэтому оно должно быть глобально уникальным в рамках Azure. Замените `<server_admin_password>` собственным значением.
```azurecli-interactive
az mariadb server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 --version 10.2
```

## <a name="configure-firewall-rule"></a>Настройка правила брандмауэра
Создайте правило брандмауэра на уровне сервера Базы данных Azure для MariaDB, выполнив команду **[az mariadb server firewall-rule create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create)**. Правило брандмауэра на уровне сервера позволяет внешним приложениям, таким как программа командной строки **mysql** или MySQL Workbench, подключаться к серверу через брандмауэр службы Azure MariaDB. 

В приведенном ниже примере создается правило брандмауэра с именем `AllowMyIP`, которое разрешает подключения с определенного IP-адреса — 192.168.0.1. Подставьте IP-адрес или диапазон IP-адресов, которые соответствуют расположению, из которого будет устанавливаться подключение. 

```azurecli-interactive
az mariadb server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
> Подключитесь к Базе данных Azure для MariaDB через порт 3306. Если вы пытаетесь подключиться из корпоративной сети, исходящий трафик через порт 3306 может быть запрещен. В таком случае вы не сможете подключиться к серверу. Для этого ваш ИТ-отдел должен открыть порт 3306.
> 

## <a name="configure-ssl-settings"></a>Настройка параметров SSL
По умолчанию между сервером и клиентскими приложениями применяется SSL-соединение. Эта настройка по умолчанию гарантирует безопасное перемещение данных за счет шифрования потока данных через Интернет. Чтобы упростить работу с этим руководством, отключите SSL-соединения для вашего сервера. Мы не рекомендуем так делать для рабочих серверов. Дополнительные сведения см. в статье [Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MariaDB](./howto-configure-ssl.md).

В следующем примере показано, как отключить принудительное SSL-подключение на сервере MariaDB.
 
 ```azurecli-interactive
 az mariadb server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Disabled
 ```

## <a name="get-the-connection-information"></a>Получение сведений о подключении

Чтобы подключиться к серверу, необходимо указать сведения об узле и учетные данные для доступа.

```azurecli-interactive
az mariadb server show --resource-group myresourcegroup --name mydemoserver
```

Результаты выводятся в формате JSON. Запишите значения **fullyQualifiedDomainName** и **administratorLogin**.
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.mariadb.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMariaDB/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMariaDB/servers",
  "userVisibleState": "Ready",
  "version": "10.2"
}
```

## <a name="connect-to-server-using-mysql-command-line"></a>Подключение к серверу с помощью командной строки mysql
Подключитесь к серверу с помощью программы командной строки **mysql**. Эту программу командной строки можно скачать [отсюда](https://dev.mysql.com/downloads/) и установить на компьютер. Вместо этого можно также нажать кнопку **Попробуйте!** в примерах кода или кнопку `>_` на панели инструментов в правом верхнем углу портала Azure и запустить **Azure Cloud Shell**.

Введите следующие команды. 

1. Подключитесь к серверу с помощью программы командной строки **mysql**:
```azurecli-interactive
 mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
```

2. Просмотрите состояние сервера в командной строке mysql>.
```sql
status
```
Если все работает правильно, в программе командной строки должен отобразиться следующий текст:

```bash
C:\Users\>mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p
Enter password: ***********
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 65512
Server version: 5.6.39.0 MariaDB Server

Copyright (c) 2000, 2016, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.23, for Linux (x86_64)

Connection id:          64681
Current database:
Current user:           myadmin@40.118.201.21
SSL:                    Cipher in use is AES256-SHA
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         5.6.39.0 MariaDB Server
Protocol version:       10
Connection:             mydemoserver.mariadb.database.azure.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    utf8
Conn.  characterset:    utf8
TCP port:               3306
Uptime:                 1 day 3 hours 28 min 50 sec

Threads: 10  Questions: 29002  Slow queries: 0  Opens: 33  Flush tables: 3  Open tables: 1  Queries per second avg: 0.293
--------------

mysql>
```

> [!TIP]
> Дополнительные команды см. в [разделе 4.5.1 справочного руководства по MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

## <a name="connect-to-server-using-mysql-workbench"></a>Подключение к серверу с помощью MySQL Workbench
1.  Запустите приложение MySQL Workbench на клиентском компьютере. Скачать и установить MySQL Workbench вы можете [здесь](https://dev.mysql.com/downloads/workbench/).

2.  В диалоговом окне **настройки нового подключения** на вкладке **Параметры** введите следующие сведения:

   ![Настройка нового подключения](./media/quickstart-create-mariadb-server-database-using-azure-cli/setup-new-connection.png)

| **Параметр** | **Рекомендуемое значение** | **Описание** |
|---|---|---|
|   Имя подключения | Мое подключение | Укажите любую метку для этого подключения. |
| Способ подключения | Выберите стандартный способ (по протоколу TCP/IP). | Используйте протокол TCP/IP для подключения к Базе данных Azure для MariaDB. |
| имя узла; | mydemoserver.mariadb.database.azure.com | Имя сервера, которое вы записали ранее. |
| Порт | 3306 | Для MariaDB используется порт по умолчанию. |
| Имя пользователя | myadmin@mydemoserver | Имя для входа администратора сервера, которое вы записали ранее. |
| Пароль | **** | Используйте пароль учетной записи администратора, настроенный ранее. |

Щелкните **Проверить подключение**, чтобы проверить, все ли параметры верно настроены.
Теперь можно щелкнуть только что созданное подключение, чтобы успешно подключиться к серверу.

## <a name="clean-up-resources"></a>Очистка ресурсов
Если эти ресурсы не требуются для изучения другого руководства, вы можете их удалить. Для этого выполните следующую команду: 

```azurecli-interactive
az group delete --name myresourcegroup
```

Если вы хотите удалить созданный сервер, выполните команду **[az mariadb server delete](/cli/azure/mariadb/server#az-mariadb-server-delete)**.
```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

<!--
## Next steps

> [!div class="nextstepaction"]
> [Design a MariaDB Database with Azure CLI](./tutorial-design-database-using-cli.md)
-->