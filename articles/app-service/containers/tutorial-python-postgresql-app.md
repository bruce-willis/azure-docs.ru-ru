---
title: Создание веб-приложения Python с подключением к базе данных PostgreSQL в Службе приложений Azure | Документация Майкрософт
description: Узнайте, как запустить управляемое данными приложение Python в Azure с подключением к базе данных PostgreSQL.
services: app-service\web
documentationcenter: python
author: cephalin
manager: jeconnoc
ms.service: app-service-web
ms.workload: web
ms.devlang: python
ms.topic: tutorial
ms.date: 09/28/2018
ms.author: beverst;cephalin
ms.custom: mvc
ms.openlocfilehash: f4ce197d541b8573e38fd85dcebb575c8ee99f59
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435792"
---
# <a name="build-a-docker-python-and-postgresql-web-app-in-azure"></a>Создание в Azure веб-приложения Docker Python с подключением к базе данных PostgreSQL

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. Это руководство демонстрирует создание управляемого данными веб-приложения Python, используя в качестве серверной части базу данных PostgreSQL. После выполнения всех действий у вас будет приложение Python Flask, работающее в контейнере Docker в службе приложений на платформе Linux.

![Приложение Docker Python Flask в службе приложений на платформе Linux](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание базы данных PostgreSQL в Azure
> * Подключение приложения Python к PostgreSQL
> * Развертывание приложения в Azure
> * просмотр журналов диагностики;
> * Обновление модели данных и повторное развертывание приложения.
> * Управление приложением на портале Azure.

Следуйте инструкциям в этой статье о macOS. Инструкции для Linux и Windows в большей степени совпадают, но различия не описаны в этом руководстве.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

1. [установите Git](https://git-scm.com/);
1. [установите Python](https://www.python.org/downloads/).
1. [установите и запустите PostgreSQL](https://www.postgresql.org/download/);

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Проверка локальной установки PostgreSQL и создание базы данных

В окне локального терминала выполните команду `psql` для подключения к локальному серверу PostgreSQL.

```bash
sudo -u postgres psql postgres
```

Если вы видите сообщение об ошибке вида `unknown user: postgres`, возможно, для установленного экземпляра PostgreSQL настроено ваше имя пользователя, использованное для входа. Введите приведенную ниже команду.

```bash
psql postgres
```

Если подключение успешно установлено, это означает, что база данных PostgreSQL запущена. В противном случае запустите локальную базу данных PostgreSQL, выполнив инструкции по [скачиванию ядра PostgreSQL](https://www.postgresql.org/download/) для используемой операционной системы.

Создайте базу данных *eventregistration* и настройте отдельного пользователя базы данных пользователя *manager* с паролем *supersecretpass*.

```sql
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Введите `\q`, чтобы выйти из клиента PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Создание локального приложения Python

На этом шаге вы настроите локальный проект Python Flask.

### <a name="clone-the-sample-app"></a>Клонирования примера приложения

Откройте окно терминала и c помощью команды `CD` перейдите в рабочий каталог.

Затем выполните следующие команды, чтобы клонировать репозиторий с примером.

```bash
git clone https://github.com/Azure-Samples/flask-postgresql-app.git
cd flask-postgresql-app
```

Этот пример репозитория содержит приложение [Flask](http://flask.pocoo.org/).

### <a name="run-the-app-locally"></a>Локальный запуск приложения

Установите необходимые пакеты и запустите приложение.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run

# PowerShell
pip install virtualenv
virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
cd app
Set-Item Env:FLASK_APP ".\app.py"
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
DBHOST="localhost" DBUSER="manager" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

После полной загрузки приложения вы увидите следующее сообщение:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Откройте браузер и перейдите по адресу `http://localhost:5000`. Щелкните **Зарегистрировать** и создайте тестового пользователя.

![Приложение Python Flask, выполняемое в локальной среде](./media/tutorial-python-postgresql-app/local-app.png)

Пример приложения Flask хранит данные пользователей в базе данных. Если вам удалось зарегистрировать пользователя, значит, ваше приложение записывает данные в локальную базу данных PostgreSQL.

Чтобы остановить Flask в любое время, введите Ctrl+C в окне терминала.

## <a name="create-a-production-postgresql-database"></a>Создание рабочей базы данных PostgreSQL

На этом шаге вы создадите базу данных PostgreSQL в Azure. При развертывании приложения в Azure используется эта облачная база данных.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера базы данных Azure для PostgreSQL

Создайте сервер PostgreSQL с помощью команды [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) в Cloud Shell.

В следующем примере команды замените *\<postgresql_name>* на уникальное имя сервера, а *\<admin_username>* и *\<admin_password>* замените учетными данными нужного пользователя. Указываются данные учетной записи администратора базы данных. Это имя используется как часть конечной точки PostgreSQL (`https://<postgresql_name>.postgres.database.azure.com`), поэтому оно должно быть уникальным на всех серверах в Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql_name> --location "West Europe" --admin-user <admin_username> --admin-password <admin_password> --sku-name B_Gen4_1
```

После создания сервера базы данных Azure для PostgreSQL в Azure CLI отображаются следующие сведения.

```json
{
  "administratorLogin": "<admin_username>",
  "fullyQualifiedDomainName": "<postgresql_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql_name>",
  "location": "westus",
  "name": "<postgresql_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Запомните значения \<admin_username> и \<admin_password> для использования в будущем. Они понадобятся для входа на сервер Postgre и его базы данных.


### <a name="create-firewall-rules-for-the-postgresql-server"></a>Создание правил брандмауэра для сервера PostgreSQL

Чтобы разрешить доступ к базе данных из ресурсов Azure, в Cloud Shell выполните следующие команды Azure CLI.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Этот параметр разрешает сетевые подключения со всех IP-адресов в сети Azure. Для рабочей среды попробуйте настроить как можно более строгие правила брандмауэра, [указав только исходящие IP-адреса, используемые вашим приложением](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

В Cloud Shell повторно выполните соответствующую команду, чтобы разрешить доступ с локального компьютера, заменив *\<you_ip_address >* [локальным IPv4-адресом](http://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql_name> --start-ip-address=<your_ip_address> --end-ip-address=<your_ip_address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Подключение приложения Python к рабочей базе данных

На этом шаге подключается пример приложения Flask к серверу базы данных Azure для PostgreSQL, которая была создана.

### <a name="create-empty-database-and-user-access"></a>Создание пустой базы данных и доступа пользователя

В окне терминала на локальном компьютере подключитесь к базе данных, выполнив следующую команду. При появлении запроса введите пароль администратора. Используйте тот же пароль, который был указан в разделе [Создание сервера базы данных Azure для PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql_name>.postgres.database.azure.com -U <my_admin_username>@<postgresql_name> postgres
```

Точно так же, как и на локальном сервере Postgres, создайте базу данных и пользователя на сервере Azure Postgres.

```bash
CREATE DATABASE eventregistration;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE eventregistration TO manager;
```

Введите `\q`, чтобы выйти из клиента PostgreSQL.

> [!NOTE]
> Рекомендуется создать пользователей базы данных с разрешениями только для конкретных приложений, а не с правами администратора. В этом примере пользователь `manager` имеет все привилегии _только_ для базы данных `eventregistration`.

### <a name="test-app-connectivity-to-production-database"></a>Тестирование подключения приложения к рабочей базе данных

Вернитесь в окно локального терминала, выполните следующие команды для миграции базы данных и сервера Flask.

```bash
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

После полной загрузки приложения вы увидите следующее сообщение:

```bash
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> 791cd7d80402, empty message
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

Откройте браузер и перейдите по адресу http://localhost:5000. Щелкните **Зарегистрировать** и создайте тестовую регистрацию. Теперь приложение записывает данные в базу данных Azure.

![Приложение Python Flask, выполняемое в локальной среде](./media/tutorial-python-postgresql-app/local-app.png)

## <a name="deploy-to-azure"></a>Развернуть в Azure

На этом шаге вы развернете приложение Python, подключенное к базе данных Postgres, в Службе приложений Azure.

### <a name="configure-repository"></a>Настройка репозитория

Подсистема развертывания Git в службе приложений вызывает автоматизацию `pip` при наличии _application.py_ в корне репозитория. В этом руководстве вы разрешите подсистеме развертывания выполнить автоматизацию. В окне терминала на локальном компьютере перейдите в корень репозитория, создайте фиктивный файл _application.py_ и зафиксируйте внесенные изменения.

```bash
cd ..
touch application.py
git add .
git commit -m "ensure azure automation"
```

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений 

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения 

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Настройка переменных среды

Ранее в этом руководстве вы определили переменные среды для подключения к базе данных PostgreSQL.

В службе приложений переменные среды задаются в качестве _параметров приложения_ с помощью команды [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell.

Код ниже указывает сведения о подключении к базе данных как параметры приложения. 

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBPASS="supersecretpass" DBNAME="eventregistration"
```

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 5, done. 
Delta compression using up to 4 threads. 
Compressing objects: 100% (5/5), done. 
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done. 
Total 5 (delta 3), reused 0 (delta 0) 
remote: Updating branch 'master'. 
remote: Updating submodules. 
remote: Preparing deployment for commit id '6c7c716eee'. 
remote: Running custom deployment command... 
remote: Running deployment command... 
remote: Handling node.js deployment. 
. 
. 
. 
remote: Deployment successful. 
To https://<app_name>.scm.azurewebsites.net/<app_name>.git 
 * [new branch]      master -> master 
```  

### <a name="configure-entry-point"></a>Настройка точки входа

По умолчанию встроенный образ выполняет поиск файла _wsgi.py_ или _application.py_ в корневом каталоге для использования в качестве точки входа, но вашей точкой входа является _app/app.py_. Файл _application.py_, который вы ранее добавили, пустой и не выполняет никаких действий.

В Cloud Shell выполните команду [`az webapp config set`](/cli/azure/webapp/config?view=azure-cli-latest#az-webapp-config-set), чтобы задать настраиваемый сценарий запуска.

```azurecli-interactive
az webapp config set --name <app_name> --resource-group myResourceGroup --startup-file "gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/app app:app"
```

Параметр `--startup-file` принимает настраиваемую команду или путь к файлу, который содержит настраиваемую команду. Настраиваемая команда должна иметь приведенный ниже формат.

```
gunicorn '--bind=0.0.0.0' --chdir /home/site/wwwroot/<subdirectory> <module>:<variable>
```

В настраиваемой команде параметр `--chdir` является обязательным, если точка входа находится не в корневом каталоге, в `<subdirectory>` является подкаталогом. `<module>` — имя _PY_-файла, а `<variable>` — переменная в модуле, которая представляет ваше веб-приложение.

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure

Перейдите к развернутому веб-приложению. Запуск занимает некоторое время, так как при первом запросе приложения контейнер должен быть скачан и запущен. Если истекает время ожидания страницы или отображается сообщение об ошибке, подождите несколько минут и обновите страницу.

```bash
http://<app_name>.azurewebsites.net
```

Вы увидите зарегистрированных ранее гостей, которые были сохранены в рабочей базе данных Azure на предыдущем шаге.

![Приложение Python Flask, выполняемое в Azure](./media/tutorial-python-postgresql-app/docker-app-deployed.png)

**Поздравляем!** Вы запустили приложение Python в службе приложений для Linux.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

Так как приложение Python работает в контейнере, служба приложений на платформе Linux позволяет обращаться к журналам консоли, создаваемым в контейнере. Чтобы найти журналы, перейдите к этому URL-адресу:

```
https://<app_name>.scm.azurewebsites.net/api/logs/docker
```

Вы должны увидеть два объекта JSON со свойством `href`. Одно свойство `href` (которое заканчивается на `_docker.log`) указывает на журналы консоли Docker, а другое свойство `href` указывает на журналы консоли, создаваемые в контейнере Python. 

```json
[  
   {  
      "machineName":"RD0003FF61ACD0_default",
      "lastUpdated":"2018-09-27T16:48:17Z",
      "size":4766,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_default_docker.log"
   },
   {  
      "machineName":"RD0003FF61ACD0",
      "lastUpdated":"2018-09-27T16:48:19Z",
      "size":2589,
      "href":"https://<app_name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log",
      "path":"/home/LogFiles/2018_09_27_RD0003FF61ACD0_docker.log"
   }
]
```

Скопируйте нужное значение `href` в окно браузера перейдите к журналам. Журналы не передаются потоком, поэтому может возникнуть задержка. Чтобы просмотреть новые журналы, обновите страницу браузера.

## <a name="update-data-model-and-redeploy"></a>Обновление модели данных и повторное развертывание

На этом шаге вы добавите несколько участников для каждой регистрации событий, обновив модель `Guest`, а затем повторно развернув обновление в Azure.

В окне терминала на локальном компьютере получите для изменения файлы из ветви `modelChange` с помощью приведенной ниже команды git.

```bash
git checkout origin/modelChange -- .
```

При этом в модель, представления и контроллеры будут внесены необходимые изменения. Он также включает в себя перенос базы данных с использованием *Alembic* (`flask db migrate`). Все изменения можно просмотреть с помощью следующей команды git.

```bash
git diff master origin/modelChange
```

### <a name="test-your-changes-locally"></a>Проверьте изменения локально.

В окне локального терминала выполните приведенные ниже команды, чтобы проверить изменения в локальной среде, запустив сервер Flask.

```bash
source venv/bin/activate
cd app
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask db upgrade
FLASK_APP=app.py DBHOST="<postgresql_name>.postgres.database.azure.com" DBUSER="manager@<postgresql_name>" DBNAME="eventregistration" DBPASS="supersecretpass" flask run
```

Перейдите по адресу http://localhost:5000 в браузере, чтобы просмотреть изменения. Создайте тестовую регистрацию.

![Приложение Python Flask из контейнера Docker, выполняемое в локальной среде](./media/tutorial-python-postgresql-app/local-app-v2.png)

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

В окне терминала на локальном компьютере зафиксируйте все изменения в Git, а затем отправьте изменение кода в Azure.

```bash 
git add . 
git commit -m "updated data model" 
git push azure master 
``` 

Перейдите в веб-приложение Azure и еще раз проверьте новые функции. Обязательно обновите страницу.

```bash
http://<app_name>.azurewebsites.net
```

![Приложение Docker Python Flask в службе приложений Azure](./media/tutorial-python-postgresql-app/docker-flask-in-azure.png)

## <a name="manage-your-web-app-in-the-azure-portal"></a>Управление веб-приложением с помощью портала Azure

Перейдите на [портал Azure](https://portal.azure.com), чтобы увидеть созданное веб-приложение.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/tutorial-python-postgresql-app/app-resource.png)

По умолчанию на портале отображается страница **Обзор** веб-приложения. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Страница службы приложений на портале Azure](./media/tutorial-python-postgresql-app/app-mgmt.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание базы данных PostgreSQL в Azure
> * Подключение приложения Python к PostgreSQL
> * Развертывание приложения в Azure
> * просмотр журналов диагностики;
> * Обновление модели данных и повторное развертывание приложения.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Настройка встроенного образа Python](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](../app-service-web-tutorial-custom-domain.md)

