---
title: Привязки службы SignalR для службы "Функции Azure"
description: Узнайте, как использовать привязки службы SignalR с помощью службы "Функции Azure".
services: functions
documentationcenter: na
author: anthonychu
manager: cfowler
editor: ''
tags: ''
keywords: функции azure, функции, обработка событий, динамические вычисления, независимая архитектура
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/23/2018
ms.author: antchu
ms.openlocfilehash: 2892481dca9ce62d96e954656341925b4c8110f9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48802021"
---
# <a name="signalr-service-bindings-for-azure-functions"></a>Привязки службы SignalR для службы "Функции Azure"

В этой статье объясняется, как пройти проверку подлинности и отправлять сообщения в режиме реального времени для клиентов, подключенных к [службе Azure SignalR](https://azure.microsoft.com/services/signalr-service/) с использованием привязок службы SignalR в службе "Функции Azure". Служба "Функции Azure" поддерживает входные и выходные привязки для службы SignalR.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x"></a>Пакеты — Функции 2.x

Привязки службы SignalR доступны в пакете NuGet [Microsoft.Azure.WebJobs.Extensions.SignalRService](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.SignalRService) версии 1.0.0-preview1-*. Исходный код для пакета находится в репозитории GitHub [azure-functions-signalrservice-extension](https://github.com/Azure/azure-functions-signalrservice-extension).

> [!NOTE]
> Служба Azure SignalR общедоступна. Тем не менее привязки службы SignalR для службы "Функции Azure" сейчас доступны в предварительной версии.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2-manual-portal.md)]

## <a name="signalr-connection-info-input-binding"></a>Входная привязка сведений о подключении SignalR

Прежде чем клиент сможет подключиться к службе Azure SignalR, необходимо получить URL-адрес конечной точки службы и действительный маркер доступа. Входная привязка *SignalRConnectionInfo* создает URL-адрес конечной точки службы SignalR и допустимый маркер, которые используются для подключения к службе. Так как маркер ограничен по времени и может использоваться для идентификации конкретного пользователя при подключении, не следует его кэшировать или передавать в совместное пользование между клиентами. С помощью этой привязки клиенты могут использовать триггер HTTP для получения сведений о подключении.

Языковой пример см. в разделах:

* [2.x C#](#2x-c-input-example)
* [2.x JavaScript](#2x-javascript-input-example)

### <a name="2x-c-input-example"></a>Пример входных данных 2.x C#

В следующем примере показана [функция C#](functions-dotnet-class-library.md), получающая сведения о подключении SignalR с помощью входной привязки и возвращающая их по протоколу HTTP.

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo(HubName = "chat")]SignalRConnectionInfo connectionInfo)
{
    return connectionInfo;
}
```

#### <a name="authenticated-tokens"></a>Прошедшие проверку подлинности маркеры

Если функцию активирует прошедший проверку подлинности клиент, вы можете добавить утверждение идентификатора пользователя для созданного маркера. Вы можете легко добавить проверку подлинности к приложению-функции с помощью [проверки подлинности службы приложений] (../app-service/app-service-authentication-overview.md).

Проверка подлинности службы приложений задает заголовки HTTP `x-ms-client-principal-id` и `x-ms-client-principal-name`, содержащие имя и идентификатор субъекта клиента прошедшего проверку подлинности пользователя соответственно. В качестве значения свойства `UserId` привязки можно задать один из заголовков с помощью [выражения привязки](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` или `{headers.x-ms-client-principal-name}`. 

```cs
[FunctionName("GetSignalRInfo")]
public static SignalRConnectionInfo GetSignalRInfo(
    [HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, 
    [SignalRConnectionInfo
        (HubName = "chat", UserId = "{headers.x-ms-client-principal-id}")]
        SignalRConnectionInfo connectionInfo)
{
    // connectionInfo contains an access key token with a name identifier claim set to the authenticated user
    return connectionInfo;
}
```

### <a name="2x-javascript-input-example"></a>Пример входных данных JavaScript 2.x

В следующем примере показана входная привязка для сведений о подключении SignalR в файле *function.json* и [функция JavaScript](functions-reference-node.md), использующая привязку для возврата сведений о подключении.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, req, connectionInfo) {
    context.res = { body: connectionInfo };
    context.done();
};
```

#### <a name="authenticated-tokens"></a>Прошедшие проверку подлинности маркеры

Если функцию активирует прошедший проверку подлинности клиент, вы можете добавить утверждение идентификатора пользователя для созданного маркера. Вы можете легко добавить проверку подлинности к приложению-функции с помощью [проверки подлинности службы приложений] (../app-service/app-service-authentication-overview.md).

Проверка подлинности службы приложений задает заголовки HTTP `x-ms-client-principal-id` и `x-ms-client-principal-name`, содержащие имя и идентификатор субъекта клиента прошедшего проверку подлинности пользователя соответственно. В качестве значения свойства `userId` привязки можно задать один из заголовков с помощью [выражения привязки](functions-triggers-bindings.md#binding-expressions-and-patterns): `{headers.x-ms-client-principal-id}` или `{headers.x-ms-client-principal-name}`. 

Пример файла function.json:

```json
{
    "type": "signalRConnectionInfo",
    "name": "connectionInfo",
    "hubName": "chat",
    "userId": "{headers.x-ms-client-principal-id}",
    "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
    "direction": "in"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, req, connectionInfo) {
    // connectionInfo contains an access key token with a name identifier 
    // claim set to the authenticated user
    context.res = { body: connectionInfo };
    context.done();
};
```

## <a name="signalr-output-binding"></a>Выходная привязка SignalR

Выходная привязка *SignalR* используется для отправки одного или нескольких сообщений с помощью службы Azure SignalR. Вы можете выполнить широковещательную передачу сообщения для всех подключенных клиентов или только для подключенных клиентов, которые прошли проверку подлинности для конкретного пользователя.

Языковой пример см. в разделах:

* [2.x C#](#2x-c-output-example)
* [2.x JavaScript](#2x-javascript-output-example)

### <a name="2x-c-output-example"></a>Пример выходных данных 2.x C#

#### <a name="broadcast-to-all-clients"></a>Широковещательная передача для всех клиентов

В следующем примере показана [функция C#](functions-dotnet-class-library.md), отправляющая сообщение для всех подключенных клиентов с помощью выходной привязки. `Target` — это имя метода, вызываемого для каждого клиента. Свойство `Arguments` представляет собой пустой массив или массив с несколькими объектами, передаваемый в метод клиента.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

#### <a name="send-to-a-user"></a>Отправка пользователю

Сообщения можно отправлять только для подключений, которые прошли проверку подлинности для пользователя, задав свойство `UserId` сообщения SignalR.

```cs
[FunctionName("SendMessage")]
public static Task SendMessage(
    [HttpTrigger(AuthorizationLevel.Anonymous, "post")]object message, 
    [SignalR(HubName = "chat")]IAsyncCollector<SignalRMessage> signalRMessages)
{
    return signalRMessages.AddAsync(
        new SignalRMessage 
        {
            // the message will only be sent to these user IDs
            UserId = "userId1",
            Target = "newMessage", 
            Arguments = new [] { message } 
        });
}
```

### <a name="2x-javascript-output-example"></a>Пример выходных данных JavaScript 2.x

#### <a name="broadcast-to-all-clients"></a>Широковещательная передача для всех клиентов

В следующем примере показана выходная привязка SignalR в файле *function.json* и [функция JavaScript](functions-reference-node.md), использующая эту привязку для отправки сообщения с помощью службы Azure SignalR. Для выходной привязки задайте массив из одного или нескольких сообщений SignalR. Сообщение SignalR состоит из свойства `target`, которое указывает имя метода, вызываемого для каждого клиента, и `arguments`, которое представляет собой массив объектов, передаваемых в метод клиента в качестве аргументов.

Данные привязки в файле *function.json*:

Пример файла function.json:

```json
{
  "type": "signalR",
  "name": "signalRMessages",
  "hubName": "<hub_name>",
  "connectionStringSetting": "<name of setting containing SignalR Service connection string>",
  "direction": "out"
}
```

Ниже показан код JavaScript.

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

#### <a name="send-to-a-user"></a>Отправка пользователю

Сообщения можно отправлять только для подключений, которые прошли проверку подлинности для пользователя, задав свойство `userId` сообщения SignalR.

Файл *function.json* остается без изменений. Ниже показан код JavaScript.

```javascript
module.exports = function (context, req) {
    context.bindings.signalRMessages = [{
        // message will only be sent to these user IDs
        "userId": "userId1",
        "target": "newMessage",
        "arguments": [ req.body ]
    }];
    context.done();
};
```

## <a name="configuration"></a>Параметр Configuration

### <a name="signalrconnectioninfo"></a>SignalRConnectionInfo

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `SignalRConnectionInfo`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type**|| Нужно задать значение `signalRConnectionInfo`.|
|**direction**|| Нужно задать значение `in`.|
|**name**|| Имя переменной, используемой в коде функции для объекта сведений о подключении. |
|**hubName**|**HubName**| В качестве значения следует указать имя центра SignalR, для которого создаются сведения о подключении.|
|**userId**|**UserId**| Необязательно: значение утверждения идентификатора пользователя, которое следует задать в маркере доступа. |
|**connectionStringSetting**|**ConnectionStringSetting**| Имя параметра приложения, который содержит строку подключения службы SignalR (по умолчанию — AzureSignalRConnectionString) |

### <a name="signalr"></a>SignalR

В следующей таблице описываются свойства конфигурации привязки, которые задаются в файле *function.json* и атрибуте `SignalR`.

|свойство function.json | Свойство атрибута |ОПИСАНИЕ|
|---------|---------|----------------------|
|**type**|| Нужно задать значение `signalR`.|
|**direction**|| Нужно задать значение `out`.|
|**name**|| Имя переменной, используемой в коде функции для объекта сведений о подключении. |
|**hubName**|**HubName**| В качестве значения следует указать имя центра SignalR, для которого создаются сведения о подключении.|
|**connectionStringSetting**|**ConnectionStringSetting**| Имя параметра приложения, который содержит строку подключения службы SignalR (по умолчанию — AzureSignalRConnectionString) |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Основные понятия триггеров и привязок в Функциях Azure](functions-triggers-bindings.md)

