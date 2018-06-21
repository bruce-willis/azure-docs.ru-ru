---
title: Способы управления подключениями в службе "Функции Azure"
description: Узнайте, как избежать проблем с производительностью в службе "Функции Azure" с помощью статического подключения клиентов.
services: functions
documentationcenter: ''
author: tdykstra
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: tdykstra
ms.openlocfilehash: 4ea2b033d8d67dd3c921fb833462605ba0aeb687
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659835"
---
# <a name="how-to-manage-connections-in-azure-functions"></a>Способы управления подключениями в службе "Функции Azure"

Служба "Функции" в приложении-функции предоставляет общий доступ к ресурсам. Одними из этих ресурсов являются подключения: HTTP-подключения, подключения к базе данных и подключения к службам Azure, таким как служба хранилища. При параллельном выполнении многих функций можно остаться без доступных подключений. В этой статье объясняется, как кодировать свои функции, чтобы избежать применения большего количества подключений, чем фактически необходимо.

## <a name="connections-limit"></a>Ограничение числа подключений

Количество доступных подключений ограничено, частично потому, что приложение-функция выполняется в [песочнице службы приложений Azure](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox). Одно из ограничений, которое налагает песочница на код — [ограничение на количество подключений, в настоящее время — 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits). По достижении этого ограничения среда выполнения функций создает журнал со следующим сообщением: `Host thresholds exceeded: Connections`.

Вероятность превышения ограничения увеличивается, когда [контроллер масштаба добавляет экземпляры приложения-функции](functions-scale.md#how-the-consumption-plan-works). Каждый экземпляр приложения-функции может одновременно вызвать множество функций, все они будут использовать подключения, которые подсчитываются до предела 300.

## <a name="use-static-clients"></a>Использование статических клиентов

Чтобы избежать большего количества подключений, чем необходимо, повторно используйте экземпляры клиента, а не создавайте новые с каждым вызовом функции. Клиенты .NET, например `HttpClient`, `DocumentClient`, и клиенты службы хранилища Azure могут управлять подключениями при использовании одного статического клиента.

Ниже приведены некоторые рекомендации по использованию клиента службы в приложении "Функции Azure":

- **Не** создавайте клиент при каждом вызове функции.
- **Создайте** единый статический клиент, который может использоваться при каждом вызове функции.
- **Рассмотрите возможность** создания одного статического клиента в общем вспомогательном классе, если различные функции используют одну службу.

## <a name="httpclient-code-example"></a>Пример кода HttpClient

Ниже приведен пример кода функции, который создает статический `HttpClient`:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("http://example.com");
    // Rest of function
}
```

При работе с `HttpClient` .NET часто задается вопрос о том, следует ли удалять клиент. Как правило, удаляются объекты, реализующие `IDisposable` после того, как вы прекратили их использование. Однако статический клиент не удаляется, так как вы продолжаете использовать его после завершения функции. Необходимо, чтобы статический клиент существовал в течение срока жизни приложения.

## <a name="documentclient-code-example"></a>Пример кода DocumentClient

`DocumentClient` подключается к экземпляру Cosmos DB. В документации по Cosmos DB рекомендуется [использовать отдельный клиент Azure Cosmos DB в течение всего жизненного цикла приложения](https://docs.microsoft.com/en-us/azure/cosmos-db/performance-tips#sdk-usage). В следующем примере показан один шаблон для соответствующей функции.

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

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о том, почему рекомендуется использовать статические клиенты, см. в статье [Антишаблон неправильного создания экземпляров](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/).

Дополнительные советы по повышению производительности службы "Функции Azure" см. в статье [Оптимизация производительности и надежности Функций Azure](functions-best-practices.md).