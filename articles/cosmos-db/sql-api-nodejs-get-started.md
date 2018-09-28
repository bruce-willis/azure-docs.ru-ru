---
title: Руководство по Node.js для API SQL в Azure Cosmos DB | Документация Майкрософт
description: Руководство по Node.js, в котором описывается, как подключиться к Azure Cosmos DB и выполнить ее запрос с помощью API SQL
services: cosmos-db
author: deborahc
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: affa302c7fd2a0cb05a6d599050e72c75ef74479
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969065"
---
# <a name="nodejs-tutorial-create-a-nodejs-console-application-with-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Руководство по Node.js: создание консольного приложения Node.js с помощью пакета SDK для JavaScript версии 2.0 с целью управления данными API SQL для Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 


Добро пожаловать в руководство по Node.js по использованию пакета SDK для JavaScript Azure Cosmos DB. После изучения этого руководства у вас будет консольное приложение, которое создает ресурсы Azure Cosmos DB и отправляет запросы к ним.

Мы рассмотрим следующие вопросы:

* создание учетной записи Azure Cosmos DB и подключение к ней;
* Настройка приложения
* создание базы данных;
* создание контейнера;
* добавление элементов JSON в контейнер;
* выполнение запросов контейнера;
* замена элемента;
* удаление элемента;
* удаление базы данных.

У вас нет времени? Не беспокойтесь! Полное решение доступно на [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). Краткие инструкции см. в разделе [Получение полного решения для руководства по Node.js](#GetSolution).

После ознакомления с руководством по Node.js воспользуйтесь кнопками голосования в верхней или нижней части этой страницы, чтобы отправить нам отзыв. Если вы хотите, чтобы мы связались с вами, укажите ваш электронный адрес в комментариях.

А теперь приступим к работе!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Предварительные требования для работы с руководством по Node.js

Вам потребуются:

* Активная учетная запись Azure. Если у вас нет такой учетной записи, вы можете зарегистрироваться для использования [бесплатной пробной версии Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) версии 6.0.0 или более поздней.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Шаг 1. Создание учетной записи Azure Cosmos DB

Давайте создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись, которую вы собираетесь использовать, можно перейти к шагу [Настройка приложения Node.js](#SetupNode). Если вы используете эмулятор Azure Cosmos DB, выполните действия, описанные в статье об [эмуляторе Azure Cosmos DB](local-emulator.md), чтобы настроить эмулятор и сразу перейти к [настройке приложения Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Шаг 2. Настройка приложения Node.js

1. Откройте удобный для вас терминал.
2. Перейдите в папку или каталог, где вы хотите сохранить приложение Node.js.
3. Создайте два пустых файла JavaScript с помощью следующих команд:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux или OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Установите модуль @azure/cosmos с помощью npm. Используйте следующую команду:
   * ```npm install @azure/cosmos --save```

Отлично! Теперь, когда настройка завершена, можно начать писать код.

## <a id="Config"></a>Шаг 3. Настройка конфигурации приложения

Откройте файл ```config.js``` в предпочитаемом текстовом редакторе.

Скопируйте и вставьте приведенный ниже фрагмент кода, после чего задайте URI конечной точки и первичный ключ Azure Cosmos DB в качестве значений свойств ```config.endpoint``` и ```config.primaryKey```. Обе эти конфигурации можно найти на [портале Azure](https://portal.azure.com).

![Руководство по Node.js (база данных Node): снимок экрана портала Azure, на котором отображена учетная запись Azure Cosmos DB с выделенным активным концентратором, выделенной кнопкой "Ключи" в колонке учетной записи Azure Cosmos DB и выделенным универсальным кодом ресурса, первичным ключом и вторичным ключом в колонке "Ключи".][keys]

```nodejs
// ADD THIS PART TO YOUR CODE
var config = {}

config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
config.primaryKey = "~your primary key here~";
``` 

Скопируйте данные ```database```, ```container``` и ```items```, а затем вставьте их в объект ```config``` ниже, где задавались значения свойств ```config.endpoint``` и ```config.primaryKey```. Если у вас уже есть данные, которые вы хотите сохранить в своей базе данных, вы можете использовать [средство переноса данных](import-data.md) Azure Cosmos DB вместо определения здесь данных.

```nodejs
var config = {}

config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
config.primaryKey = "~your primary key here~";

config.database = {
    "id": "FamilyDatabase"
};

config.container = {
    "id": "FamilyContainer"
};

config.items = {
    "Andersen": {
        "id": "Anderson.1",
        "lastName": "Andersen",
        "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
    }
};
```
Обратите внимание, что если вам знакома предыдущая версия пакета SDK для JavaScript, вы уже видели термины "коллекция" и "документ". Так как Azure Cosmos DB поддерживает [несколько моделей API](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities), версия 2.0+ пакета SDK для JavaScript использует общие термины "контейнер" и "элемент". Контейнер может представлять собой коллекцию, граф или таблицу. Элемент может представлять собой документ, ребро, вершину или запись и является содержимым внутри контейнера. 

Теперь экспортируйте объект ```config```, чтобы на него можно было ссылаться в файле ```app.js```.
```nodejs
        },
        "isRegistered": false
    }
};

// ADD THIS PART TO YOUR CODE
module.exports = config;
```
## <a id="Connect"></a>Шаг 4. Подключение к учетной записи Azure Cosmos DB

Откройте пустой файл ```app.js``` в текстовом редакторе. Скопируйте и вставьте следующий код, чтобы импортировать модуль ```@azure/cosmos``` и созданный модуль ```config```.

```nodejs
// ADD THIS PART TO YOUR CODE
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');
```

Скопируйте и вставьте код, чтобы использовать ранее сохраненные значения ```config.endpoint``` и ```config.primaryKey``` и создать CosmosClient.

```nodejs
const url = require('url');

// ADD THIS PART TO YOUR CODE
const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
```

Теперь, когда у нас есть код для инициализации клиента Azure Cosmos DB, давайте рассмотрим принципы работы с ресурсами Azure Cosmos DB.

## <a name="step-5-create-a-database"></a>Шаг 5. Создание базы данных

Скопируйте и вставьте приведенный ниже код, чтобы задать значение Not Found для состояния HTTP, а также идентификатор базы данных и контейнера. По этим идентификаторам клиент Azure Cosmos DB найдет нужные базу данных и контейнер.

```nodejs
const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

// ADD THIS PART TO YOUR CODE
const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;
```

[Базу данных](sql-api-resources.md#databases) можно создать с помощью метода [createIfNotExists](/javascript/api/%40azure/cosmos/databases) или функции [create](/javascript/api/%40azure/cosmos/databases) класса **Databases**. База данных представляет собой логический контейнер элементов, разделенных между контейнерами. 

Скопируйте и вставьте функции **createDatabase** и **readDatabase** в файл app.js после определения идентификаторов ```databaseId``` и ```containerId```. Функция **createDatabase** создаст базу данных с идентификатором ```FamilyDatabase```, указанным из объекта ```config```, если она не была создана ранее. Функция **readDatabase** считает определение базы данных, чтобы удостовериться в ее наличии.

```nodejs
/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}
```

Скопируйте и вставьте приведенный ниже код, в котором вы задали функции **createDatabase** и **readDatabase**, для добавления функции вспомогательного приложения **exit**, которая выведет сообщение о выходе. 

```nodejs
// ADD THIS PART TO YOUR CODE
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
};
```
Скопируйте и вставьте приведенный ниже код, в котором вы задали функцию **exit**, для вызова функций **createDatabase** и **readDatabase**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
```

На этом этапе ваш код в ```app.js``` теперь должен выглядеть так, как показано ниже:

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;


/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 
```bash 
node app.js
```

Поздравляем! Вы успешно создали базу данных Azure Cosmos DB.

## <a id="CreateContainer"></a>Шаг 6. Создание контейнера

> [!WARNING]
> При вызове функции **createContainer** будет создан контейнер с ценовыми требованиями. Дополнительные сведения см. на [странице цен](https://azure.microsoft.com/pricing/details/cosmos-db/).

Контейнер можно создать с помощью метода [createIfNotExists](/javascript/api/%40azure/cosmos/containers) или функции [create](/javascript/api/%40azure/cosmos/containers) из класса **Containers**. 

Контейнер состоит из элементов (которые в случае API SQL являются документами JSON) и связанной логики приложения JavaScript.

Скопируйте и вставьте функции **createContainer** и **readContainer** после функции **readDatabase** в файле app.js. Функция **createContainer** создаст контейнер с идентификатором ```containerId```, указанным из объекта ```config```, если он не был создан ранее. Функция **readContainer** будет считывать определение контейнера, чтобы проверить наличие контейнера.

```nodejs
/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}
```

Скопируйте и вставьте код после вызова функции **readDatabase**, чтобы выполнить функции **createContainer** и **readContainer**.
```nodejs
createDatabase()
    .then(() => readDatabase())

    // ADD THIS PART TO YOUR CODE
    .then(() => createContainer())
    .then(() => readContainer())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

На этом этапе ваш код в ```app.js``` теперь должен выглядеть так, как показано ниже:

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

```bash 
node app.js
```

Поздравляем! Вы успешно создали контейнер Azure Cosmos DB.

## <a id="CreateItem"></a>Шаг 7. Создание элемента

Элемент можно создать с помощью функции [create](/javascript/api/%40azure/cosmos/items) класса **Items**. При использовании API SQL элементы проецируются как документы, которые являются содержимым JSON, определенным пользователем (произвольным). Теперь можно вставить элемент в Azure Cosmos DB.

Скопируйте и вставьте функцию **createFamilyItem** после функции **readContainer**. Функция **createFamilyItem** создает элементы, содержащие данные JSON, сохраненные в объекте ```config```. Перед созданием элемента нужно проверить, нет ли элемента с таким же идентификатором.

```nodejs
/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};
```

Скопируйте и вставьте код после вызова функции **readContainer**, чтобы выполнить функцию **createFamilyItem**.
```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())

    // ADD THIS PART TO YOUR CODE
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

```bash 
node app.js
```

Поздравляем! Вы успешно создали элемент Azure Cosmos DB.


## <a id="Query"></a>Шаг 8. Запрос ресурсов Azure Cosmos DB
Azure Cosmos DB поддерживает [полнофункциональные запросы](sql-api-sql-query.md) к документам JSON, хранящимся в каждом контейнере. В следующем примере кода показан выполняемый запрос к документам в контейнере.

Скопируйте и вставьте функцию **queryContainer** после функции **createFamilyItem** в файле app.js. Azure Cosmos DB поддерживает SQL-подобные запросы, как показано ниже. Дополнительные сведения о создании сложных запросов см. на [площадке для запросов](https://www.documentdb.com/sql/demo) и в [документации по запросам](sql-api-sql-query.md).

```nodejs
/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
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
};
```

Скопируйте и вставьте код после вызовов функции **createFamilyItem**, чтобы выполнить функцию **queryContainer**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryContainer())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

В окне терминала перейдите к файлу ```app.js``` и выполните эту команду:

```bash 
node app.js
```

Поздравляем! Вы успешно выполнили запрос к элементам Azure Cosmos DB.

## <a id="ReplaceItem"></a>Шаг 9. Замена элемента
Azure Cosmos DB поддерживает замену содержимого элементов.

Скопируйте и вставьте функцию **replaceFamilyItem** после функции **queryContainer** в файле app.js. Обратите внимание, что мы сменили значение свойства grade дочернего элемента с 5 на 6.

```nodejs
// ADD THIS PART TO YOUR CODE
/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};
```
Скопируйте и вставьте код после вызова функции **queryContainer**, чтобы выполнить функцию **replaceFamilyItem**. Кроме того, добавьте код, чтобы еще раз вызвать функцию **queryContainer** и проверить успешное изменение элемента.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```
В окне терминала перейдите к файлу ```app.js``` и выполните эту команду:

```bash 
node app.js
```

Поздравляем! Вы успешно заменили элемент Azure Cosmos DB.

## <a id="DeleteItem"></a>Шаг 10. Удаление элемента

Azure Cosmos DB поддерживает удаление элементов JSON.

Скопируйте функцию **deleteFamilyItem** и вставьте ее после функции **replaceFamilyItem**.

```nodejs
/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};
```

Скопируйте и вставьте код после вызова второй функции **queryContainer**, чтобы выполнить функцию **deleteFamilyItem**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer
    ())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

```bash 
node app.js
```

Поздравляем! Вы успешно удалили элемент Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Шаг 11. Удаление базы данных

Удаление созданной базы данных приведет к удалению базы данных и всех дочерних ресурсов (контейнеров, элементов и т. д.).

Скопируйте функцию **cleanup** и вставьте ее после функции **deleteFamilyItem**, чтобы удалить базу данных и все ее дочерние ресурсы.

```nodejs
/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}
```

Скопируйте код и вставьте его после вызова функции **deleteFamilyItem**, чтобы выполнить функцию **cleanup**.

```nodejs
createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

## <a id="Run"></a> Шаг 12. Запуск приложения Node.js

В целом, код должен выглядеть следующим образом:
```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
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
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

В окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

```bash 
node app.js
```

Должны отобразиться выходные данные вашего приложения. Выходные данные должны соответствовать примеру текста ниже.

    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit

Поздравляем! Вы ознакомились с руководством по Node.js и создали первое консольное приложение Azure Cosmos DB.

## <a id="GetSolution"></a>Получение полного решения для руководства по Node.js

Если вы не располагаете временем, чтобы выполнить шаги из этого руководства, или просто хотите скачать код, это можно сделать на портале [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Чтобы запустить решение по началу работы, которое содержит все коды из этой статьи, вам понадобится следующее:

* [Учетная запись Azure Cosmos DB][create-account]
* Решение для [начала работы](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), доступное на GitHub.

Установите модуль **@azure/cosmos** с помощью npm. Используйте следующую команду:

* ```npm install @azure/cosmos --save```

Затем в файле ```config.js``` обновите значения config.endpoint и config.primaryKey, как описано в разделе [Шаг 3. Настройка конфигурации приложения](#Config). 

Затем в окне терминала перейдите к файлу ```app.js``` и выполните эту команду: 

```bash 
node app.js
```

Теперь все готово. Можно начать работу с решением. 

## <a name="next-steps"></a>Дополнительная информация
* Требуется более сложный пример Node.js? См. статью о [создании веб-приложения Node.js с использованием Azure Cosmos DB](sql-api-nodejs-application.md).
* Узнайте, как выполнять [мониторинг учетной записи Azure Cosmos DB](monitor-accounts.md).
* Отправьте запросы образцу набора данных в [Площадке для запросов](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png