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
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064615"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Проверка данных для выставления счетов по регистрации учетных записей корпоративных пользователей с помощью REST API

Интерфейсы API отчетов Azure позволяют проверять данные о расходах на Azure и управлять ими.

Из этой статьи вы узнаете, как получить текущий счет по регистрации учетных записей корпоративных пользователей.

Чтобы получить текущий счет, сделайте следующее:
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Создание запроса  

Параметр `{enrollmentID}` является обязательным и должен содержать идентификатор регистрации для корпоративной учетной записи.

Ниже приведены обязательные заголовки. 

|Заголовок запроса|ОПИСАНИЕ|  
|--------------------|-----------------|  
|*Content-Type:*|Обязательный элемент. Задайте значение `application/json`.|  
|*Authorization:*|Обязательный элемент. Принимает действующий [ключ API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) `Bearer`. |  

В этом примере показан синхронный вызов, который возвращает сведения о текущем цикле выставления счетов. Из соображений производительности синхронные вызовы возвращают сведения только за последний месяц.  Вы также можете обращаться к API [асинхронно](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based), чтобы получить данные за 36 месяцев.


## <a name="response"></a>Ответ  

Код состояния 200 (ОК) возвращается в случае успешного ответа, который содержит список расходов с подробными сведениями для учетной записи.

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

Каждый элемент в параметре **data** обозначает конкретные расходы.

|Свойство ответа|ОПИСАНИЕ|
|----------------|----------|
|**cost** | Сумма, подлежащая оплате, в валюте, используемой на территории, где расположен конкретный центр обработки данных. |
|**subscriptionGuid** | Глобальный уникальный идентификатор для подписки. | 
|**departmentId** | Идентификатор подразделения, если применимо. |
|**date** | Дата начисления этих расходов. |
|**теги** | Строка в формате JSON, которая содержит связанные с подпиской теги. |
|**resourceGroup**|Имя группы ресурсов, которая содержит объект, за который начислена плата. |
|**nextLink**| Если этот параметр задан, в его значении указывается URL-адрес следующей страницы со сведениями. Если страница последняя, значение не указывается. |  
||
  
Идентификаторы подразделений, группы ресурсов, теги и другие связанные с ними поля определяются администратором корпоративной учетной записи.  

Этот пример приведен в сокращенном виде. Полное описание каждого поля ответа см. в разделе [о получении сведений об использовании](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail). 

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
- Ознакомьтесь со статьей [Обзор API-интерфейсов отчетов для корпоративных клиентов](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Подробнее о [REST API выставления счетов для корпоративных клиентов](https://docs.microsoft.com/rest/api/billing/)   
- [Начало работы с Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
