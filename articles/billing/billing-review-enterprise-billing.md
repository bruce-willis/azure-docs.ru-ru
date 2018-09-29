---
title: Проверка данных для выставления счетов по регистрации учетных записей Azure корпоративных пользователей с помощью REST API | Документация Майкрософт
description: Узнайте, как применить REST API Azure для проверки данных для выставления счетов по регистрации учетных записей корпоративных пользователей.
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
ms.author: erikre
ms.openlocfilehash: f4b5fccefb08597a13fcc91eda11bbf3eb122914
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394852"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Проверка данных для выставления счетов по регистрации учетных записей корпоративных пользователей с помощью REST API

Интерфейсы API отчетов Azure позволяют проверять данные о расходах на Azure и управлять ими.

В этой статье вы узнаете, как получать данные для выставления счетов, связанные с учетными записями счетов, отделов или соглашениями предприятий (EA), используя API Azure REST. 

## <a name="individual-account-billing"></a>Отдельная учетная запись выставления счетов

Чтобы получить сведения об использовании учетных записей пользователей в подразделении, выполните следующую команду.

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Параметр `{billingAccountId}` является обязательным и должен содержать идентификатор регистрации для учетной записи.

Ниже приведены обязательные заголовки. 

|Заголовок запроса|ОПИСАНИЕ|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный элемент. Задайте значение `application/json`.|  
|*Authorization:*|Обязательный элемент. Принимает действующий [ключ API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

В этом примере показан синхронный вызов, который возвращает сведения о текущем цикле выставления счетов. Из соображений производительности синхронные вызовы возвращают сведения только за последний месяц.  Вы также можете обращаться к API [асинхронно](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), чтобы получить данные за 36 месяцев.


## <a name="response"></a>Ответ  

Код состояния 200 (ОК) возвращается в случае успешного ответа, который содержит список затрат с подробными сведениями для учетной записи.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Этот пример приведен в сокращенном виде. Полное описание каждого поля ответа и обработки ошибок см. в разделе [Usage Details - List By Billing Account](/rest/api/consumption/usagedetails/listbybillingaccount) (Подробные сведения об использовании, список учетной записи выставления счетов).

## <a name="department-billing"></a>Выставление счетов в отделе 

Получение сведений об использовании, объединенных для всех учетных записей в отделе. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Параметр `{departmentId}` является обязательным и должен содержать идентификатор отдела в учетной записи регистрации.

Ниже приведены обязательные заголовки. 

|Заголовок запроса|ОПИСАНИЕ|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный элемент. Задайте значение `application/json`.|  
|*Authorization:*|Обязательный элемент. Принимает действующий [ключ API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

В этом примере показан синхронный вызов, который возвращает сведения о текущем цикле выставления счетов. Из соображений производительности синхронные вызовы возвращают сведения только за последний месяц.  Вы также можете обращаться к API [асинхронно](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), чтобы получить данные за 36 месяцев.

### <a name="response"></a>Ответ  

Код состояния 200 (ОК) возвращается для успешного ответа, который содержит список подробной информации об использовании и стоимости за данный расчетный период и идентификатор счета для отдела.


В следующем примере показано выходные данные API REST для отдела `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Этот пример приведен в сокращенном виде. Полное описание каждого поля ответа и обработки ошибок см. в разделе [Usage Details - List By Department](/rest/api/consumption/usagedetails/listbydepartment) (Подробные сведения об использовании, список для отдела).

## <a name="enrollment-account-billing"></a>Выставление счетов учетной записи регистрации

Получение сведений об использовании, объединенных для учетной записи регистрации.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Параметр `{enrollmentAccountId}` является обязательным и должен содержать идентификатор учетной записи регистрации.

Ниже приведены обязательные заголовки. 

|Заголовок запроса|ОПИСАНИЕ|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный элемент. Задайте значение `application/json`.|  
|*Authorization:*|Обязательный элемент. Принимает действующий [ключ API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

В этом примере показан синхронный вызов, который возвращает сведения о текущем цикле выставления счетов. Из соображений производительности синхронные вызовы возвращают сведения только за последний месяц.  Вы также можете обращаться к API [асинхронно](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), чтобы получить данные за 36 месяцев.

### <a name="response"></a>Ответ  

Код состояния 200 (ОК) возвращается для успешного ответа, который содержит список подробной информации об использовании и стоимости за данный расчетный период и идентификатор счета для отдела.

В следующем примере показано выходные данные API REST для отдела `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Этот пример приведен в сокращенном виде. Полное описание каждого поля ответа и обработки ошибок см. в разделе [Usage Details - List By Enrollment Account](/rest/api/consumption/usagedetails/listbyenrollmentaccount) (Подробные сведения об использовании, список для учетной записи регистрации).

## <a name="next-steps"></a>Дополнительная информация 
- Ознакомьтесь со статьей [Обзор API-интерфейсов отчетов для корпоративных клиентов](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Подробнее о [REST API выставления счетов для корпоративных клиентов](https://docs.microsoft.com/rest/api/billing/)   
- [Начало работы с Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
