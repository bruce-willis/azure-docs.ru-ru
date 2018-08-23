---
title: Azure Cosmos DB. Создание приложения с помощью Node.js и API SQL | Документация Майкрософт
description: В этой статье представлен пример кода Node.js, который можно использовать для подключения и выполнения запросов к API SQL в Azure Cosmos DB.
services: cosmos-db
author: deborahc
manager: andrl
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 07/30/2018
ms.author: dech
ms.openlocfilehash: 23b7c5d332bd5fb75936c95ab9e38d17e1573999
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42022722"
---
# <a name="azure-cosmos-db-build-a-nodejs-app-using-sql-api-javascript-sdk-20-preview-and-the-azure-portal"></a>Azure Cosmos DB: создание приложения Node.js с помощью API SQL, пакета SDK для JavaScript версии 2.0 (предварительная версия) и портала Azure 

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ — значение" и базы данных графов, используя преимущества возможностей глобального распределения и горизонтального масштабирования базы данных Azure Cosmos DB. 

В этом кратком руководстве показано, как создать учетную запись [API SQL для Azure Cosmos DB](sql-api-introduction.md), базу данных документов и контейнер с помощью портала Azure. Затем вы создадите консольное приложение с помощью [пакета SDK для SQL на JavaScript](sql-api-sdk-node.md) и запустите его. В этом кратком руководстве используется [пакет SDK для JavaScript](https://www.npmjs.com/package/@azure/cosmos) 2.0, который в настоящее время доступен в предварительной версии. 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Кроме того, сделайте следующее:
    * [Node.js](https://nodejs.org/en/) версии 6.0.0 или более поздней.
    * [Git](http://git-scm.com/)

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Добавление коллекции

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Добавление демонстрационных данных

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Обращение к данным

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение API SQL из GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Просмотр кода

Этот шаг не является обязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string). 

Обратите внимание, что если вам знакома предыдущая версия пакета SDK для JavaScript, вы уже видели термины "коллекция" и "документ". Так как Azure Cosmos DB поддерживает [несколько моделей API](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities), пакет SDK для JavaScript версии 2.0+ использует общий термин "контейнер", который может быть коллекцией, графом или таблицей, и термин "элемент" для описания содержимого контейнера.

Приведенные ниже фрагменты кода взяты из файла **app.js**.

* Инициализация `CosmosClient`.

    ```nodejs
    const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
    ```

* Создание базы данных.

    ```nodejs
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* Создается контейнер (коллекция).

    ```nodejs
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Создается элемент (документ).

    ```nodejs
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Выполнение запроса SQL через JSON.

    ```nodejs
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
    ```    

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. На [портале Azure](http://portal.azure.com/) перейдите к учетной записи базы данных Azure Cosmos DB и на левой панели навигации щелкните **Ключи**, а затем выберите **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать универсальный код ресурса (URI) и первичный ключ в файл `config.js`.

    ![Просмотр и копирование ключа доступа на портале Azure, колонка "Ключи"](./media/create-sql-api-dotnet/keys.png)

2. Откройте файл `config.js`. 

3. Скопируйте значение универсального кода ресурса (URI) на портале (с помощью кнопки копирования) и добавьте его в качестве значения параметра ключа конечной точки в файле `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Затем скопируйте значение первичного ключа с портала и добавьте его в качестве значения параметра `config.primaryKey` в файле `config.js`. Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. 

    `config.primaryKey = "FILLME"`
    
## <a name="run-the-app"></a>Запуск приложения
1. В окне терминала запустите `npm install`, чтобы установить необходимые модули npm.

2. Запустите `node app.js` в окне терминала, чтобы запустить приложение Node.

Вернитесь в обозреватель данных, где вы можете просматривать, запрашивать и изменять новые данные, а также работать с ними. 

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как создать учетную запись Azure Cosmos DB, коллекцию с помощью обозревателя данных, а также как запустить приложение. Теперь можно импортировать дополнительные данные в учетную запись Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных в DocumentDB с помощью средства миграции базы данных](import-data.md)


