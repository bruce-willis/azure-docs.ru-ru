---
title: Создание веб-приложения Node.js для Azure Cosmos DB | Документы Майкрософт
description: В этом руководстве по Node.js описывается использование Microsoft Azure Cosmos DB для хранения данных и доступа к ним из веб-приложения Node.js Express, размещенного на веб-сайтах Azure.
keywords: Application development, database tutorial, learn Node.js, Node.js tutorial
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 07/31/2018
ms.author: sngun
ms.openlocfilehash: 9f7744c7743107b3587bd63a6e7681a6f1e42c00
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39608976"
---
# <a name="_Toc395783175"></a>Создание веб-приложения Node.js с помощью Azure Cosmos DB и пакета SDK для Node.js (предварительная версия)
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Node.js — предварительная версия 2.0](sql-api-nodejs-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

В этом руководстве по Node.js показано, как использовать учетную запись API SQL Azure Cosmos DB для хранения данных и обеспечения доступа к ним из приложения Node.js Express, размещенного на веб-сайтах Azure. В этом руководстве вы создадите простое веб-приложение (приложение со списком задач), которое позволит вам создавать, извлекать и выполнять задачи. Задачи будут храниться в виде документов JSON в Azure Cosmos DB. Ниже приведен снимок экрана приложения со списком задач:

![Снимок экрана приложения "Мой список дел", созданного с помощью этого учебника](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

В этом руководстве показано, как создать учетную запись API SQL Azure Cosmos DB с помощью портала Azure. Затем вы создадите и запустите веб-приложение, созданное на основе пакета SDK для Node.js для создания базы данных, контейнера и добавления элементов в контейнер. В этом руководстве используется пакет SDK для Node.js версии 2.0, который в настоящее время доступен в режиме предварительной версии.

Полный пример также можно получить на сайте [GitHub][GitHub]. Инструкции по запуску приложения см. в [файле сведений](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

## <a name="_Toc395783176"></a>Предварительные требования

Перед выполнением инструкций, приведенных в этой статье, следует убедиться, что установлены следующие компоненты:

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] версии 6.10 или выше.
* [Генератор Express](http://www.expressjs.com/starter/generator.html) (его можно установить через `npm install express-generator -g`).
* [Git][Git].

## <a name="_Toc395637761"></a>Шаг 1. Создание учетной записи базы данных Azure Cosmos DB
Давайте сначала создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись или вы используете эмулятор Azure Cosmos DB в этом руководстве, можно перейти к разделу [Шаг 2. Создание приложения Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Шаг 2. Создание приложения Node.js
Теперь создадим базовый проект Node.js «Привет, мир!» с помощью платформы [Express](http://expressjs.com/) .

1. Откройте предпочитаемый терминал, например командную строку Node.js.
2. Перейдите в каталог, в котором будет сохранено новое приложение.
3. С помощью генератора Express создайте новое приложение с именем **todo**.

   ```bash
   express todo
   ```
4. Откройте новый каталог **todo** и установите зависимости.

   ```bash
   cd todo
   npm install
   ```
5. Запустите новое приложение.

   ```bash
   npm start
   ```

6. Новое приложение можно просмотреть, перейдя в браузере по адресу [http://localhost:3000](http://localhost:3000).
   
    ![Изучение Node.js — снимок экрана приложения "Привет, мир" в окне браузера](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Остановите приложение, нажав клавиши CTRL+C в окне терминала, а затем нажмите клавишу **Y**, чтобы завершить пакетное задание.

## <a name="_Toc395783179"></a>Шаг 3. Установка необходимых модулей

Файл **package.json** является одним из файлов, создаваемых в корневой папке проекта. Этот файл содержит список дополнительных модулей, необходимых для приложения Node.js. Позднее, при развертывании этого приложения на веб-сайтах Azure, этот файл будет использоваться для определения модулей, которые должны быть установлены в Azure для поддержки вашего приложения. Для работы с этим руководством нужно установить еще два пакета.

1. Откройте терминал и установите модуль **async** с помощью npm.

   ```bash
   npm install async --save
   ```

2. Установите модуль **@azure/cosmos** с помощью npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Шаг 4. Использование службы Azure Cosmos DB в приложении Node
Теперь, когда вы завершили первоначальную настройку и установку, нужно написать код, который требуется приложению со списком задач для обмена данными с Azure Cosmos DB.

### <a name="create-the-model"></a>Создание модели
1. В корне каталога проекта создайте каталог с именем **models**.  

2. В каталоге **models** создайте новый файл с именем **taskDao.js**. Этот файл содержит код, необходимый для создания базы данных и контейнера, и определяет методы чтения, обновления, создания и поиска задач в Azure Cosmos DB. 

3. Скопируйте следующий код в файл **taskDao.js**.

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Сохраните и закройте файл **taskDao.js** .  

### <a name="create-the-controller"></a>Создание контроллера

1. В каталоге проекта **routes** создайте новый файл с именем **tasklist.js**.  

2. Добавьте в **tasklist.js**следующий код. Он загружает модули CosmosClient и async, используемые файлом **tasklist.js**. Он также определяет класс **TaskList**, который передается как экземпляр определенного ранее объекта **TaskDao**:
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Сохраните и закройте файл **tasklist.js** .

### <a name="add-configjs"></a>Добавление config.js

1. В корне каталога проекта создайте файл с именем **config.js**. 

2. Добавьте следующий код в файл **config.js**. Он определяет значения и параметры конфигурации, необходимые нашему приложению.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. В файле **config.js** замените значения HOST и AUTH_KEY значениями, найденными на странице "Ключи" учетной записи Azure Cosmos DB на [портале Microsoft Azure](https://portal.azure.com). 

4. Сохраните и закройте файл **config.js** .

### <a name="modify-appjs"></a>Изменение app.js
1. В каталоге проекта откройте файл **app.js** . Этот файл был создан ранее, при создании веб-приложения Express.  

2. Добавьте следующий код в файл **app.js**. Этот код определяет используемый файл конфигурации, продолжая считывать из него значения в переменные, которыми мы вскоре воспользуемся. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Наконец, сохраните и закройте файл **app.js**. Мы почти закончили.

## <a name="_Toc395783181"></a>Шаг 5. Построение пользовательского интерфейса
Теперь давайте обратим наше внимание на создание пользовательского интерфейса, чтобы пользователь смог взаимодействовать с нашим приложением. Созданное нами приложение Express использует в качестве обработчика представлений **Jade**. Дополнительные сведения о Jade см. на сайте [http://jade-lang.com/](http://jade-lang.com/).

1. Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других файлов **.jade**. На этом шаге он будет изменен для использования [Twitter Bootstrap](https://github.com/twbs/bootstrap)— набора средств, упрощающих разработку привлекательного веб-сайта.  

2. Откройте файл **layout.jade**, расположенный в папке **views**, и замените его содержимое следующим.

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Он позволяет эффективно сообщить подсистеме **Jade** о том, что необходимо выполнить прорисовку HTML-кода для нашего приложения, и создает **block** с именем **content**, где можно указать разметку для страниц содержимого.

    Сохраните и закройте файл **layout.jade** .

3. Теперь откройте файл **index.jade** — представление, которое будет использоваться нашим приложением, — и замените содержимое файла следующим:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
       form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
   ```

Этот код дополняет макет и предоставляет содержимое для заполнителя **content**, который мы ранее видели в файле **layout.jade**.
   
В макете мы создали две формы HTML.

Первая форма содержит таблицу для наших данных и кнопку, позволяющую обновлять элементы путем отправки POST-запроса к методу **/completeTask** нашего контроллера.
    
Вторая форма содержит два поля ввода и кнопку, позволяющую создать новый элемент путем POST-запроса к методу **/addtask** нашего контроллера.

Этого должно быть достаточно для работы нашего приложения.

## <a name="_Toc395783181"></a>Шаг 6. Локальный запуск приложения
1. Чтобы протестировать приложение на локальном компьютере, выполните `npm start` в терминале для запуска приложения, а затем обновите в браузере страницу [http://localhost:3000](http://localhost:3000). Страница должна выглядеть так, как на рисунке ниже:
   
    ![Снимок экрана приложения «Мой список дел» в окне браузера](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Если появляется сообщение об ошибке отступа в файле layout.jade или index.jade, выровняйте две первые строки в этих двух файлах по левому краю без пробелов. Если перед первыми двумя строками есть пробелы, удалите их, сохраните оба файла, а затем обновите окно браузера. 

2. Используйте поля "Элемент", "Имя элемента" и "Категория", чтобы указать новую задачу, а затем щелкните **Добавить элемент**. Будет создан документ в Azure Cosmos DB с заданными свойствами. 
3. Страница должна обновиться, чтобы отобразить только что созданный элемент в списке ToDo.
   
    ![Снимок экрана приложения с новым элементом в списке дел](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Чтобы завершить задачу, просто установите флажок в столбце "Завершено" и нажмите кнопку **Обновить задачи**. После этого созданный документ будет обновлен и удален из представления.

5. Чтобы остановить приложение, нажмите клавиши CTRL + C в окне терминала и нажмите клавишу **Y** для завершения пакетного задания.

## <a name="_Toc395783182"></a>Шаг 7. Развертывание проекта приложения на веб-сайтах Azure
1. Если это еще не сделано, включите репозиторий git для веб-сайта Azure. Соответствующие инструкции см. в статье [Развертывание локального репозитория Git в службе приложений Azure](../app-service/app-service-deploy-local-git.md).
2. Добавьте веб-сайт Azure в качестве удаленного репозитория git.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Выполните развертывание методом Push на удаленный репозиторий.
   
        git push azure master
4. Через несколько секунд Visual Studio завершит публикацию вашего веб-приложения и запустит браузер, где вы увидите свое творение, запущенное в Azure!

    Поздравляем! Вы только что создали свое первое веб-приложение Node.js Express с использованием Azure Cosmos DB и опубликовали его на веб-сайтах Azure.

    Если вы хотите скачать полный пример приложения или сослаться на него во время работы с этим руководством, файл примера приложения находится в репозитории [GitHub][GitHub].

## <a name="_Toc395637775"></a>Дальнейшие действия

* Хотите выполнять проверку масштабирования и производительности с помощью Azure Cosmos DB? Дополнительные сведения см. в статье о [проверке производительности и масштабирования с помощью Azure Cosmos DB](performance-testing.md).
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).
* Изучите [документацию по базе данных Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

