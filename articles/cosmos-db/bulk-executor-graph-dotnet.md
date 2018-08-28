---
title: Использование библиотеки BulkExecutor .NET для выполнения массовых операций с графами в API Gremlin в Azure Cosmos DB
description: Узнайте, как использовать библиотеку BulkExecutor, чтобы массово импортировать данные графа в контейнер API Gremlin в Azure Cosmos DB.
services: cosmos-db
keywords: graph, gremlin, bulk, bulkexecutor, migration, data, cosmosdb, cosmos, database, import
author: luisbosquez
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 39abf6d6da8a8035cf486ceb30b9c21186bbb925
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234425"
---
# <a name="using-the-graph-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Использование библиотеки BulkExecutor .NET для выполнения массовых операций с графами в API Gremlin в Azure Cosmos DB

В этом руководстве приведены инструкции по использованию библиотеки BulkExecutor .NET в Azure Cosmos DB для импорта и обновления объектов графа в контейнерах API Gremlin в Azure Cosmos DB. В этом процессе используется класс Graph в [библиотеке BulkExecutor](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview), чтобы программными средствами создавать объекты Vertex (вершина) и Edge (ребро), а затем вставлять несколько из них в один сетевой запрос. Такое поведение можно настроить в библиотеке BulkExecutor, чтобы оптимизировать использование как базы данных, так и локальной памяти.

В отличие от отправки Gremlin-запросов в базу данных, когда команды оцениваются и выполняются поочередно, при использовании библиотеки BulkExecutor объекты создаются и проверяются локально. После создания объектов можно последовательно отправлять объекты графа в службу базы данных. Этот метод позволяет повысить скорость приема данных практически в 100 раз, что очень полезно при первоначальном переносе или периодическом перемещении данных. [Пример приложения с использованием библиотеки BulkExecutor для работы с графами в Azure Cosmos DB ](https://aka.ms/graph-bulkexecutor-sample) доступен в GitHub.

## <a name="bulk-operations-with-graph-data"></a>Массовые операции с данными графа

[Библиотека BulkExecutor](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) содержит пространство имен `Microsoft.Azure.CosmosDB.BulkExecutor.Graph` для создания и импорта объектов графа. 

Ниже описан процесс переноса данных для контейнера API Gremlin:
1. Извлеките записи из источника данных.
2. Создайте объекты `GremlinVertex` и `GremlinEdge` на основе полученных записей и добавьте объекты в структуру данных `IEnumerable`. В этой части приложения необходимо реализовать логику распознавания и добавления связей на случай, если источник данных не является базой данных графа.
3. Вставьте объекты графа в коллекцию с помощью [метода Graph BulkImportAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet).

Такой механизм повышает эффективность переноса данных по сравнению с использованием Gremlin-клиента. При вставке данных c помощью Gremlin приложение отправляет единоразовый запрос на создание данных, который нужно проверить, оценить и затем выполнить. Если используется библиотека BulkExecutor, проверка выполняется в приложении и для каждого сетевого запроса отправляется несколько объектов графа за раз.

### <a name="creating-vertices-and-edges"></a>Создание ребер и вершин

`GraphBulkExecutor` предоставляет метод `BulkImportAsync`, которому необходим список `IEnumerable` объектов `GremlinVertex` или `GremlinEdge`, определенных в пространстве имен `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`. В приведенном примере мы разделили создание ребер и вершин на две отдельные задачи импорта в BulkExecutor. См. пример ниже.

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Дополнительные сведения о параметрах библиотеки BulkExecutor см. в разделе [Массовый импорт данных в Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#bulk-import-data-to-azure-cosmos-db).

Необходимо создать экземпляры полезных данных в объектах `GremlinVertex` и `GremlinEdge`. Ниже показан способ их создания:

**Вершины**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Ребра**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> BulkExecutor не выполняет автоматическую проверку существующих вершин, пока не будут добавлены ребра. Перед выполнением задач BulkImport нужно проверить вершины в приложении.

## <a name="sample-application"></a>Пример приложения

### <a name="prerequisites"></a>Предварительные требования
* Visual Studio 2017 с рабочей нагрузкой разработки Azure. Вы можете бесплатно начать работу в [выпуске Visual Studio 2017 Community](https://visualstudio.microsoft.com/downloads/).
* Подписка Azure. Вы можете создать [бесплатную учетную запись Azure здесь](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Кроме того, можно создать учетную запись базы данных Cosmos DB в [бесплатной пробной версии Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) без подписки Azure.
* База данных API Gremlin в Azure Cosmos DB с **неограниченной коллекцией**. В этом руководстве объясняется, как начать работу с [Gremlin API в Azure Cosmos DB в .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Дополнительные сведения см. на [странице скачивания Git](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Клонирование примера приложения
Здесь приведены пошаговые инструкции по началу работы [на примере библиотеки BulkExecutor для работы с графами в Azure Cosmos DB](https://aka.ms/graph-bulkexecutor-sample), размещенном в GitHub. Приложение представляет собой .NET-решение, которое случайным образом создает объекты вершины и ребра, а затем выполняет массовую вставку данных в определенную учетную запись базы данных графа. Чтобы запустить приложение, выполните следующую команду `git clone`:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Этот репозиторий содержит пример GraphBulkExecutor со следующими файлами:

Файл|ОПИСАНИЕ
---|---
`App.config`|Здесь указаны параметры, относящиеся к приложению и базе данных. Сначала нужно изменить файл, чтобы подключить целевую базу данных и коллекции.
`Program.cs`| Файл содержит логику создания коллекции `DocumentClient`, управления очистками и отправки запросов BulkExecutor.
`Util.cs`| Файл содержит вспомогательный класс с логикой генерации тестовых данных, а также проверки наличия базы данных и коллекции.

В файле `App.config` приведены возможные значения конфигурации:

Параметр|ОПИСАНИЕ
---|---
`EndPointUrl`|**Конечная точка .NET SDK**, указанная в колонке "Обзор" вашей учетной записи в базе данных API Gremlin в Azure Cosmos DB в следующем формате: `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Первичный или вторичный ключ из вашей учетной записи Azure Cosmos DB. Дополнительные сведения см. в статье [Защита доступа к данным Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys).
`DatabaseName`, `CollectionName`|**Названия целевой базы данных и коллекции**. Если `ShouldCleanupOnStart` присвоено значение `true`, эти значения, наряду с `CollectionThroughput`, используются для очистки и создания новой базы данных и коллекции. Аналогично, если `ShouldCleanupOnFinish` присвоено значение `true`, база данных будет удалена по окончании приема данных. Обратите внимание: целевая коллекция должна быть **неограниченной**.
`CollectionThroughput`|Применяется для создания новой коллекции, если параметру `ShouldCleanupOnStart` присвоено значение `true`.
`ShouldCleanupOnStart`|Сбрасывает учетную запись базы данных и коллекции до запуска программы, а затем создает новые со значениями `DatabaseName`, `CollectionName` и `CollectionThroughput`.
`ShouldCleanupOnFinish`|Сбрасывает учетную запись базы данных и коллекции с определенными значениями `DatabaseName` и `CollectionName` после запуска программы.
`NumberOfDocumentsToImport`|Определяет количество тестовых вершин и ребер, которые будут созданы в примере. Это количество относится и к вершинам, и к ребрам.
`NumberOfBatches`|Определяет количество тестовых вершин и ребер, которые будут созданы в примере. Это количество относится и к вершинам, и к ребрам.
`CollectionPartitionKey`|Применяется для создания тестовых вершин и ребер, если это свойство присвоено автоматически. Также используется при повторном создании базы данных и коллекций, если `ShouldCleanupOnStart` присвоено значение `true`.

### <a name="run-the-sample-application"></a>Запуск примера приложения

1. Добавьте параметры конкретной базы данных в `App.config`, чтобы создать экземпляр DocumentClient. Если базы данных и контейнера еще нет, они будут созданы автоматически.
2. Запустите приложение. `BulkImportAsync` будет вызываться дважды: при первом вызове импортируются вершины, при втором — ребра. Если вставка каких-либо объектов приводит к ошибке, они будут добавлены в `.\BadVertices.txt` или `.\BadEdges.txt`.
3. Оцените результаты, отправив запрос в базу данных графа. Если `ShouldCleanupOnFinish` имеет значение true, база данных будет автоматически удалена.

## <a name="next-steps"></a>Дополнительная информация
* Дополнительные сведения о пакете Nuget и заметки о выпуске библиотеки .NET массового исполнителя см. в статье [о пакете SDK массового исполнителя](sql-api-sdk-bulk-executor-dot-net.md). 
* Ознакомьтесь с [советами по повышению производительности](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) при использовании библиотеки BulkExecutor.
* Прочитайте [справочную статью о BulkExecutor.Graph](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) с дополнительными сведениями о классах и методах, указанных в этом пространстве имен.
