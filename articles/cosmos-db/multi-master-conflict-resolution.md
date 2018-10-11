---
title: Разрешение конфликтов с несколькими источниками в Azure Cosmos DB
description: В этой статье описываются категории конфликтов и режимы их разрешения, такие как "Приоритет последней записи", "Пользовательский – определяемый пользователем" и "Пользовательский – асинхронный", в Azure Comsos DB с несколькими источниками.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 39fd393e78a2b66749c6aa34a758b185b38effdf
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041193"
---
# <a name="multi-master-conflict-resolution-in-azure-cosmos-db"></a>Разрешение конфликтов с несколькими источниками в Azure Cosmos DB 

Azure Cosmos DB с несколькими источниками предоставляет глобальные режимы разрешения конфликтов, чтобы обеспечить согласованность данных во всех регионах репликации.

## <a name="conflict-categories"></a>Категории конфликтов

Существует три категории конфликтов, возникающих при работе с данными Azure Cosmos DB:

* **Конфликты вставки.** Этот тип конфликта возникает, когда приложение одновременно вставляет из двух или нескольких регионов два или более документа с одним и тем же уникальным индексом (например, ID). В этом случае все записи могут быть изначально выполнены успешно, но на основе выбранной вами политики разрешения конфликтов только один документ будет иметь исходный идентификатор.

* **Конфликты замены.** Этот тип конфликта возникает, когда приложение одновременно обновляет один документ из двух или нескольких регионов.

* **Конфликты удаления.** Этот тип конфликта возникает, когда приложение одновременно удаляет документ из одного региона и обновляет его из одного или нескольких регионов. 

## <a name="conflict-resolution-modes"></a>Режимы разрешения конфликтов

В Azure Cosmos DB существуют три режима разрешения конфликтов. Режимы разрешения конфликтов определяются для каждой коллекции.

> [!NOTE]
> Пользователи SQL API могут выбирать между тремя режимами разрешения разных конфликтов. Для всех других моделей API (MongoDB, Cassandra, Graph и таблицы) конфликты разрешаются с помощью приоритета последней записи.

### <a name="last-writer-wins-lww"></a>Приоритет последней записи (LWW)

Приоритет последней записи — это политика разрешения конфликтов по умолчанию. В этом режиме конфликты разрешаются на основе числового значения, переданного в свойстве документа.

Следующий фрагмент кода — это пример настройки политики разрешения конфликтов "приоритет последней записи" при использовании .Net SDK. "ConflictResolutionPath" определяет путь к свойству, которое используется для разрешения конфликта. В этом примере `/userDefinedId` — это путь разрешения конфликтов и документ с наибольшим значением `userDefinedId`, которое всегда будет побеждать в случае конфликта. Чтобы зарегистрировать режим "Приоритет последней записи", подготовьте коллекцию с ConflictResolutionPolicy так, как показано ниже.

```csharp
DocumentCollection lwwCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
   Id = "myCollection", 
   ConflictResolutionPolicy = new ConflictResolutionPolicy
   {
      Mode = ConflictResolutionMode.LastWriterWins,
      ConflictResolutionPath = "/userDefinedId"
   }
});
```

 Режим разрешения "приоритет последней записи" применяется к разным категориям данных конфликта:

* **Конфликты вставки и обновления.** Если во время операции вставки или замены сталкиваются два или более документа, то документ, который содержит наибольшее значение для пути разрешения конфликта, побеждает (то есть, userDefinedId). Если у нескольких документов одинаковое числовое значение для пути разрешения конфликта, то выбранный победитель не является детерминированным. Тем не менее, все регионы будут сходиться к одному победителю.

* **Конфликты удаления.** В случае конфликта удаления, удаление всегда выигрывает у конфликтов замены, независимо от значения пути разрешения конфликта.

### <a name="custom--user-defined-procedure"></a>Пользовательский – определяемый пользователем

В этом режиме разрешения конфликтов пользовательские элементы управления регистрируют в коллекцию определяемые пользователем процедуры (UDP). UDP имеет определенную подпись. Если вы выберете эту опцию, но не зарегистрируете UDP, или если во время выполнения UDP генерирует исключение, то конфликты записываются в веб-канал конфликтов, где их можно разрешить по отдельности.

Чтобы зарегистрировать режим "Пользовательский – определяемый пользователем", подготовьте коллекцию с ConflictResolutionPolicy так, как показано ниже.

```csharp
DocumentCollection udpCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
  Id = "myCollection",
  ConflictResolutionPolicy = new ConflictResolutionPolicy
  {
     Mode = ConflictResolutionMode.Custom,
     ConflictResolutionProcedure = UriFactory.CreateStoredProcedureUri("myDatabase","myCollection","myUdpStoredProcedure").ToString()
  }
});
```

Затем добавьте определяемую пользователем процедуру в коллекцию, как показано ниже.

```csharp
StoredProcedure myUdpStoredProc = new StoredProcedure
{
   Id = "myUdpStoredProcedure",
   Body = myUdpStoredProcText
};
await myClient.UpsertStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("myDatabase", "myCollection"), myUdpStoredProc);
```

Хранимая процедура, которая зарегистрирована в коллекции, имеет специальную подпись. В этом примере для разрешения конфликтов UDP использует свойство `UserDefinedId`. В конфликте побеждает документ с наибольшим значением.

```javascript
function myUdpStoredProcedure(incomingDocument, existingDocument, isDeleteConflict, conflictingDocuments){
    var collection = getContext().getCollection();

    if (!incomingDocument) {
        if (existingDocument) {

            collection.deleteDocument(existingDocument._self, {}, function(err, responseOptions) {
                if (err) throw err;
            });
        }
    } else if (isDeleteConflict) {
        // delete always wins.
    } else {
        var documentToUse = incomingDocument;

        if (existingDocument) {
            if (documentToUse.userDefinedId < existingDocument.userDefinedId) {
                documentToUse = existingDocument;
            }
        }

        var i;
        for (i = 0; i < conflictingDocuments.length; i++) {
            if (documentToUse.userDefinedId < conflictingDocuments[i].userDefinedId) {
                documentToUse = conflictingDocuments[i];
            }
        }

        tryDelete(conflictingDocuments, incomingDocument, existingDocument, documentToUse);
    }

    function tryDelete(documents, incoming, existing, documentToInsert) {
        if (documents.length > 0) {
            collection.deleteDocument(documents[0]._self, {}, function(err, responseOptions) {
                if (err) throw err;

                documents.shift();
                tryDelete(documents, incoming, existing, documentToInsert);
            });
        } else if (existing) {
            collection.replaceDocument(existing._self, documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        } else {
            collection.createDocument(collection.getSelfLink(), documentToInsert,
                function(err, documentCreated) {
                    if (err) throw err;
                });
        }
    }
}

```

Процедура имеет четыре параметра:

* **incomingDocument:** указывает конфликтующие версии документа. Существуют конфликты вставки, замены или удаления. Для конфликта удаления это будет образ удаления (отметка полного удаления) без содержимого.

* **existingDocument:** указывает зафиксированную версию документа, который имеет то же значение Rid, что и incomingDocument. Этот параметр имеет значение NULL для конфликта вставки и удаления.

* **isDeleteConflict:** логический флаг, указывающий на то, конфликтует ли входящий документ с ранее удаленным документом. Если этому параметру присвоить значение true, то existingDocument также будет иметь значение NULL.

* **conflictingDocuments:** указывает коллекцию зафиксированных версий всех документов базы данных, которые конфликтуют с incomingDocument в столбце "Идентификатор" или в других полях уникального индекса. У этих документов будет разное значение Rid по сравнению с incomingDocument.

Определяемая пользователем процедура имеет полный доступ к ключу раздела Cosmos DB и может выполнять любые операции хранения для разрешения конфликтов. Если определяемая пользователем процедура не зафиксирует версию конфликта, то система бросит конфликт и existingDocument останутся зафиксированными. Если определяемая пользователем процедура завершается ошибкой либо ее не существует, то Azure Cosmos DB добавит конфликт в веб-канал, где его можно обработать асинхронно, как показано в разделе [Asynchronous conflict resolution mode](#custom--asynchronous) (Пользовательский асинхронный режим разрешения конфликтов). 

### <a name="custom--asynchronous"></a>Пользовательский – асинхронный  

В этом режиме Azure Cosmos DB исключает все зафиксированные конфликты (вставки, замены и удаления) и регистрирует их в ленте конфликтов только для чтения для их отложенного разрешения с помощью приложения пользователя. Приложение может выполнять разрешение конфликтов асинхронно и использовать для разрешения конфликта любую логику или ссылаться на любой внешний источник, приложение или службу.

Чтобы зарегистрировать режим разрешения конфликтов "Пользовательский – асинхронный", подготовьте коллекцию с ConflictResolutionPolicy так, как показано ниже.

```csharp
DocumentCollection manualCollection = await myClient.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("myDatabase"), new DocumentCollection
{
    Id = "myCollection",
    ConflictResolutionPolicy = new ConflictResolutionPolicy
    {
        Mode = ConflictResolutionMode.Custom
    }
});
```

Чтобы читать и обрабатывать любые конфликты в веб-канале конфликтов, выполните приведенный ниже код. Хранимые данные веб-канала конфликтов добавляют некоторую стоимость. Поэтому рекомендуется удалять хранимые данные из в веб-канала конфликтов после их обработки.

```csharp
FeedResponse<Conflict> response = await myClient.ReadConflictFeedAsync(myCollectionUri);
  foreach (Conflict conflict in response)
  {
      switch(conflict.OperationKind)
      {
         case OperationKind.Create:
         //Process Insert Conflicts.
         …
         case OperationKind.Replace:
         //Process Replace Conflicts
         …
         case OperationKind.Delete:
         //Process Delete Conflicts
         …
      }
  //Optionally delete the conflict after processed
  await myClient.DeleteConflictAsync(conflict.SelfLink);
  }
```

> [!NOTE]
> Канал конфликтов не содержит прослушиватель для отправки уведомлений для последующей обработки, таких как канал изменений в Cosmos DB. Чтобы собрать канал конфликтов и определить, существуют ли конфликты, необходимо использовать логику.

## <a name="code-samples"></a>Примеры кода

Ниже приведены примеры приложений, которые демонстрируют разрешение конфликтов для перечисленных API. Каждый пример создает конфликты в контейнере, а затем демонстрирует способы их устранения для каждого поддерживаемого режима разрешения конфликтов.

|Модель API  | SDK |Образец |
|---------|---------|---------|
|API-интерфейс SQL    | .NET    |[azure-cosmos-db-sql-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-dotnet-multi-master)  |
|API-интерфейс SQL    | Узел    |[azure-cosmos-js/samples/MultiRegionWrite/](https://github.com/Azure/azure-cosmos-js/tree/master/samples/MultiRegionWrite)  |
|API-интерфейс SQL    | Java    |[azure-cosmos-db-sql-java-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-sql-java-multi-master)  |
|MongoDB  | .NET    |[azure-cosmos-db-mongodb-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-multi-master)   |
|API таблиц  | .NET    |[azure-cosmos-db-table-dotnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-table-dotnet-multi-master)       |
|API Gremlin | .NET | [azure-cosmos-db-gremlin-dontnet-multi-master](https://github.com/Azure-Samples/azure-cosmos-db-gremlin-dontnet-multi-master)|

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы узнали о категориях конфликтов и режимах их разрешения для Azure Cosmos DB. Далее рекомендуем ознакомиться со следующими ресурсами:

* [Использование клиентов MongoDB с несколькими источниками](multi-master-oss-nosql.md)
