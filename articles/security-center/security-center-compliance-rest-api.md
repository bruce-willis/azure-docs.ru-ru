---
title: Мониторинг соответствия требованиям центра безопасности Azure с использованием REST API | Документация Майкрософт
description: Просмотр результатов автоматических проверок соответствия требованиям центра безопасности Azure с использованием REST API.
services: security-center
documentationcenter: na
author: rloutlaw
manager: angerobe
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rloutlaw
ms.openlocfilehash: 651d7737258f1b1b17c8392a09882388ddf95635
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42819310"
---
# <a name="review-security-center-compliance-results-using-the-azure-rest-apis"></a>Просмотр результатов проверок соответствия требованиям центра безопасности с использованием Azure REST API

В этой статье вы узнаете, как получить информацию о соответствии безопасности для списка подписок с использованием интерфейсов Azure REST API.

## <a name="review-compliance-for-each-subscription"></a>Проверка соответствия для каждой подписки

Приведенный ниже пример получает информацию оценки безопасности для данного соответствия и подписки, используя операцию [Get Compliances](/rest/api/securitycenter/compliances/get).

```http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/compliances/{complianceName}?api-version=2017-08-01-preview
```

Параметр `{complianceName}` обязателен и должен содержать имя оцененного соответствия для `{subscription-id}`. Выходные данные будут содержать следующие результаты оценки:

```json
{
...
  "properties": {
    "assessmentResult": [
      {
        "segmentType": "Compliant",
        "percentage": 77.777777777777786
      }
    ],
    "resourceCount": 18,
    "assessmentTimestampUtcDate": "2018-01-01T00:00:00Z"
  }
}
```

## <a name="review-compliance-for-multiple-subscriptions"></a>Проверка соответствия для нескольких подписок

Чтобы получить данные для нескольких подписок, с помощью вызова ниже сначала получите список подписок, используя операцию [List Subscriptions](/rest/api/resources/subscriptions/list). Затем вызовите операцию [Get Compliances](/rest/api/securitycenter/compliances/get) для каждого из возвращенных идентификаторов подписок.

Вызов API:

```http
GET https://management.azure.com/subscriptions?api-version=2016-06-01
```

Возвращает массив со значениями наподобие приведенных ниже. Используйте значения subscriptionId в приведенном выше вызове, чтобы просмотреть информацию о соответствии для всех подписок.

```json
"value": [
    {
      "id": "/subscriptions/{subscription-id}",
      "subscriptionId": "{subscription-id}",
      "displayName": "Demo subscription",
      ...
    }
```






