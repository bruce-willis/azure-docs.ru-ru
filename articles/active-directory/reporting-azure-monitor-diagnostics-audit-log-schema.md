---
title: Интерпретация схемы журналов аудита Azure Active Directory в Azure Monitor (предварительная версия) | Документация Майкрософт
description: Описание схемы журналов аудита Azure AD для использования в Azure Monitor (предварительная версия).
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e1ae8e2a4dc9ef9c21300ebfc4df8c0f1c5819f2
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240597"
---
# <a name="interpret-the-azure-active-directory-audit-logs-schema-in-azure-monitor-preview"></a>Интерпретация схемы журналов аудита Azure Active Directory в Azure Monitor (предварительная версия)

В этой статье описывается схема журналов аудита Azure AD в Azure Monitor. Каждая отдельная запись журнала хранится как текст в формате большого двоичного объекта JSON, как показано в двух примерах ниже. 

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-17T00:14:31.2585575Z", 
        "operationName": "Change password (self-service)",
        "operationVersion": "1.0",
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "resultDescription": "None", 
        "durationMs": "-1", 
        "correlationId": "60d5e89a-b890-413f-9e25-a047734afe9f", 
        "identity": "sreens@wingtiptoysonline.com", 
        "Level": "Informational", 
        "location": "WUS", 
        "properties": { 
            "identityType": "UPN", 
            "operationType": "Update", 
            "additionalDetails": "None", 
            "additionalTargets": "", 
            "targetUpdatedProperties": "", 
            "targetResourceType": "UPN__TenantContextID__PUID__ObjectID__ObjectClass", 
            "targetResourceName": "sreens@wingtiptoysonline.com__bf85dc9d-cb43-44a4-80c4-469e8c58249e__1003BFFD9FEB17DB__7a408bdd-7d97-4574-8511-dd747b56465d__User", 
            "auditEventCategory": "UserManagement" 
        } 
    } 
    ] 
} 
```

```json
{ 
    "records": [ 
    { 
        "time": "2018-03-18T19:47:43.0368859Z", 
        "operationName": "Update service principal.", 
        "operationVersion": "1.0", 
        "category": "Audit", 
        "tenantId": "bf85dc9d-cb43-44a4-80c4-469e8c58249e", 
        "resultType": "Success", 
        "resultSignature": "-1", 
        "durationMs": "-1", 
        "callerIpAddress": "<null>", 
        "correlationId": "14916c7a-5a7d-44e8-9b06-74b49efb08ee", 
        "identity": "NA", 
        "Level": "Informational", 
        "properties": { 
            "identityType": "NA", 
            "operationType": "Update", 
            "additionalDetails": {}, 
            "additionalTargets": "", 
            "targetUpdatedProperties": [ 
            { 
                "Name": "Included Updated Properties", 
                "OldValue": null, 
                "NewValue": "" 
            }, 
            { 
                "Name": "TargetId.ServicePrincipalNames", 
                "OldValue": null, 
                "NewValue": "http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29" 
            } 
            ], 
        "targetResourceType": "Other__ObjectID__ObjectClass__Name__AppId__SPN", 
        "targetResourceName": "ServicePrincipal_ea70a262-4da3-440a-b396-9734ddfd9df2__ea70a262-4da3-440a-b396-9734ddfd9df2__ServicePrincipal__Salesforce__cd3ed3de-93ee-400b-8b19-b61ef44a0f29__http://adapplicationregistry.onmicrosoft.com/salesforce.com/primary;cd3ed3de-93ee-400b-8b19-b61ef44a0f29", 
        "auditEventCategory": "ApplicationManagement" 
      } 
    } 
    ] 
} 
```

| Имя поля | ОПИСАНИЕ |
|------------|-------------|
| Twitter в режиме реального       | Дата и время (в формате UTC). |
| operationName | Имя операции. |
| operationVersion | Запрошенная клиентом версия REST API. |
| category | В настоящее поддерживается только значение *Audit*. |
| tenantId | GUID клиента, связанный с журналами. |
| resultType | Результат операции: *Success* или *Failure*. |
| resultSignature |  Это поле не сопоставлено, и его можно игнорировать. | 
| resultDescription | Дополнительное описание результата, если оно доступно. | 
| durationMs |  Это поле не сопоставлено, и его можно игнорировать. |
| callerIpAddress | IP-адрес клиента, отправившего запрос. | 
| correlationId | Необязательный GUID, переданный клиентом. Он может помочь скоррелировать операции на стороне клиента с операциями на стороне сервера и удобен при трассировке журналов, в которых регистрируется работа служб. |
| identity | Удостоверение из маркера, предоставляемое при выполнении запроса. Это может быть учетная запись пользователя, учетная запись системы или субъект-служба. |
| level | Тип сообщения. Для журналов аудита всегда имеет значение *Informational*. |
| location | Расположение центра обработки данных. |
| properties | Список поддерживаемых свойств, относящихся к журналу аудита. Дополнительную информацию можно найти в таблице ниже. | 


| Имя свойства | ОПИСАНИЕ |
|---------------|-------------|
| AuditEventCategory | Тип события аудита. Может иметь значение *User Management*, *Application Management* и т. д.|
| Identity Type | Может иметь значение *Application* или *User*. |
| Тип операции | Может иметь значение *Add*, *Update*, *Delete* или *Other*. |
| Target Resource Type | Задает тип целевого ресурса, с которым выполнялась операция. Может иметь значение *Application*, *User*, *Role*, *Policy*. | 
| Target Resource Name | Имя целевого ресурса. Например, это может быть имя приложения, имя роли, имя участника-пользователя или имя субъекта-службы. |
| additionalTargets | Содержит список дополнительных свойств для конкретных операций. Например, для операции обновления *targetUpdatedProperties* содержит предыдущие и новые значения. | 

## <a name="next-steps"></a>Дополнительная информация

* [Интерпретация схемы журналов входа Azure Active Directory в Azure Monitor (предварительная версия)](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Дополнительные сведения о журналах диагностики Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Часто задаваемые вопросы и известные проблемы](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)