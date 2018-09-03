---
title: Перемещение данных из концентраторов событий Azure в хранилище данных SQL | Документация Майкрософт
description: В этом руководстве показано, как записать данные из концентратора событий в хранилище данных SQL с помощью функции Azure, активируемой службой "Сетка событий".
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
ms.author: shvija
ms.date: 08/27/2018
ms.topic: tutorial
ms.service: event-hubs
ms.openlocfilehash: 9673a7bff8e2d22764be28abef807434c53cc552
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43145162"
---
# <a name="process-and-migrate-captured-event-hubs-data-to-a-sql-data-warehouse-using-event-grid-and-azure-functions"></a>Обработка и перемещение записанных данных из концентраторов событий в хранилище данных SQL с помощью служб "Сетка событий" и "Функции Azure"

Функция [Сбор](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) в концентраторах событий Azure — это самый простой способ автоматически доставить данные потоковой передачи из концентраторов событий в хранилище BLOB-объектов Azure или Azure Data Lake Store. Вы можете последовательно обрабатывать и доставлять данные в любые другие назначения хранения по своему усмотрению, например в хранилище данных SQL или Cosmos DB. В этом руководстве показано, как записать данные из концентратора событий в хранилище данных SQL с помощью функции Azure, активируемой службой [Сетка событий](https://docs.microsoft.com/azure/event-grid/overview).

![Visual Studio](./media/store-captured-data-data-warehouse/EventGridIntegrationOverview.PNG)

*   Сначала необходимо создать концентратор событий с включенной функцией **Сбор** и задать хранилище BLOB-объектов Azure как место назначения. Данные, созданные WindTurbineGenerator, потоком передаются в концентратор событий и автоматически записываются в службу хранилища Azure как файлы AVRO. 
*   Далее нужно создать подписку "Сетка событий Azure" с пространством имен концентраторов событий в качестве источника и конечной точкой функции Azure в качестве целевого назначения.
*   Каждый раз, когда новый файл AVRO доставляется в большой двоичный объект в службе хранилища Azure с помощью функции "Сбор" концентраторов событий, служба "Сетка событий" уведомляет функцию Azure, предоставляя универсальный код ресурса (URI) большого двоичного объекта. Функция затем переносит данные из большого двоичного объекта в хранилище данных SQL.

Вот какие действия выполняются в этом руководстве: 

> [!div class="checklist"]
> * Развертывание инфраструктуры
> * Публикация кода в приложение-функцию.
> * Создание подписки "Сетка событий" из приложения-функции.
> * Потоковая передача примера данных в концентратор событий. 
> * Проверка собранных данных в хранилище данных SQL.

## <a name="prerequisites"></a>Предварительные требования

- [Visual Studio 2017 версии 15.3.2 или более поздней](https://www.visualstudio.com/vs/). Вместе с программой нужно установить следующие рабочие нагрузки: разработка классических приложений .NET, разработка Azure, разработка ASP.NET и веб-разработка, разработка Node.js и Python.
- Скачайте [пример Git](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo). Пример решения состоит из следующих компонентов:
    - *WindTurbineDataGenerator* — простой издатель, который отправляет образцы данных ветровой турбины в концентратор событий с поддержкой функции "Сбор".
    - *FunctionDWDumper* — функция Azure, которая получает уведомление Сетки событий, когда файл AVRO записывается в большой двоичный объект в службе хранилища Azure. Она получает путь универсального кода ресурса (URI) большого двоичного объекта, считывает его содержимое и помещает эти данные в хранилище данных SQL.

### <a name="deploy-the-infrastructure"></a>Развертывание инфраструктуры
Разверните необходимую для этого руководства инфраструктуру с помощью Azure PowerShell или Azure CLI, используя [этот шаблон Azure Resource Manager](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json). Этот шаблон создает следующие ресурсы:

-   концентратор событий с включенной функцией "Сбор";
-   учетную запись хранения для собранных данных о событии;
-   план службы приложений Azure для размещения приложения-функции;
-   приложение-функцию для обработки записанных файлов событий;
-   SQL Server для размещения хранилища данных;
-   Хранилище данных SQL для хранения перенесенных данных.

В следующих разделах представлены команды Azure CLI и Azure PowerShell для развертывания инфраструктуры, необходимой для этого руководства. Перед запуском команд обновите имена следующих объектов: 

- Группа ресурсов Azure 
- регион для группы ресурсов;
- пространство имен Центров событий;
- концентратор событий;
- Azure SQL Server;
- пользователь SQL (и пароль);
- База данных SQL Azure
- Хранилище Azure 
- приложение-функция Azure.

Чтобы создать все артефакты в Azure, этим сценариям понадобится некоторое время. Дождитесь завершения сценария, прежде чем продолжать. Если по какой-либо причине развертывание завершается сбоем, удалите группу ресурсов, исправьте проблему и повторите команду. 

#### <a name="azure-cli"></a>Инфраструктура CLI Azure
Чтобы развернуть этот шаблон с помощью Azure CLI, выполните следующие команды.

```azurecli-interactive
az group create -l westus -n rgDataMigrationSample

az group deployment create \
  --resource-group rgDataMigrationSample \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
  --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
```

#### <a name="azure-powershell"></a>Azure PowerShell
Чтобы развернуть этот шаблон с помощью PowerShell, выполните следующие команды.

```powershell
New-AzureRmResourceGroup -Name rgDataMigration -Location westcentralus

New-AzureRmResourceGroupDeployment -ResourceGroupName rgDataMigration -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json -eventHubNamespaceName <event-hub-namespace> -eventHubName hubdatamigration -sqlServerName <sql-server-name> -sqlServerUserName <user-name> -sqlServerDatabaseName <database-name> -storageName <unique-storage-name> -functionAppName <app-name>
```


### <a name="create-a-table-in-sql-data-warehouse"></a>Создание таблицы в хранилище данных SQL 
Создайте таблицу в хранилище данных SQL, запустив сценарий [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql) с помощью [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md), [SQL Server Management Studio](../sql-data-warehouse/sql-data-warehouse-query-ssms.md) или редактора запроса на портале. 

```sql
CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
    [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
    [MeasureTime] datetime NULL, 
    [GeneratedPower] float NULL, 
    [WindSpeed] float NULL, 
    [TurbineSpeed] float NULL
)
WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
```

## <a name="publish-code-to-the-functions-app"></a>Публикация кода в приложение-функцию

1. Откройте решение *EventHubsCaptureEventGridDemo.sln* в Visual Studio 2017 (версии 15.3.2 или более поздней). 

1. В обозревателе решений щелкните правой кнопкой мыши *FunctionEGDWDumper* и выберите **Публиковать**.

   ![Публикация приложения-функции](./media/store-captured-data-data-warehouse/publish-function-app.png)

1. Выберите **Приложение функции Azure** и **Выбрать существующее**. Нажмите кнопку **Опубликовать**.

   ![Целевое приложение-функция](./media/store-captured-data-data-warehouse/pick-target.png)

1. Выберите приложение-функцию, развернутое с помощью шаблона. Нажмите кнопку **ОК**.

   ![Выбор приложения-функции](./media/store-captured-data-data-warehouse/select-function-app.png)

1. После настройки профиля в Visual Studio выберите **Публиковать**.

   ![Выбор публикации](./media/store-captured-data-data-warehouse/select-publish.png)

После публикации функции вы можете подписаться на событие записи из концентраторов событий.


## <a name="create-an-event-grid-subscription-from-the-functions-app"></a>Создание подписки "Сетка событий" из приложения-функции
 
1. Перейдите на [портал Azure](https://portal.azure.com/). Выберите группу ресурсов и приложение-функцию.

   ![Просмотр приложения-функции](./media/store-captured-data-data-warehouse/view-function-app.png)

1. Выберите функцию.

   ![Выбор функции](./media/store-captured-data-data-warehouse/select-function.png)

1. Выберите **Добавление подписки для службы "Сетка событий"**.

   ![Добавить подписку](./media/store-captured-data-data-warehouse/add-event-grid-subscription.png)

1. Присвойте имя подписке для службы "Сетка событий". Используйте тип события **Пространства имен Центров событий**. Укажите значения для экземпляра пространства имен в службе "Центры событий". Оставьте для конечной точки подписчика указанное значение. Нажмите кнопку **Создать**.

   ![Создание подписки](./media/store-captured-data-data-warehouse/set-subscription-values.png)

## <a name="generate-sample-data"></a>Создание примера данных  
Вы завершили настройку концентратора событий, хранилища данных SQL, приложения-функции Azure и подписки "Сетка событий". Вы можете запустить WindTurbineDataGenerator.exe для генерации потоков данных в концентратор событий после обновления строки подключения и имени своего концентратора событий в исходном коде. 

1. На портале выберите пространство имен концентратора событий. Выберите **Строки подключения**.

   ![Выбор элемента "Строки подключения"](./media/store-captured-data-data-warehouse/event-hub-connection.png)

2. Выберите **RootManageSharedAccessKey**.

   ![Выбор ключа](./media/store-captured-data-data-warehouse/show-root-key.png)

3. Скопируйте значение в поле **Connection string — Primary Key** (Строка подключения — первичный ключ).

   ![Копирование ключа](./media/store-captured-data-data-warehouse/copy-key.png)

4. Вернитесь к проекту Visual Studio. В проекте *WindTurbineDataGenerator* откройте файл *program.cs*.

5. Обновите значения **EventHubConnectionString** и **EventHubName** своей строкой подключения и именем концентратора событий. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```

6. Создайте решение, а затем запустите приложение WindTurbineGenerator.exe. 

## <a name="verify-captured-data-in-data-warehouse"></a>Проверка собранных данных в хранилище данных
Через пару минут запросите таблицу в хранилище данных SQL. Обратите внимание, что данные, созданные WindTurbineDataGenerator, были переданы потоком в концентратор событий, записаны в контейнер службы хранилища Azure, а затем перенесены в таблицу хранилища данных SQL с помощью приложения-функции Azure.  

## <a name="next-steps"></a>Дополнительная информация 
Вы можете использовать мощные инструменты визуализации данных в хранилище данных, чтобы получить аналитические сведения.

В этой статье описано, как использовать [Power BI с хранилищем данных SQL](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-integrate-power-bi).



