---
title: Проверка данных о выставлении счетов по подписке Azure с помощью REST API | Документация Майкрософт
description: Узнайте, как использовать интерфейсы REST API Azure для проверки сведений о выставлении счетов по подписке.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: cc29d1f613af67604d50654be794cc90080098bb
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064487"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Проверка данных о выставлении счетов по подписке с помощью интерфейсов REST API

Интерфейсы API отчетов Azure позволяют проверять данные о расходах на Azure и управлять ими.  

С помощью фильтров вы можете настроить результаты в соответствии со своими требованиями.

Из этой статьи вы узнаете, как с помощью REST API получить сведения о выставлении счетов по подписке для указанного диапазона дат.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Создание запроса  

`{subscriptionID}` — это обязательный параметр, который позволяет определить целевую подписку.

`{billingPeriod}` — это обязательный параметр, который позволяет указать текущий [период выставления счетов](https://docs.microsoft.com/rest/api/billing/billingperiods/get#billingperiod).

Параметры `${startDate}` и `${endDate}` являются обязательными в нашем примере, но не для конечной точки.  Они позволяют указать диапазон дат в строковом формате ГГГГ-ММ-ДД (например, `'20180501'` и `'20180615'`). 

Ниже приведены обязательные заголовки. 

|Заголовок запроса|ОПИСАНИЕ|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный элемент. Задайте значение `application/json`.|  
|*Authorization:*|Обязательный элемент. Задайте в качестве значения [допустимый токен доступа](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer`. |  

## <a name="response"></a>Ответ  

Код состояния 200 (ОК) возвращается в случае успешного ответа, который содержит список затрат с подробными сведениями для учетной записи.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": ${usageQuantity},
        "billableQuantity": ${billableQuantity},
        "pretaxCost": ${cost},
        "meterId": "${meterID}",
        "meterDetails": ${meterDetails}
      }
    }
    ],
    "nextLink": "${nextLinkURL}"
} 
```  

Каждый элемент в разделе **value** представляет сведения об использовании службы:

|Свойство ответа|ОПИСАНИЕ|
|----------------|----------|
|**subscriptionGuid** | Глобальный уникальный идентификатор для подписки. | 
|**startDate** | Дата начала использования. |
|**endDate** | Дата окончания использования. |
|**usageQuantity** | Использованный объем. | 
|**billableQuantity** | Фактический объем, за который выставлен счет. |
|**pretaxCost** | Стоимость, указанная в счете-фактуре, без учета применимых налогов. | 
|**meterDetails** | Подробные сведения об использовании. |
|**nextLink**| Если этот параметр задан, в его значении указывается URL-адрес следующей страницы со сведениями. Если страница последняя, значение не указывается. |  
||
  
Этот пример приведен в сокращенном виде. Полное описание каждого поля ответа см. в разделе [сведений об использовании](https://docs.microsoft.com/rest/api/consumption/usagedetails/listbybillingperiod#usagedetailslistresult). 

Другие коды состояния означают состояния ошибки. В этих случаях объект ответа содержит описание с объяснением причины сбоя запроса.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>Дополнительная информация 
- Ознакомьтесь со статьей [Общие сведения о корпоративной отчетности](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Подробнее о [REST API выставления счетов для корпоративных клиентов](https://docs.microsoft.com/rest/api/billing/)   
- [Начало работы с Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
