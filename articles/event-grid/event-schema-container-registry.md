---
title: Схема Реестра контейнеров Сетки событий Azure
description: В этой статье приведены свойства событий Реестра контейнеров, используемых со службой "Сетка событий Azure".
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 08/13/2018
ms.author: tomfitz
ms.openlocfilehash: d18a6718e4c29f3d04639644dc752b0733f15ba8
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42144403"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Схема событий службы "Сетка событий Azure" для Реестра контейнеров

В этой статье описаны свойства и схема для событий Реестра контейнеров. Общие сведения о схемах событий см. в статье [Схема событий службы "Сетка событий Azure"](event-schema.md).

## <a name="available-event-types"></a>Доступные типы событий

Хранилище BLOB-объектов выдает следующие типы событий:

| Тип события | ОПИСАНИЕ |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Вызывается при отправке образа. |
| Microsoft.ContainerRegistry.ImageDeleted | Вызывается при удалении образа. |

## <a name="example-event"></a>Пример события

В примере ниже показана схема события передачи образа. 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Схема для события удаления образа аналогична:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
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
| data | object | Данные события хранилища BLOB-объектов. |
| dataVersion | строка | Версия схемы для объекта данных. Версию схемы определяет издатель. |
| metadataVersion | строка | Версия схемы для метаданных события. Служба "Сетка событий" определяет схему свойств верхнего уровня. Это значение предоставляет служба "Сетка событий". |

Объект данных имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| id | строка | Идентификатор события. |
| timestamp | строка | Время возникновения события. |
| action | строка | Действие, которое включает в себя указанное событие. |
| target | object | Целевой объект события. |
| запрос | object | Запрос, который создал событие. |

Целевой объект имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| mediaType | строка | Тип MIME передаваемого объекта. |
| size | целое число | Число байтов содержимого. Это значение совпадает со значением поля Length. |
| digest | строка | Хэш-код содержимого, как определено в спецификации API HTTP версии 2 реестра. |
| длина | целое число | Число байтов содержимого. Это значение совпадает со значением поля Size. |
| repository | строка | Имя репозитория. |
| tag | строка | Имя тега. |

Объект запроса имеет следующие свойства:

| Свойство | type | ОПИСАНИЕ |
| -------- | ---- | ----------- |
| id | строка | Идентификатор запроса, инициировавшего событие. |
| addr | строка | IP-адрес или имя узла и, возможно, порт клиентского подключения, инициировавшего событие. Это значение — RemoteAddr из стандартного HTTP-запроса. |
| host | строка | Доступное из внешней сети имя узла на экземпляре реестра, которое указано в заголовке host во входящих HTTP-запросах. |
| метод | строка | Метод запроса, который создал событие. |
| useragent | строка | Заголовок user agent из запроса. |

## <a name="next-steps"></a>Дополнительная информация

* См. общие сведения о [службе "Сетка событий Azure"](overview.md).
* Дополнительные сведения о создании подписки на Сетку событий Azure см. в статье [Схема подписки для службы "Сетка событий"](subscription-creation-schema.md).
