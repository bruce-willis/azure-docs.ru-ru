---
title: 'Сетка событий Azure: безопасность и проверка подлинности'
description: В статье описываются сетка событий Azure и ее основные понятия.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: babanisa
ms.openlocfilehash: ce0e766a07fd19f523f1f35b9a3cbc865cfb8c71
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42146937"
---
# <a name="event-grid-security-and-authentication"></a>Сетка событий: безопасность и проверка подлинности 

В сетке событий Azure предусмотрено три типа проверки подлинности:

* Подписки на события
* Публикация событий
* Доставка событий веб-перехватчика

## <a name="webhook-event-delivery"></a>Доставка событий веб-перехватчика

Веб-перехватчики — это один из многих способов получения событий из службы "Сетка событий Azure". При возникновении нового события служба "Сетка событий" отправляет в настроенную конечную точку HTTP-запрос, в тексте которого находится событие.

Как и многие другие службы, поддерживающие веб-перехватчики, служба "Сетка событий" требует, чтобы вы подтвердили "право собственности" на конечную точку веб-перехватчика до того, как служба начнет доставку событий в эту конечную точку. Это требование необходимо, чтобы предотвратить преобразование неожидаемой конечной точки в целевую конечную точку для доставки событий из службы "Сетка событий". Однако инфраструктура Azure автоматически обрабатывает эту проверку, когда вы используете любую из следующих трех служб Azure:

* Azure Logic Apps;
* служба автоматизации Azure;
* служба "Функции Azure" для триггера службы "Сетка событий".

При использовании любого другого типа конечной точки, такого как функция Azure на основе триггера HTTP, код конечной точки должен участвовать в подтверждении проверки со службой "Сетка событий". Сетка событий поддерживает две различные модели подтверждения проверки:

1. **Подтверждение кода проверки**. Во время создания подписки на события Сетка событий передает событие проверки подписки в конечную точку. Схема этого события похожа на любое другое событие EventGridEvent, а часть данных этого события включает свойство `validationCode`. После того как приложение проверит, что запрос проверки предназначен для ожидаемой подписки на события, код приложения должен ответить, вернув код проверки в Сетку событий. Этот механизм подтверждения поддерживается во всех версиях Сетки событий.

2. **Подтверждение URL-адреса проверки (подтверждение вручную)**. В некоторых случаях вы не можете управлять исходным кодом конечной точки, чтобы реализовать подтверждение на основе кода проверки. Например, если вы используете стороннюю службу (например, [Zapier](https://zapier.com) или [IFTTT](https://ifttt.com/)), возможно, вы не сможете ответить программным способом с использованием кода проверки. Таким образом, начиная с версии 2018-05-01-preview, Сетка событий теперь поддерживает подтверждение проверки вручную. Если вы создаете подписку на события с помощью пакета SDK или инструментов, которые используют эту новую версию API (2018-05-01-preview), Сетка событий отправит свойство `validationUrl` (в дополнение к свойству `validationCode`) как часть данных события проверки подписки. Чтобы выполнить подтверждение, просто выполните запрос GET к этому URL-адресу через клиент REST или с помощью своего веб-браузера. Предоставленный URL-адрес проверки действителен только около 10 минут. В течение этого времени состояние подготовки подписки на событие находится в `AwaitingManualAction`. Если вы не завершили проверку вручную в течение 10 минут, состояние обеспечения установится на `Failed`. Вам нужно будет повторно активировать создание подписки на события, прежде чем пытаться снова выполнить проверку вручную.

Этот механизм проверки вручную доступен в предварительной версии. Чтобы использовать ее, нужно установить [расширение службы "Сетка событий"](/cli/azure/azure-cli-extensions-list) для [AZ CLI 2.0](/cli/azure/install-azure-cli). Расширение можно установить с помощью `az extension add --name eventgrid`. Если вы используйте REST API, убедитесь, что установлена версия `api-version=2018-05-01-preview`.

### <a name="validation-details"></a>Сведения о проверке

* Во время создания или обновления подписки на события Сетка событий публикует в целевую конечную точку событие проверки подписки. 
* В качестве заголовка событие содержит значение "Aeg-Event-Type: SubscriptionValidation".
* Текст события имеет ту же схему, что и другие события сетки событий.
* Свойство eventType события — "Microsoft.EventGrid.SubscriptionValidationEvent".
* Свойство данных события включает свойство "validationCode" со строкой, сгенерированной случайным образом. Например "validationCode: acb13…".
* Если вы используете API версии 2018-05-01-preview, данные события будут также включать свойство `validationUrl` с URL-адресом для проверки подписки вручную.
* Массив содержит только событие проверки. Другие события отправляются в отдельном запросе после возврата кода проверки.
* Пакеты SDK EventGrid DataPlane содержат классы, соответствующие данным события проверки подписки и ответу на проверку подписки.

Пример SubscriptionValidationEvent показан в следующем примере:

```json
[{
  "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "subject": "",
  "data": {
    "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    "validationUrl": "https://rp-eastus2.eventgrid.azure.net:553/eventsubscriptions/estest/validate?id=B2E34264-7D71-453A-B5FB-B62D0FDC85EE&t=2018-04-26T20:30:54.4538837Z&apiVersion=2018-05-01-preview&token=1BNqCxBBSSE9OnNSfZM4%2b5H9zDegKMY6uJ%2fO2DFRkwQ%3d"
  },
  "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
  "eventTime": "2018-01-25T22:12:19.4556811Z",
  "metadataVersion": "1",
  "dataVersion": "1"
}]
```

Чтобы подтвердить владение конечной точкой, в свойстве validationResponse возвращается код проверки, как показано в следующем примере:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Также можно вручную проверить подписку, отправив запрос GET к URL-адресу проверки. Подписка на событие остается в состоянии ожидания, пока проверка не будет завершена.

Найти пример C#, в котором показано, как обрабатывать подтверждение проверки подписки, можно здесь: https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/blob/master/EventGridConsumer/EventGridConsumer/Function1.cs.

### <a name="checklist"></a>Контрольный список

Если при создании подписки на события вы видите сообщение об ошибке, такое как "Не удалось выполнить проверку предоставленной конечной точки https://your-endpoint-here. Для получения дополнительных сведений посетите https://aka.ms/esvalidation", то это указывает на сбой подтверждения проверки. Чтобы устранить эту ошибку, проверьте следующие аспекты:

* Вы можете управлять кодом приложения в целевой конечной точке? Например, если вы пишете функцию Azure на основе триггера HTTP, есть ли у вас доступ к коду приложения для внесения в него изменений?
* Если у вас есть доступ к коду приложения, реализуйте механизм подтверждения на основе ValidationCode, как показано в примере выше.

* Если у вас нет доступа к коду приложения (например, если вы используете стороннюю службу, поддерживающую веб-перехватчики), вы можете использовать механизм подтверждения вручную. Для этого проверьте, что вы используете API версии 2018-05-01-preview (например, с помощью расширения CLI Сетки событий, описанного выше), чтобы получить свойство validationUrl в событии проверки. Чтобы выполнить подтверждение проверки вручную, получите значение свойства "validationUrl" и перейдите по этому URL-адресу в своем веб-браузере. Если проверка прошла успешно, вы должны увидеть соответствующее сообщение в своем веб-браузере, а для состояния provisioningState подписки на события будет отображаться значение "Succeeded". 

### <a name="event-delivery-security"></a>Защита доставки событий

Защитить конечную точку веб-перехватчика можно путем добавления параметров запроса для URL-адреса веб-перехватчика при создании подписки на события. Задайте один из этих параметров запроса в качестве секрета (например, [маркер доступа](https://en.wikipedia.org/wiki/Access_token)), с помощью которого веб-перехватчик сможет распознавать событие, поступающее из Сетки событий с допустимыми разрешениями. Сетка событий будет включать эти параметры в каждую доставку событий для веб-перехватчика.

При изменении подписки на событие параметры запроса не будут отображаться или возвращаться, если в [интерфейсе командной строки](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) Azure не используется параметр [--include-full-endpoint-url](https://docs.microsoft.com/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az-eventgrid-event-subscription-show) (включить полный URL-адрес конечной точки).

Наконец, необходимо отметить, что служба "Сетка событий Azure" поддерживает только конечные точки веб-перехватчиков HTTPS.

## <a name="event-subscription"></a>Подписка на события

Чтобы подписаться на событие, необходимо иметь разрешение **Microsoft.EventGrid/EventSubscriptions/Write** для требуемого ресурса. Это разрешение необходимо, так как вы записываете новую подписку в области действия ресурса. Требуемый ресурс зависит от того, на какой раздел оформляется подписка: системный или пользовательский. В этом разделе описываются оба типа подписки.

### <a name="system-topics-azure-service-publishers"></a>Системные разделы (издатели служб Azure)

Для системных разделов необходимо разрешение на запись новой подписки на события в области действия ресурса, публикующего событие. Ресурс имеет следующий формат: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{resource-provider}/{resource-type}/{resource-name}`

Например, чтобы подписаться на событие в учетной записи хранения с именем **myacct**, требуется разрешение Microsoft.EventGrid/EventSubscriptions/Write для следующего ресурса: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myacct`

### <a name="custom-topics"></a>Пользовательские разделы

Для пользовательских разделов необходимо разрешение на запись новой подписки на события в области действия для сетки событий. Ресурс имеет следующий формат: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.EventGrid/topics/{topic-name}`

Например, чтобы подписаться на пользовательский раздел с именем **mytopic**, требуется разрешение Microsoft.EventGrid/EventSubscriptions/Write для следующего ресурса: `/subscriptions/####/resourceGroups/testrg/providers/Microsoft.EventGrid/topics/mytopic`

## <a name="topic-publishing"></a>Публикация разделов

Для разделов используется либо подписанный URL-адрес (SAS), либо проверка подлинности с использованием ключа. Рекомендуется использовать SAS, но проверка подлинности с использованием ключа обеспечивает простое программирование, а также совместима со множеством существующих издателей веб-перехватчиков. 

Значение проверки подлинности следует включить в заголовок HTTP. Для SAS в качестве значения заголовка используйте **aeg-sas-token**. Для подлинности с использованием ключа в качестве значения заголовка следует использовать **aeg-sas-key**.

### <a name="key-authentication"></a>Проверка подлинности с использованием ключа

Проверка подлинности с использованием ключа — самая простая форма проверки подлинности. Используйте следующий формат: `aeg-sas-key: <your key>`

Например, для передачи ключа можно использовать следующую команду:

```
aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==
```

### <a name="sas-tokens"></a>Маркеры SAS

Маркеры SAS для сетки события включают ресурс, срок его действия и подпись. Маркер SAS имеет следующий формат: `r={resource}&e={expiration}&s={signature}`.

Ресурс — это путь для раздела сетки событий, в который вы отправляете события. Вот пример допустимого пути к ресурсу: `https://<yourtopic>.<region>.eventgrid.azure.net/eventGrid/api/events`

Вы создаете подпись на основе ключа.

Например, допустимое значение **aeg-sas-token** выглядит следующим образом:

```http
aeg-sas-token: r=https%3a%2f%2fmytopic.eventgrid.azure.net%2feventGrid%2fapi%2fevent&e=6%2f15%2f2017+6%3a20%3a15+PM&s=a4oNHpRZygINC%2fBPjdDLOrc6THPy3tDcGHw1zP4OajQ%3d
```

В следующем примере создается маркер SAS для использования с сеткой событий:

```cs
static string BuildSharedAccessSignature(string resource, DateTime expirationUtc, string key)
{
    const char Resource = 'r';
    const char Expiration = 'e';
    const char Signature = 's';

    string encodedResource = HttpUtility.UrlEncode(resource);
    var culture = CultureInfo.CreateSpecificCulture("en-US");
    var encodedExpirationUtc = HttpUtility.UrlEncode(expirationUtc.ToString(culture));

    string unsignedSas = $"{Resource}={encodedResource}&{Expiration}={encodedExpirationUtc}";
    using (var hmac = new HMACSHA256(Convert.FromBase64String(key)))
    {
        string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(unsignedSas)));
        string encodedSignature = HttpUtility.UrlEncode(signature);
        string signedSas = $"{unsignedSas}&{Signature}={encodedSignature}";

        return signedSas;
    }
}
```

## <a name="management-access-control"></a>Контроль доступа к управлению

Сетка событий Azure позволяет контролировать уровень доступа, предоставленного разным пользователям для выполнения различных операций управления, таких как создание списка подписок на события, создание новых подписок и генерирование ключей. В службе "Сетка событий" используется проверка доступа на основе ролей Azure (RBAC).

### <a name="operation-types"></a>Типы операций

Сетка событий Azure поддерживает следующие действия:

* Microsoft.EventGrid/*/read
* Microsoft.EventGrid/*/write
* Microsoft.EventGrid/*/delete
* Microsoft.EventGrid/eventSubscriptions/getFullUrl/action
* Microsoft.EventGrid/topics/listKeys/action
* Microsoft.EventGrid/topics/regenerateKey/action

Последние три операции возвращают потенциально секретную информацию, которая отфильтровывается из обычных операций чтения. Это наилучший способ ограничить доступ к этим операциям. Настраиваемые роли можно создавать с помощью [Azure PowerShell](../role-based-access-control/role-assignments-powershell.md), [интерфейса командной строки (CLI) Azure](../role-based-access-control/role-assignments-cli.md) и интерфейса [REST API](../role-based-access-control/role-assignments-rest.md).

### <a name="enforcing-role-based-access-check-rbac"></a>Применение проверки доступа на основе ролей (RBAC)

Чтобы принудительно применить RBAC для разных пользователей, выполните следующие действия:

#### <a name="create-a-custom-role-definition-file-json"></a>Создание файла определения пользовательской роли (.json)

Вот пример определений роли сетки событий, позволяющие пользователям выполнять разные действия.

**EventGridReadOnlyRole.json**: разрешено только для операций чтения.

```json
{
  "Name": "Event grid read only role",
  "Id": "7C0B6B59-A278-4B62-BA19-411B70753856",
  "IsCustom": true,
  "Description": "Event grid read only role",
  "Actions": [
    "Microsoft.EventGrid/*/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription Id>"
  ]
}
```

**EventGridNoDeleteListKeysRole.json**: разрешен ограниченный набор действий по публикации и запрещены действия удаления.

```json
{
  "Name": "Event grid No Delete Listkeys role",
  "Id": "B9170838-5F9D-4103-A1DE-60496F7C9174",
  "IsCustom": true,
  "Description": "Event grid No Delete Listkeys role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action"
  ],
  "NotActions": [
    "Microsoft.EventGrid/*/delete"
  ],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

**EventGridContributorRole.json**: разрешено выполнять все действия сетки событий.

```json
{
  "Name": "Event grid contributor role",
  "Id": "4BA6FB33-2955-491B-A74F-53C9126C9514",
  "IsCustom": true,
  "Description": "Event grid contributor role",
  "Actions": [
    "Microsoft.EventGrid/*/write",
    "Microsoft.EventGrid/*/delete",
    "Microsoft.EventGrid/topics/listkeys/action",
    "Microsoft.EventGrid/topics/regenerateKey/action",
    "Microsoft.EventGrid/eventSubscriptions/getFullUrl/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<Subscription id>"
  ]
}
```

#### <a name="create-and-assign-custom-role-with-azure-cli"></a>Создание и назначение пользовательской роли с помощью Azure CLI

Чтобы создать настраиваемую роль, используйте следующую команду:

```azurecli
az role definition create --role-definition @<file path>
```

Чтобы назначить роль пользователю, используйте следующую команду:

```azurecli
az role assignment create --assignee <user name> --role "<name of role>"
```

## <a name="next-steps"></a>Дополнительная информация

* Общие сведения о сетке событий см. в статье [Сведения о сетке событий](overview.md)
