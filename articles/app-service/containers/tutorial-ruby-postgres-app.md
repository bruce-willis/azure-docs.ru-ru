---
title: Разработка веб-приложений на основе Ruby и Postgres в службе приложений Azure на платформе Linux | Документация Майкрософт
description: Узнайте, как создать приложение Ruby, работающее в Azure, с подключением к базе данных PostgreSQL в Azure.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
ms.service: app-service-web
ms.workload: web
ms.devlang: ruby
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 925537b3dff852921aad1e74d009e09fc90c394a
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445082"
---
# <a name="build-a-ruby-and-postgres-web-app-in-azure-app-service-on-linux"></a>Разработка веб-приложения на основе Ruby и Postgres в службе приложений Azure на платформе Linux

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это высокомасштабируемая служба размещения с самостоятельной установкой исправлений на основе операционной системы Linux. В этом руководстве показано, как создать веб-приложение Ruby и подключить его к базе данных PostgreSQL. По завершении вы получите приложение [Ruby on Rails](http://rubyonrails.org/), работающее в службе приложений под управлением Linux.

![Приложение Ruby on Rails, работающее в службе приложений Azure](./media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание базы данных PostgreSQL в Azure
> * Подключение приложения Ruby on Rails к PostgreSQL
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

* [установите Git](https://git-scm.com/);
* [установите Ruby 2.3](https://www.ruby-lang.org/en/documentation/installation/);
* [установите Ruby on Rails 5.1](http://guides.rubyonrails.org/v5.1/getting_started.html);
* [установите и запустите PostgreSQL](https://www.postgresql.org/download/);

## <a name="prepare-local-postgres"></a>Подготовка локальной базы данных Postgres

На этом шаге вы создадите пример базы данных на локальном сервере Postgres.

### <a name="connect-to-local-postgres-server"></a>Подключение к локальному серверу Postgres

Откройте окно терминала и выполните команду `psql` для подключения к локальному серверу Postgres.

```bash
sudo -u postgres psql
```

Если подключение установлено, это означает, что база данных Postgres запущена. В противном случае запустите локальную базу данных Postgres, выполнив инструкции по [скачиванию ядра PostgreSQL](https://www.postgresql.org/download/).

Введите `\q`, чтобы выйти из клиента Postgres. 

С помощью своего имени пользователя для входа в Linux создайте пользователя Postgres, который сможет создавать базы данных, выполняя следующую команду:

```bash
sudo -u postgres createuser -d <signed_in_user>
```

<a name="step2"></a>

## <a name="create-a-ruby-on-rails-app-locally"></a>Создание приложения Ruby on Rails локально
На этом шаге вы создадите пример приложения Ruby on Rails, настроите его подключение к базе данных и запустите это приложение в локальной среде. 

### <a name="clone-the-sample"></a>Клонирования репозитория

Откройте окно терминала и c помощью команды `cd` перейдите в рабочий каталог.

Выполните команду ниже, чтобы клонировать репозиторий с примером.

```bash
git clone https://github.com/Azure-Samples/rubyrails-tasks.git
```

С помощью команды `cd` перейдите в клонированный каталог. Установите необходимые пакеты.

```bash
cd rubyrails-tasks
bundle install --path vendor/bundle
```

### <a name="run-the-sample-locally"></a>Локальный запуск примера

Выполните [перенос Rails](http://guides.rubyonrails.org/active_record_migrations.html#running-migrations), чтобы создать таблицы, необходимые для приложения. Чтобы узнать, какие таблицы создаются при переносе, просмотрите каталог _db/migrate_ в репозитории Git.

```bash
rake db:create
rake db:migrate
```

Запустите приложение.

```bash
rails server
```

Откройте браузер и перейдите по адресу `http://localhost:3000`. Добавьте несколько задач на странице.

![Успешное подключение Ruby on Rails к Postgres](./media/tutorial-ruby-postgres-app/postgres-connect-success.png)

Чтобы остановить сервер Rails, введите `Ctrl + C` в окне терминала.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-postgres-in-azure"></a>Создание Postgres в Azure

На этом этапе вы создадите базу данных Postgres в [Базе данных Azure для PostgreSQL](/azure/postgresql/). Позже вы настроите приложение Ruby on Rails для подключения к этой базе данных.

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)] 

### <a name="create-a-postgres-server"></a>Создание сервера Postgres

Создайте сервер PostgreSQL с помощью команды [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create).

Выполните следующую команду в Cloud Shell и замените уникальное имя сервера заполнителем  *\<имя_сервера_postgres>*. Имя сервера должно быть уникальным на всех серверах в Azure. 

```azurecli-interactive
az postgres server create --location "West Europe" --resource-group myResourceGroup --name <postgres_server_name> --admin-user adminuser --admin-password My5up3r$tr0ngPa$w0rd! --sku-name GP_Gen4_2
```

После создания сервера базы данных Azure для PostgreSQL в Azure CLI отображаются следующие сведения.

```json
{
  "administratorLogin": "adminuser",
  "earliestRestoreDate": "2018-06-15T12:38:25.280000+00:00",
  "fullyQualifiedDomainName": "<postgres_server_name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgres_server_name>",
  "location": "westeurope",
  "name": "<postgres_server_name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  < Output has been truncated for readability >
}
```

### <a name="configure-server-firewall"></a>Настройка брандмауэра сервера

В Cloud Shell создайте правило брандмауэра для сервера Postgres, выполнив команду [`az postgres server firewall-rule create`](/cli/azure/postgres/server/firewall-rule?view=azure-cli-latest#az-postgres-server-firewall-rule-create), чтобы разрешить подключения клиентов. Если для начального и конечного IP-адресов задано значение 0.0.0.0, брандмауэр открыт только для других ресурсов Azure. Замените уникальное имя сервера заполнителем *\<имя_сервера_postgres>*.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server <postgres_server_name> --name AllowAllIps --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
```

> [!TIP] 
> Вы можете применить еще более строгие ограничения в правиле брандмауэра, [разрешив только исходящие IP-адреса, используемые приложением](../app-service-ip-addresses.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="connect-to-production-postgres-server-locally"></a>Локальное подключение к рабочему серверу Postgres

В Cloud Shell установите соединение с сервером Postgres в Azure. Используйте значение, указанное ранее для заполнителей _&lt;имя_сервера_postgres>_.

```bash
psql -U adminuser@<postgres_server_name> -h <postgres_server_name>.postgres.database.azure.com postgres
```

При появлении запроса на ввод пароля используйте пароль _My5up3r$tr0ngPa$w0rd!_, указанный во время создания сервера базы данных.

### <a name="create-a-production-database"></a>Создание рабочей базы данных

В командной строке `postgres` создайте базу данных.

```sql
CREATE DATABASE sampledb;
```

### <a name="create-a-user-with-permissions"></a>Создание пользователя с разрешениями

Создайте пользователя базы данных с именем _railsappuser_ и предоставьте ему все привилегии в базе данных `sampledb`.

```sql
CREATE USER railsappuser WITH PASSWORD 'MyPostgresAzure2017'; 
GRANT ALL PRIVILEGES ON DATABASE sampledb TO railsappuser;
```

Завершите подключение к серверу, введя команду `\q`.

## <a name="connect-app-to-azure-postgres"></a>Подключение приложения к Azure Postgres

На этом шаге вы подключите приложение Ruby on Rails к базе данных Postgres, созданной в Базе данных Azure для PostgreSQL.

<a name="devconfig"></a>

### <a name="configure-the-database-connection"></a>Настройка подключения к базе данных

В репозитории откройте файл _config/database.yml_. В нижней части файла замените рабочие переменные следующим кодом. 

```txt
production:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: <%= ENV['DB_DATABASE'] %>
  username: <%= ENV['DB_USERNAME'] %>
  password: <%= ENV['DB_PASSWORD'] %>
```

Сохраните изменения.

### <a name="test-the-application-locally"></a>Локальное тестирование приложения

Вернитесь в окно терминала на локальном компьютере и установите следующие переменные среды:

```bash
export DB_HOST=<postgres_server_name>.postgres.database.azure.com
export DB_DATABASE=sampledb 
export DB_USERNAME=railsappuser@<postgres_server_name>
export DB_PASSWORD=MyPostgresAzure2017
```

Выполните перенос базы данных Rails с настроенными рабочими значениями для создания таблиц в базе данных Postgres Базы данных Azure для PostgreSQL. 

```bash
rake db:migrate RAILS_ENV=production
```

При использовании в рабочей среде приложению Rails нужны предкомпилированные ресурсы. Создайте необходимые ресурсы, выполнив следующую команду:

```bash
rake assets:precompile
```

В рабочей среде Rails также используются секреты для управления безопасностью. Создайте секретный ключ.

```bash
rails secret
```

Сохраните секретный ключ в соответствующих переменных, используемых в рабочей среде Rails. Для удобства используйте один ключ для обеих переменных.

```bash
export RAILS_MASTER_KEY=<output_of_rails_secret>
export SECRET_KEY_BASE=<output_of_rails_secret>
```

Включите обработку файлов JavaScript и CSS в рабочей среде Rails.

```bash
export RAILS_SERVE_STATIC_FILES=true
```

Запустите пример приложения в рабочей среде.

```bash
rails server -e production
```

Перейдите на страницу `http://localhost:3000`. Если страница загрузилась без ошибок, это значит, что приложение Ruby on Rails подключается к базе данных Postgres в Azure.

Добавьте несколько задач на странице.

![Приложение Ruby on Rails подключилось к Базе данных Azure для PostgreSQL](./media/tutorial-ruby-postgres-app/azure-postgres-connect-success.png)

Чтобы остановить сервер Rails, введите `Ctrl + C` в окне терминала.

### <a name="commit-your-changes"></a>Фиксация изменений

Чтобы зафиксировать изменения, выполните следующие команды Git:

```bash
git add .
git commit -m "database.yml updates"
```

Ваше приложение готово к развертыванию.

## <a name="deploy-to-azure"></a>Развернуть в Azure

На этом этапе вы развернете приложение Rails, подключенное к базе данных Postgres, в Службе приложений Azure.

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan no h](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-ruby-linux-no-h.md)] 

### <a name="configure-database-settings"></a>Настройка параметров базы данных

В службе приложений переменные среды задаются в качестве _параметров приложения_ с помощью команды [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell.

Команда Cloud Shell ниже позволяет настроить параметры приложения `DB_HOST`, `DB_DATABASE`, `DB_USERNAME` и `DB_PASSWORD`. Замените заполнители _&lt;имя_приложения>_ и _&lt;имя_сервера_postgres>_.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings DB_HOST="<postgres_server_name>.postgres.database.azure.com" DB_DATABASE="sampledb" DB_USERNAME="railsappuser@<postgres_server_name>" DB_PASSWORD="MyPostgresAzure2017"
```

### <a name="configure-rails-environment-variables"></a>Настройка переменных среды Rails

В окне терминала на локальном компьютере создайте секретный ключ для рабочей среды Rails в Azure.

```bash
rails secret
```

Настройте переменные, необходимые для рабочей среды Rails.

В следующей команде Cloud Shell замените два заполнителя _&lt;output_of_rails_secret>_ новым секретным ключом, созданным в окне терминала на локальном компьютере.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings RAILS_MASTER_KEY="<output_of_rails_secret>" SECRET_KEY_BASE="<output_of_rails_secret>" RAILS_SERVE_STATIC_FILES="true" ASSETS_PRECOMPILE="true"
```

Свойство со значением `ASSETS_PRECOMPILE="true"` указывает контейнеру Ruby по умолчанию предварительно компилировать ресурсы во время каждого развертывания Git.

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

В окне терминала на локальном компьютере добавьте удаленное приложение Azure в локальный репозиторий Git.

```bash
git remote add azure <paste_copied_url_here>
```

Выполните публикацию в удаленную службу приложений Azure, чтобы развернуть приложение Ruby on Rails. После этого введите пароль, указанный ранее в процессе создания пользователя развертывания.

```bash
git push azure master
```

Во время развертывания служба приложений Azure будет взаимодействовать с Git.

```bash
Counting objects: 3, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 291 bytes | 0 bytes/s, done.
Total 3 (delta 2), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'a5e076db9c'.
remote: Running custom deployment command...
remote: Running deployment command...
...
< Output has been truncated for readability >
```

### <a name="browse-to-the-azure-web-app"></a>Переход к веб-приложению Azure

Перейдите по адресу `http://<app_name>.azurewebsites.net` и добавьте несколько задач в список.

![Приложение Ruby on Rails, работающее в службе приложений Azure](./media/tutorial-ruby-postgres-app/ruby-postgres-in-azure.png)

Вы запустили управляемое данными приложение Ruby on Rails в службе приложений Azure.

## <a name="update-model-locally-and-redeploy"></a>Локальное обновление и повторное развертывание модели

На этом шаге вы внесете некоторые изменения в модель данных `task` и веб-приложение, а затем опубликуете обновления в Azure.

Для сценария с задачами необходимо изменить приложение, чтобы можно было пометить задачу как выполненную.

### <a name="add-a-column"></a>Добавление столбца

В окне терминала перейдите к корневой папке репозитория Git.

Создайте миграцию, при которой столбец логических значений `Done` добавляется в таблицу `Tasks`:

```bash
rails generate migration AddDoneToTasks Done:boolean
```

Эта команда создает файл миграции в каталоге _db/migrate_.


В окне терминала выполните перенос базы данных Rails, чтобы внести изменения в локальную базу данных.

```bash
rake db:migrate
```

### <a name="update-application-logic"></a>Обновление логики приложения

Откройте файл *app/controllers/tasks_controller.rb*. В конце файла найдите следующую строку:

```rb
params.require(:task).permit(:Description)
```

Измените эту строку, добавив в нее новый параметр `Done`.

```rb
params.require(:task).permit(:Description, :Done)
```

### <a name="update-the-views"></a>Обновление представлений

Откройте файл *app/views/tasks/_form.html.erb*, который является формой редактирования.

Найдите строку `<%=f.error_span(:Description) %>` и вставьте непосредственно под ней следующий код:

```erb
<%= f.label :Done, :class => 'control-label col-lg-2' %>
<div class="col-lg-10">
  <%= f.check_box :Done, :class => 'form-control' %>
</div>
```

Откройте файл *app/views/tasks/show.html.erb*, который является страницей представления с одной записью. 

Найдите строку `<dd><%= @task.Description %></dd>` и вставьте непосредственно под ней следующий код:

```erb
  <dt><strong><%= model_class.human_attribute_name(:Done) %>:</strong></dt>
  <dd><%= check_box "task", "Done", {:checked => @task.Done, :disabled => true}%></dd>
```

Откройте файл *app/views/tasks/index.html.erb*, который является страницей индекса для всех записей.

Найдите строку `<th><%= model_class.human_attribute_name(:Description) %></th>` и вставьте непосредственно под ней следующий код:

```erb
<th><%= model_class.human_attribute_name(:Done) %></th>
```

В этом же файле найдите строку `<td><%= task.Description %></td>` и вставьте непосредственно под ней следующий код:

```erb
<td><%= check_box "task", "Done", {:checked => task.Done, :disabled => true} %></td>
```

### <a name="test-the-changes-locally"></a>Локальная проверка изменений

В окне терминала на локальном компьютере запустите сервер Rails.

```bash
rails server
```

Чтобы увидеть, как изменится состояние задачи, перейдите по адресу `http://localhost:3000` и добавьте или измените элемент.

![Добавлен флажок для задачи](./media/tutorial-ruby-postgres-app/complete-checkbox.png)

Чтобы остановить сервер Rails, введите `Ctrl + C` в окне терминала.

### <a name="publish-changes-to-azure"></a>Публикация изменений в Azure

В окне терминала выполните перенос базы данных Rails для рабочей среды, чтобы внести изменения в базу данных Azure.

```bash
rake db:migrate RAILS_ENV=production
```

Зафиксируйте все изменения в Git, а затем отправьте изменения кода в Azure.

```bash
git add .
git commit -m "added complete checkbox"
git push azure master
```

После выполнения команды `git push` перейдите в веб-приложение Azure и еще раз проверьте новые функции.

![Изменения модели и базы данных, опубликованные в Azure](media/tutorial-ruby-postgres-app/complete-checkbox-published.png)

Добавленные задачи сохраняются в базе данных. Изменения в схеме данных не влияют на имеющиеся данные.

## <a name="manage-the-azure-web-app"></a>Управление веб-приложением Azure

Перейдите на [портал Azure](https://portal.azure.com) для управления созданным веб-приложением.

В меню слева выберите **Службы приложений**, а затем щелкните имя своего веб-приложения Azure.

![Переход к веб-приложению Azure на портале](./media/tutorial-php-mysql-app/access-portal.png)

Отобразится страница обзора вашего веб-приложения. Вы можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление.

В меню слева доступны страницы для настройки приложения.

![Страница службы приложений на портале Azure](./media/tutorial-php-mysql-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание базы данных Postgres в Azure
> * Подключение приложения Ruby on Rails к Postgres
> * Развертывание приложения в Azure
> * Обновление модели данных и повторное развертывание приложения.
> * Потоковая передача журналов диагностики из Azure.
> * Управление приложением на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с веб-приложением.

> [!div class="nextstepaction"]
> [Сопоставление существующего настраиваемого DNS-имени с веб-приложениями Azure](../app-service-web-tutorial-custom-domain.md)
