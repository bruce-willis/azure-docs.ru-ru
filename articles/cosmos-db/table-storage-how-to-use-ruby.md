---
title: Как использовать Хранилище таблиц Azure и API таблиц Azure Cosmos DB с Ruby | Документация Майкрософт
description: Хранение структурированных данных в облаке с помощью хранилища таблиц Azure или API таблиц Azure Cosmos DB.
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: ruby
ms.topic: sample
ms.date: 04/05/2018
ms.author: sngun
ms.openlocfilehash: d1583001550f5f272f4070006a4a6ac3be000de6
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798276"
---
# <a name="how-to-use-azure-table-storage-and-the-azure-cosmos-db-table-api-with-ruby"></a>Как использовать Хранилище таблиц Azure и API таблиц Azure Cosmos DB с Ruby
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Обзор
Из этого руководства вы узнаете, как реализовать стандартные сценарии с использованием Хранилища таблиц Azure и API таблиц Azure Cosmos DB. В приведенных здесь примерах, написанных на Ruby, используется [клиентская библиотека таблиц службы хранилища Azure для Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table). Здесь описаны такие сценарии, как **создание и удаление таблицы, вставка и запрос сущностей в таблице**.

## <a name="create-an-azure-service-account"></a>Создание учетной записи службы Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Создание учетной записи хранения Azure
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="add-access-to-storage-or-azure-cosmos-db"></a>Добавление доступа к службе хранилища или Azure Cosmos DB
Для использования службы хранилища Azure или Azure Cosmos DB необходимо скачать и использовать пакет Ruby для Azure, который содержит набор вспомогательных библиотек, взаимодействующих со службами REST таблиц.

### <a name="use-rubygems-to-obtain-the-package"></a>Использование RubyGems для получения пакета
1. Используйте интерфейс командной строки, например **PowerShell** (Windows), **Terminal** (Mac) или **Bash** (Unix).
2. Введите **gem install azure-storage-table** в окне командной строки, чтобы установить пакет gem и зависимости.

### <a name="import-the-package"></a>Импорт пакета
С помощью любого текстового редактора добавьте указанный код в начало файла Ruby, где планируется использовать службу хранилища.

```ruby
require "azure/storage/table"
```

## <a name="add-an-azure-storage-connection"></a>Добавление подключения к службе хранилища Azure
Модуль службы хранилища Azure считывает переменные среды **AZURE_STORAGE_ACCOUNT** и **AZURE_STORAGE_ACCESS_KEY**, чтобы получить информацию, необходимую для подключения к учетной записи хранения Azure. Если эти переменные среды не заданы, необходимо указать сведения об учетной записи перед использованием **Azure::Storage::Table::TableService** с помощью приведенного ниже кода.

```ruby
Azure.config.storage_account_name = "<your Azure Storage account>"
Azure.config.storage_access_key = "<your Azure Storage access key>"
```

Вот как можно получить эти значения из классический учетной записи хранения или учетной записи хранения Resource Manager на портале Azure.

1. Войдите на [портал Azure](https://portal.azure.com).
2. Перейдите к учетной записи хранения, которая будет использоваться.
3. В колонке "Параметры" справа щелкните **Ключи доступа**.
4. В колонке "Ключи доступа" вы увидите ключи доступа 1 и 2. Можно использовать любой из них.
5. Щелкните значок копирования, чтобы скопировать ключ в буфер обмена.

## <a name="add-an-azure-cosmos-db-connection"></a>Добавление подключения к Azure Cosmos DB
Чтобы подключиться к Azure Cosmos DB, скопируйте первичную строку подключения c портала Azure и с ее помощью создайте объект **Client**. Объект **Client** можно передать при создании объекта **TableService**.

```ruby
common_client = Azure::Storage::Common::Client.create(storage_account_name:'myaccount', storage_access_key:'mykey', storage_table_host:'mycosmosdb_endpoint')
table_client = Azure::Storage::Table::TableService.new(client: common_client)
```

## <a name="create-a-table"></a>Создание таблицы
Объект **Azure::Storage::Table::TableService** позволяет работать с таблицами и сущностями. Чтобы создать таблицу, используйте метод **create_table()**. В следующем примере создается таблица или выводится ошибка, если она возникла.

```ruby
azure_table_service = Azure::Storage::Table::TableService.new
begin
    azure_table_service.create_table("testtable")
rescue
    puts $!
end
```

## <a name="add-an-entity-to-a-table"></a>Добавление сущности в таблицу
Чтобы добавить сущность, сначала создайте хэш-объект, который определяет свойства сущности. Обратите внимание, что для каждой сущности необходимо указать **PartitionKey** и **RowKey**. Это уникальные идентификаторы сущностей, которые можно запросить гораздо быстрее, чем для других свойств. Служба хранилища Azure использует **PartitionKey** , чтобы автоматически распространять сущности таблицы на множество узлов хранилища. Сущности с одним значением **PartitionKey** хранятся на одном узле. **RowKey** — это уникальный идентификатор сущности в разделе, которому она принадлежит.

```ruby
entity = { "content" => "test entity",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.insert_entity("testtable", entity)
```

## <a name="update-an-entity"></a>Обновление сущности
Для обновления имеющейся сущности доступно несколько методов:

* **update_entity():** обновляет имеющуюся сущность путем ее замены.
* **merge_entity():** обновляет сущность посредством объединения новых значений свойств с имеющейся сущностью.
* **insert_or_merge_entity():** обновляет имеющуюся сущность путем ее замены. Если сущность не существует, будет вставлена новая сущность.
* **insert_or_replace_entity():** обновляет сущность посредством объединения новых значений свойств с имеющейся сущностью. Если сущность не существует, будет вставлена новая сущность.

В следующем примере показано обновление сущности с помощью метода **update_entity()**.

```ruby
entity = { "content" => "test entity with updated content",
    :PartitionKey => "test-partition-key", :RowKey => "1" }
azure_table_service.update_entity("testtable", entity)
```

Если сущность, которая обновляется, не существует, при использовании метода **update_entity()** или **merge_entity()** операция завершится ошибкой. Поэтому, если вы хотите сохранить сущность независимо от того, существует она или нет, используйте метод **insert_or_replace_entity()** или **insert_or_merge_entity()**.

## <a name="work-with-groups-of-entities"></a>Работа с группами сущностей
Иногда имеет смысл отправлять совместно несколько операций в пакете для атомарной обработки сервером. Для этого сначала требуется создать объект **Batch**, а затем использовать метод **execute_batch()** для **TableService**. В следующем примере показана отправка двух сущностей с RowKey 2 и 3 в пакете. Обратите внимание, что пример работает только для сущностей с одинаковым значением PartitionKey.

```ruby
azure_table_service = Azure::TableService.new
batch = Azure::Storage::Table::Batch.new("testtable",
    "test-partition-key") do
    insert "2", { "content" => "new content 2" }
    insert "3", { "content" => "new content 3" }
end
results = azure_table_service.execute_batch(batch)
```

## <a name="query-for-an-entity"></a>Запрос сущности
Чтобы отправить запрос к сущности в таблице, используйте метод **get_entity()**: передайте имя таблицы, **PartitionKey** и **RowKey**.

```ruby
result = azure_table_service.get_entity("testtable", "test-partition-key",
    "1")
```

## <a name="query-a-set-of-entities"></a>Запрос набора сущностей
Чтобы отправить запрос к набору сущностей в таблице, создайте хэш-объект запроса и используйте метод **query_entities()**. В следующем примере показано, как получить все сущности с одним значением **PartitionKey**:

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'" }
result, token = azure_table_service.query_entities("testtable", query)
```

> [!NOTE]
> Если полученный в результате набор слишком велик и не может быть возвращен в одном запросе, возвращается маркер продолжения, который можно использовать для извлечения последующих страниц.
>
>

## <a name="query-a-subset-of-entity-properties"></a>Запрос подмножества свойств сущности
Запрос к таблице может получить лишь несколько свойств сущности. Этот метод, который называется "проекцией", снижает потребление пропускной способности и может повысить производительность запросов, особенно для крупных сущностей. Используйте предложение select и передайте имена свойств, которые необходимо передать клиенту.

```ruby
query = { :filter => "PartitionKey eq 'test-partition-key'",
    :select => ["content"] }
result, token = azure_table_service.query_entities("testtable", query)
```

## <a name="delete-an-entity"></a>Удаление сущности
Чтобы удалить сущность, используйте метод **delete_entity()**. Передайте имя таблицы, содержащей сущность, а также свойства PartitionKey и RowKey сущности.

```ruby
azure_table_service.delete_entity("testtable", "test-partition-key", "1")
```

## <a name="delete-a-table"></a>Удаление таблицы
Чтобы удалить таблицу, используйте метод **delete_table()** и передайте имя удаляемой таблицы.

```ruby
azure_table_service.delete_table("testtable")
```

## <a name="next-steps"></a>Дополнительная информация

* [Обозреватель хранилищ Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) — это бесплатное автономное приложение от корпорации Майкрософт, позволяющее визуализировать данные из службы хранилища Azure на платформе Windows, macOS и Linux.
* [Центр разработчиков для Ruby](https://azure.microsoft.com/develop/ruby/)
* [Клиентская библиотека таблиц службы хранилища Microsoft Azure для Ruby](https://github.com/azure/azure-storage-ruby/tree/master/table) 

