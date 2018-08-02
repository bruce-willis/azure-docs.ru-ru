---
title: Способы управления подключениями в службе "Функции Azure"
description: Узнайте, как избежать проблем с производительностью в службе "Функции Azure" с помощью статического подключения клиентов.
services: functions
documentationcenter: ''
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: glenga
ms.openlocfilehash: 86727355d36e16f5b3c7edef8ce666fb27805a80
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346306"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Способы управления подключениями в службе "Функции Azure"

Служба "Функции" в приложении-функции предоставляет общий доступ к ресурсам. Одними из этих ресурсов являются подключения: HTTP-подключения, подключения к базе данных и подключения к службам Azure, таким как служба хранилища. При параллельном выполнении многих функций можно остаться без доступных подключений. В этой статье объясняется, как кодировать свои функции, чтобы избежать применения большего количества подключений, чем фактически необходимо.

## <a name="connections-limit"></a>Ограничение числа подключений

Количество доступных подключений ограничено, частично потому, что приложение-функция выполняется в [песочнице службы приложений Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Одно из ограничений, которое налагает песочница на код — [ограничение на количество подключений, в настоящее время — 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). По достижении этого ограничения среда выполнения функций создает журнал со следующим сообщением: `Host thresholds exceeded: Connections`.

Вероятность превышения лимита возрастает, когда [контроллер шкалы добавляет экземпляры приложения-функции](functions-scale.md#how-the-consumption-plan-works) для обработки большего количества запросов. Каждый экземпляр приложения-функции одновременно может вызвать множество функций и все они будут использовать подключения, которые учитываются до предела 300.

## <a name="use-static-clients"></a>Использование статических клиентов

Чтобы избежать большего количества подключений, чем необходимо, повторно используйте экземпляры клиента, а не создавайте новые с каждым вызовом функции. Клиенты сети, такие как [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) и служба хранилища Azure могут управлять соединениями при использовании одного статического клиента.

Ниже приведены некоторые рекомендации по использованию клиента службы в приложении "Функции Azure":

- **Не** создавайте клиент при каждом вызове функции.
- **Создайте** единый статический клиент, который может использоваться при каждом вызове функции.
- **Рассмотрите возможность** создания одного статического клиента в общем вспомогательном классе, если различные функции используют одну службу.

## <a name="httpclient-code-example"></a>Пример кода HttpClient

Ниже приведен пример кода функции, которую создает статический [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx).

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

Один из часто задаваемых вопросов о .NET [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx): "Следует ли мне удалять статический клиент?" Как правило, удаляются объекты, реализующие `IDisposable` после того, как вы прекратили их использование. Однако статический клиент не удаляется, так как вы продолжаете использовать его после завершения функции. Необходимо, чтобы статический клиент существовал в течение срока жизни приложения.

## <a name="documentclient-code-example"></a>Пример кода DocumentClient

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) подключается к экземпляру Azure Cosmos DB. В документации Azure Cosmos DB рекомендуется [использовать отдельный клиент Azure Cosmos DB в течении всего жизненного цикла приложения](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage). В следующем примере показан один шаблон в функции, чтобы это делать.

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

## <a name="sqlclient-connections"></a>Подключения SqlClient

Код функции может использовать поставщик данных .NET Framework для SQL Server ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)), что бы подключатся к реляционной базе данных SQL. Это также основной поставщик данных для платформ, которые основаны на ADO.NET, такие как Entity Framework. В отличие от соединений [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) и [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
) ADO.NET осуществляет объединение подключений в пул по умолчанию. Тем не менее, стоит оптимизировать подключения к базе данных, потому что вы по прежнему можете остаться без подключений. Дополнительные сведения см. в разделе [Объединение подключений в пул в SQL Server (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).

> [!TIP]
> Некоторые платформы данных, такие как [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx), обычно получают строки подключений из раздела файла конфигурации **ConnectionStrings**. В этом случае необходимо добавить строки подключений базы данных SQL непосредственно в список функциональных настроек приложения **Строки подключения** и в [файл local.settings.json](functions-run-local.md#local-settings-file) в локальном проекте. Если вы создаете параметр [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) в коде функции, следует сохранить значение строки подключения в **Параметрах приложения** с другими подключениями.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о том, почему рекомендуется использовать статические клиенты, см. в статье [Антишаблон неправильного создания экземпляров](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Дополнительные советы по повышению производительности службы "Функции Azure" см. в статье [Оптимизация производительности и надежности Функций Azure](functions-best-practices.md).