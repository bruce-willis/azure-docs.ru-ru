---
title: Создание приложения Xamarin.Forms с использованием .NET и API MongoDB в Azure Cosmos DB | Документы Майкрософт
description: В этой статье представлен пример кода Xamarin, который можно использовать для подключения к API MongoDB в Azure Cosmos DB и выполнения запросов
services: cosmos-db
author: codemillmatt
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart, xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: e002f99cc16721b78d85284105ce22ee7d1eb9a3
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574833"
---
# <a name="quickstart-build-a-mongodb-api-xamarinforms-app-with-net-and-the-azure-portal"></a>Краткое руководство. Создание приложения Xamarin.Forms API MongoDB с использованием .NET и портала Azure

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB.

В этом кратком руководстве показано, как c помощью портала Azure создать учетную запись [API MongoDB](mongodb-introduction.md) для Azure Cosmos DB, базу данных документов и коллекцию. Вам предстоит создать приложение Xamarin.Forms со списком дел с помощью [драйвера MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Необходимые компоненты для запуска примера приложения

Чтобы запустить пример, вам потребуется [Visual Studio](https://www.visualstudio.com/downloads/) или [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и действительная учетная запись Azure CosmosDB.

Если вы еще не установили Visual Studio, скачайте [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) с установленной и настроенной рабочей нагрузкой **Разработка мобильных приложений на .NET**.

Если вы предпочитаете работать на компьютере Mac, скачайте [Visual Studio для Mac](https://visualstudio.microsoft.com/vs/mac/) и запустите программу установки.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Описанный в этой статье пример приложения совместим с MongoDB.Driver версии 2.6.1.

## <a name="clone-the-sample-app"></a>Клонирования примера приложения

Сначала скачайте пример приложения API MongoDB из репозитория GitHub. В нем реализуется приложение со списком дел с моделью хранения документов MongoDB.

1. Откройте командную строку, создайте папку git-samples, а затем закройте окно командной строки.

    ```bash
    md "C:\git-samples"
    ```

2. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в новую папку для установки примера приложения.

    ```bash
    cd "C:\git-samples"
    ```

3. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Если вы не хотите использовать Git, можно [скачать проект в виде ZIP-файла](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)

## <a name="review-the-code"></a>Просмотр кода

Этот шаг не является обязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string).

Следующие фрагменты кода взяты из класса `MongoService`, расположенного по следующему пути: src/TaskList.Core/Services/MongoService.cs.

* Инициализация клиента Mongo.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Получение ссылки на базу данных и коллекцию. Пакет .NET SDK для MongoDB автоматически создаст базу данных и коллекцию, если их не существует.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Получение всех документов в виде списка.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Запрос конкретных документов.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Создание задачи и ее вставка в коллекцию MongoDB.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Изменение задачи в коллекции MongoDB.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Удаление задачи из коллекции MongoDB.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. На [портале Azure](http://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации щелкните **Строка подключения**, а затем выберите **Ключи записи-чтения**. В следующих шагах используйте кнопки копирования в правой части экрана, чтобы скопировать основную строку подключения.

2. Откройте файл **APIKeys.cs** в каталоге **Helpers** проекта **TaskList.Core**.

3. Скопируйте **основную строку подключения** на портале (с помощью кнопки копирования) и используйте ее в качестве значения поля **ConnectionString** в файле **APIKeys.cs**.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено.

## <a name="run-the-app"></a>Запуск приложения

### <a name="visual-studio-2017"></a>Visual Studio 2017

1. В Visual Studio в **обозревателе решений** щелкните каждый проект правой кнопкой мыши и выберите **Управление пакетами NuGet**.
2. Щелкните **Восстановить все пакеты NuGet**.
3. Щелкните правой кнопкой мыши **TaskList.Android** и выберите **Назначить запускаемым проектом**.
4. Нажмите клавишу F5 для запуска отладки приложения.
5. Если вы хотите запустить приложение на устройстве iOS, сначала необходимо подключиться к компьютеру Mac (для этого [выполните следующие действия](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)).
6. Щелкните проект правой кнопкой мыши **TaskList.iOS** и выберите **Назначить запускаемым проектом**.
7. Нажмите клавишу F5 для запуска отладки приложения.

### <a name="visual-studio-for-mac"></a>Visual Studio для Mac

1. В раскрывающемся списке платформ выберите TaskList.iOS или TaskList.Android, в зависимости от платформы, на которой необходимо запустить приложение.
2. Нажмите сочетание клавиш CMD+ВВОД для запуска отладки приложения.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как создать учетную запись Azure Cosmos DB и запустить приложение Xamarin.Forms, используя API MongoDB. Теперь можно импортировать дополнительные данные в учетную запись Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Перенос данных в DocumentDB с помощью mongoimport и mongorestore](mongodb-migrate.md)