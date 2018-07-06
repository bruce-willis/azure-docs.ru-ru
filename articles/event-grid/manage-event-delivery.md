---
title: Управление параметрами доставки для подписок на службу "Сетка событий Azure"
description: Описание настройки опций доставки событий для службы "Сетка событий Azure".
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 65e79f492e96c418502e096b60992ba992868dd7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035413"
---
# <a name="manage-event-grid-delivery-settings"></a>Управление параметрами доставки службы "Сетка событий Azure"

При создании подписки на события, можно настроить параметры доставки событий. Можно задать продолжительность попыток доставки сообщения службой "Сетка событий Azure". Можно задать учетную запись для хранения событий, которые не удается доставить в конечную точку.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Установка политики повтора

При создании подписки на сетку событий, можно задать значения для продолжительности попыток доставки события службой "Сетка событий Azure". По умолчанию служба "Сетка событий Azure" осуществляет не более 30 попыток в течение 24 часов (1440 минут). В подписке на сетку событий можно установить любое из этих значений.

Чтобы установить время жизни события на значение, отличное от 1440 минут, используйте следующий код.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --event-ttl 720
```

Чтобы установить максимальное количество попыток, отличное от 30, используйте следующий код.

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --max-delivery-attempts 18
```

Если заданы оба параметра `event-ttl` и `max-deliver-attempts`, служба "Сетка событий Azure" использует первый для истечения повторных попыток.

## <a name="set-dead-letter-location"></a>Установка размещения недоставленных сообщений

Когда службе "Сетка событий Azure" не удается доставить событие, она может отправлять его в учетною запись хранилища. Этот процесс называется перемещение в очередь недоставленных сообщений. По умолчанию в службе "Сетка событий Azure" не включено перемещение в очередь недоставленных сообщений. Чтобы его включить, необходимо указать учетную запись хранения недоставленных событий при создании подписки на событие. Можно извлекать события из этой учетной записи хранения, чтобы продолжать доставку.

Служба "Сетка событий Azure" отправляет событие в расположение недоставленных сообщений, если все ее повторные попытки исчерпаны или она получает сообщение об ошибке, указывающую, что доставка никогда не будет успешной. Например, если служба "Сетка событий Azure" при доставке события получает ошибку "Неправильный формат", она немедленно отправляет это событие в расположение недоставленных сообщений.

Прежде чем задать параметры расположения недоставленных сообщений, необходимо иметь учетную запись хранения с контейнером. При создании подписки на событие необходимо ввести конечную точку для этого контейнера. Конечная точка имеет следующий формат: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Следующий скрипт возвращает идентификатор ресурса для существующей учетной записи хранения и создает подписку на событие, используя контейнер в этой учетной записи хранения для недоставленных сообщений конечной точки.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Чтобы использовать службу "Сетка событий Azure" для реагирования на недоставленные события, [создайте подписку на события](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) для хранилища больших двоичных объектов недоставленных сообщений. Каждый раз, когда ваше хранилище больших двоичных объектов недоставленных сообщений получает недоставленное событие, служба "Сетка событий Azure" уведомляет вашего обработчика. Обработчик отвечает действиями, которые необходимо предпринять для согласования недоставленных событий. 

## <a name="next-steps"></a>Дополнительная информация

* См. дополнительные сведения о [доставке сообщений и повторных попытках в Сетке событий](delivery-and-retry.md).
* Общие сведения о службе "Сетка событий" см. в разделе [Общие сведения о службе "Сетка событий Azure"](overview.md).
* Сведения о том, как быстро приступить к использованию службы "Сетка событий", см. в разделе [Создание и перенаправление пользовательского события со службой "Сетка событий Azure"](custom-event-quickstart.md).
