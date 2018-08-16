---
title: Схема событий группы ресурсов службы "Сетка событий Azure"
description: Описание свойств для событий группы ресурсов, используемых со службой "Сетка событий Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 08/02/2018
ms.author: tomfitz
ms.openlocfilehash: 407d9fd5b6f4d554af37b60edf12422f8816ac00
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495328"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Схема событий службы "Сетка событий Azure" для групп ресурсов

В этой статье описаны свойства и схема для событий группы ресурсов. См. общие сведения о [схеме событий службы "Сетка событий Azure"](event-schema.md).

Подписки и группы ресурсов Azure выдают одинаковые типы событий. Типы событий связаны с изменениями в ресурсах. Основное различие в том, что группы ресурсов выдают события для ресурсов в пределах группы ресурсов, а подписки Azure — для ресурсов во всей подписке.

События ресурса создаются для операций PUT, PATCH и DELETE, которые отправляются в `management.azure.com`. Операции POST и GET не создают события. Операции, передаваемые в плоскость данных (например, в `myaccount.blob.core.windows.net`), не создают события.

При оформлении подписки на события, связанные с группой ресурсов, ваша конечная точка получает все события для этой группы ресурсов. Это могут быть как события, которые вы хотите просмотреть, например обновление виртуальной машины, так и менее важные события, например появление новой записи в журнале развертывания. Вы можете получать все события в конечной точке и написать код, который обрабатывает нужные события, или задать фильтр при создании подписки на события.

Для программной обработки события можно отсортировать по значению `operationName`. Например, конечная точка событий может обрабатывать только события для операций `Microsoft.Compute/virtualMachines/write` или `Microsoft.Storage/storageAccounts/write`.

Тема события — это идентификатор ресурса, который является целевым объектом операции. Для фильтрации событий ресурса укажите идентификатор ресурса при создании подписки на события. Примеры скриптов см. в статьях об оформлении подписки и фильтрации для группы ресурсов с помощью [PowerShell](scripts/event-grid-powershell-resource-group-filter.md) и [Azure CLI](scripts/event-grid-cli-resource-group-filter.md). Чтобы выполнить фильтрацию по типу ресурсов, используйте значение в следующем формате: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

## <a name="available-event-types"></a>Доступные типы событий

Группы ресурсов выдают события управления из Azure Resource Manager, например при создании виртуальной машины или удалении учетной записи хранения.

| Тип события | ОПИСАНИЕ |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Возникает при успешном создании или обновлении ресурса. |
| Microsoft.Resources.ResourceWriteFailure | Возникает при неудачном создании или обновлении ресурса. |
| Microsoft.Resources.ResourceWriteCancel | Возникает при отмене создания или обновления ресурса. |
| Microsoft.Resources.ResourceDeleteSuccess | Возникает при успешном удалении ресурса. |
| Microsoft.Resources.ResourceDeleteFailure | Возникает при неудачном удалении ресурса. |
| Microsoft.Resources.ResourceDeleteCancel | Возникает при отмене удаления ресурса. Это событие происходит при отмене развертывания шаблона. |

## <a name="example-event"></a>Пример события

В следующем примере показана схема события **ResourceWriteSuccess**. Та же схема используется для событий **ResourceWriteFailure** и **ResourceWriteCancel** с разными значениями для `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

В следующем примере показана схема события **ResourceDeleteSuccess**. Та же схема используется для событий **ResourceDeleteFailure** и **ResourceDeleteCancel** с разными значениями для `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

## <a name="event-properties"></a>Свойства события

Событие содержит следующие высокоуровневые данные:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| Раздел | строка | Полный путь к ресурсу для источника событий. Это поле защищено от записи. Это значение предоставляет служба "Сетка событий". |
| subject | строка | Определенный издателем путь к субъекту событий. |
| eventType | строка | Один из зарегистрированных типов событий для этого источника событий. |
| eventTime | строка | Время создания события с учетом времени поставщика в формате UTC. |
| id | строка | Уникальный идентификатор события. |
| data | object | Данные события группы ресурсов. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| authorization | object | Запрошенная авторизация для операции. |
| claims | object | Свойства утверждений. Дополнительные сведения см. в [спецификациях JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | строка | Идентификатор операции для устранения неполадок. |
| httpRequest | object | Подробные сведения об операции. Этот объект включается только при обновлении существующего ресурса или при удалении ресурса. |
| resourceProvider | строка | Поставщик ресурсов, выполняющий операцию. |
| resourceUri | строка | URI ресурса в операции. |
| operationName | строка | Операция, которая выполнена. |
| status | строка | Состояние операции. |
| subscriptionId | строка | Идентификатор подписки ресурса. |
| tenantId | строка | Идентификатор клиента ресурса. |

## <a name="next-steps"></a>Дополнительная информация

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
