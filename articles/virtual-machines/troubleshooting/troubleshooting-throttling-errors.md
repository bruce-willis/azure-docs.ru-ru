---
title: Устранение ошибок регулирования в Azure | Документация Майкрософт
description: Ошибки регулирования, повторные попытки и задержки в службе вычислений Azure.
services: virtual-machines
documentationcenter: ''
author: changov
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: vashan, rajraj, changov
ms.openlocfilehash: 53d94d8674a064960b3447374f68af0d3fdf6e0c
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47412562"
---
# <a name="troubleshooting-api-throttling-errors"></a>Устранение ошибок регулирования API 

Запросы службы вычислений Azure могут регулироваться в подписке и в пределах каждого региона для оптимизации общей производительности службы. Мы гарантируем, что все вызовы к поставщику вычислительных ресурсов Azure (CRP), который управляет ресурсами в пространстве имен Microsoft.Compute, не превышают максимально допустимую частоту запросов API. В этом документе описывается регулирование API, сведения об устранении неполадок регулирования и рекомендации по предотвращению регулирования.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Регулирование Azure Resource Manager и поставщиками ресурсов  

Azure Resource Manager в качестве "входной двери" Azure выполняет аутентификацию и первоочередную проверку, а также регулирование всех входящих запросов API. [Здесь](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-request-limits) описываются ограничения частоты вызовов Azure Resource Manager и связанные диагностические HTTP-заголовки ответа.
 
При получении клиентом API Azure ошибки регулирования состояние HTTP следующее: "429 —слишком много запросов". Чтобы понять, с помощью чего выполняется регулирование запросов (Azure Resource Manager или поставщика базовых ресурсов, например CRP), проверьте `x-ms-ratelimit-remaining-subscription-reads` для запросов GET и заголовки ответа `x-ms-ratelimit-remaining-subscription-writes` для запросов, отличных от GET. Если оставшееся количество вызовов приближается к 0, значит достигнут определенный Azure Resource Manager предел вызовов в подписке. Действия по всем клиентам подписки учитываются вместе. В противном случае регулирование поступает от поставщика целевых ресурсов (указанного в сегменте `/providers/<RP>` URL-адреса запроса). 

## <a name="call-rate-informational-response-headers"></a>Заголовки ответов, содержащие сведения о частоте вызовов 

| Заголовок                            | Формат значения                           | Пример                               | ОПИСАНИЕ                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-remaining-resource |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Оставшееся количество вызовов API для политики регулирования, охватывающей группу ресурсов или группу операций, включая целевой объект запроса.                                                                   |
| x-ms-request-charge               | ```<count>   ```                             | 1                                     | Число счетчиков вызовов, засчитанных для этого HTTP-запроса по отношению к лимиту применяемой политики. Это чаще всего 1. Для пакетных запросов, например для масштабирования масштабируемого набора виртуальных машин, могут засчитываться несколько счетчиков. |


Обратите внимание, что запрос API может подчиняться нескольким политикам регулирования. Будет создан отдельный заголовок `x-ms-ratelimit-remaining-resource` для каждой политики. 

Ниже приведен пример ответа для удаления виртуальной машины в запросе масштабируемого набора виртуальных машин.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

##<a name="throttling-error-details"></a>Сведения об ошибке регулирования

Состояние HTTP 429 обычно используется для отклонения запроса в связи с достижением ограничения частоты вызовов. Типичный ответ ошибки регулирования от поставщика вычислительных ресурсов будет выглядеть, как в приведенном ниже примере (показаны только соответствующие заголовки):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Политика с количеством оставшихся вызовов 0 является причиной возвращения ошибки регулирования. В этом случае это `HighCostGet30Min`. Общий формат текста ответа — это общий формат ошибки API Azure Resource Manager (совместимый с OData). Основной код ошибки (`OperationNotAllowed`) — это код, используемый поставщиком вычислительных ресурсов для сообщения об ошибках регулирования (среди других типов ошибок клиента). 

Как показано выше, каждая ошибка регулирования содержит заголовок `Retry-After`, который предоставляет минимальное число секунд для ожидания клиентом перед повторным выполнением запроса. 

## <a name="best-practices"></a>Рекомендации 

- Не повторяйте ошибки в API службы Azure без ограничений. Код клиента часто попадает в быстрый цикл повтора при возникновении ошибки, которая не предполагает повторный запрос. Повторные попытки будут в конечном счете исчерпаны в связи с достигнутым допустимым пределом вызовов для целевой группы операции, что повлияет на других клиентов подписки. 
- В случаях автоматизации API большого объема можно реализовать упреждающее саморегулирование на стороне клиента, когда количество доступных вызовов для целевой группы операции опускается ниже порогового значения. 
- Во время отслеживания асинхронных операций учитывайте указания в заголовках Retry-After. 
- Если для кода клиента нужны сведения о конкретной виртуальной машине, то запрос этой виртуальной машины делается напрямую, а не перечисляются все виртуальные машины, содержащиеся в группе ресурсов или в целой подписке, а затем выбирается необходимая виртуальная машина на стороне клиента. 
- Если для кода клиента нужны виртуальные машины, диски и моментальные снимки из определенного расположения Azure, то используйте форму на основе расположения вместо запрашивания всех виртуальных машин в подписке, а затем примените фильтр по расположению на стороне клиента: запрос региональных конечных точек поставщика вычислительных ресурсов `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` и `/subscriptions/<subId>/providers/Microsoft.Compute/virtualMachines`. •   При создании или обновлении ресурсов API, в частности виртуальных машин и масштабируемого набора виртуальных машин, гораздо более эффективно отслеживать завершение возвращаемой асинхронной операции, а не опрашивать URL-адрес ресурса (на основе `provisioningState`).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о механизме повторов для других служб в Azure см. [здесь](https://docs.microsoft.com/en-us/azure/architecture/best-practices/retry-service-specific).