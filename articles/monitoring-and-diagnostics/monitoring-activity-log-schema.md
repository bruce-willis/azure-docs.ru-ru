---
title: Схема событий журнала действий Azure
description: Общие сведения о схеме событий для данных, генерируемых в журнал действий
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 4/12/2018
ms.author: dukek
ms.component: activitylog
ms.openlocfilehash: 9c1f4699f067ece3108813d28ff834c68f44316d
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40003837"
---
# <a name="azure-activity-log-event-schema"></a>Схема событий журнала действий Azure
**Журнал действий Azure** — это журнал с подробными сведениями о событиях на уровне подписки, которые произошли в Azure. В этой статье описывается схема событий по категориям данных. Схема данных зависит от того, считываются ли данные через портал, PowerShell, CLI или напрямую через REST API и [передачу потока данных в хранилище или концентраторы событий с помощью профиля журнала](./monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile). В примерах ниже приведена схема, доступная на портале, в PowerShell, CLI и REST API. Сопоставление этих свойств со [схемой журналов системы диагностики Azure](./monitoring-diagnostic-logs-schema.md) приведено в конце статьи.

## <a name="administrative"></a>Administrative
Эта категория содержит записи всех операций создания, обновления, удаления и других действий, которые выполняются через Resource Manager. В качестве примеров типов событий, которые относятся к этой категории, можно назвать "создание виртуальной машины" или "удаление группы безопасности сети". Каждое действие, выполняемое пользователем или приложением с помощью Resource Manager, моделируется как операция с определенным типом ресурсов. Если тип операции — "запись", "удаление" или "действие", то любые сведения об этой операции (о ее запуске, успешном выполнении или сбое) записываются в категорию "Административная". Категория "Административная" также включает в себя любые изменения в управлении доступом на основе ролей, которые происходят в подписке.

### <a name="sample-event"></a>Пример события
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Описания свойств
| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| authorization |BLOB-объект со свойствами RBAC события. Обычно включает следующие свойства: action, role и scope. |
| caller |Адрес электронной почты пользователя, который выполнил операцию, утверждение имени субъекта-службы или имени участника-пользователя в зависимости от доступности. |
| каналов |Одно из следующих значений: Admin или Operation. |
| claims |Токен JWT, который используется Active Directory для аутентификации пользователя или приложения при выполнении этой операции в Resource Manager. |
| correlationId |Обычно GUID в строковом формате. События, которые совместно используют идентификатор correlationId, принадлежат к одному общему действию. |
| Description (Описание) |Статическое описание события в текстовом виде. |
| eventDataId |Уникальный идентификатор события. |
| httpRequest |Большой двоичный объект, описывающий HTTP-запрос. Обычно включает clientRequestId, clientIpAddress и method (метод HTTP, например PUT). |
| level |Уровень события. Одно из таких значений: Critical, Error, Warning, Informational. |
| имя_группы_ресурсов |Имя группы ресурсов для затронутого ресурса. |
| resourceProviderName |Имя поставщика ресурса для затронутого ресурса. |
| ResourceId |Идентификатор ресурса для затронутого ресурса. |
| operationId |События, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |
| status |Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |Обычно содержит код состояния HTTP для соответствующего вызова REST, но может содержать и другие строковые описания подсостояния, например: OK (код состояния HTTP: 200); Created (код состояния HTTP: 201); Accepted (код состояния HTTP: 202); No Content (код состояния HTTP: 204); Bad Request (код состояния HTTP: 400); Not Found (код состояния HTTP: 404); Conflict (код состояния HTTP: 409); Internal Server Error (код состояния HTTP: 500); Service Unavailable (код состояния HTTP: 503); Gateway Timeout (код состояния HTTP: 504). |
| eventTimestamp |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |

## <a name="service-health"></a>Работоспособность службы
Эта категория содержит записи всех инцидентов, связанных с работоспособностью службы, которые произошли в Azure. В качестве примера типа события из этой категории можно назвать следующее: "В работе SQL Azure в восточной части США наблюдаются простои". Существует шесть разновидностей событий работоспособности службы: "Требуется действие", "Помощь при восстановлении", "Инцидент", "Обслуживание", "Сведения" и "Безопасность". Они отображаются, только если в подписке есть ресурс, на который повлияет данное событие.

### <a name="sample-event"></a>Пример события
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Дополнительные сведения о значениях в свойствах см. в статье об [уведомлениях о работоспособности службы](./monitoring-service-notifications.md).

## <a name="alert"></a>Предупреждение
Эта категория содержит записи всех активаций оповещений Azure. В качестве примера типа события из этой категории можно назвать следующее: "Процент использования ЦП на виртуальной машине myVM за последние 5 минут превышал 80 %". Различные системы Azure работают по принципу оповещений, то есть вы можете определить какое-то правило, и система будет направлять вам уведомление, когда условия этого правила выполняются. Каждый раз, когда "активируется" поддерживаемый тип оповещения Azure или выполняются условия для создания уведомления, в эту категорию журнала активности также передается запись об активации.

### <a name="sample-event"></a>Пример события

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Описания свойств
| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| caller | Всегда имеет значение Microsoft.Insights/alertRules. |
| каналов | Всегда имеет значение "Admin, Operation". |
| claims | Большой двоичный объект JSON с именем субъекта-службы (SPN) или типом ресурса обработчика предупреждений. |
| correlationId | Глобальный уникальный идентификатор (GUID) в строковом формате. |
| description |Статическое текстовое описание события оповещения. |
| eventDataId |Уникальный идентификатор события оповещения. |
| level |Уровень события. Одно из таких значений: Critical, Error, Warning, Informational. |
| имя_группы_ресурсов |Имя группы ресурсов для затронутого ресурса, если это оповещение метрики. Для других типов оповещений это имя группы ресурсов, содержащей само оповещение. |
| resourceProviderName |Имя поставщика ресурсов для затронутого ресурса, если это оповещение метрики. Для других типов оповещений это имя поставщика ресурсов для самого оповещения. |
| ResourceId | Имя идентификатора ресурса для затронутого ресурса, если это оповещение метрики. Для других типов оповещений это идентификатор ресурса самого ресурса оповещения. |
| operationId |События, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |
| status |Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Обычно для оповещений имеет значение null. |
| eventTimestamp |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |

### <a name="properties-field-per-alert-type"></a>Поле свойств по типу оповещения
Поле свойств будет содержать разные значения в зависимости от источника события оповещения. Два распространенных поставщика событий оповещений — оповещения журнала действий и оповещения метрик.

#### <a name="properties-for-activity-log-alerts"></a>Свойства оповещений журнала действий
| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| properties.subscriptionId | Идентификатор подписки из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.eventDataId | Идентификатор данных события из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.resourceGroup | Идентификатор группы ресурсов из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.resourceId | Идентификатор ресурса из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.eventTimestamp | Метка времени события из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.operationName | Имя операции из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий. |
| properties.status | Состояние из события журнала действий, которое вызвало активацию данного правила оповещения журнала действий.|

#### <a name="properties-for-metric-alerts"></a>Свойства оповещений метрик
| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| properties.RuleUri | Идентификатор ресурса самого правила оповещения метрики. |
| properties.RuleName | Имя правила оповещения метрики. |
| properties.RuleDescription | Описание правила оповещения метрики (как указано в правиле оповещения). |
| properties.Threshold | Пороговое значение, используемое для оценки правила оповещения метрики. |
| properties.WindowSizeInMinutes | Размер окна, используемый для оценки правила оповещения метрики. |
| properties.Aggregation | Тип агрегата, определенный в правиле оповещения метрики. |
| properties.Operator | Условный оператор, используемый для оценки правила оповещения метрики. |
| properties.MetricName | Имя метрики, используемой для оценки правила оповещения метрики. |
| properties.MetricUnit | Единица измерения метрики, используемая для оценки правила оповещения метрики. |

## <a name="autoscale"></a>Autoscale
Эта категория содержит записи всех событий, связанных с операциями системы автоматического масштабирования, в основе которой лежат параметры автомасштабирования, определенные в подписке. В качестве примера типа события из этой категории можно назвать следующее: "Не удалось выполнить автоматическое увеличение масштаба". С помощью функции автомасштабирования можно автоматически увеличивать или уменьшать число экземпляров в поддерживаемом типе ресурсов на основе времени суток и (или) загружать данные (метрики), используя параметр автомасштабирования. Когда выполняются условия для увеличения или уменьшения масштаба, в эту категорию записываются соответствующие события (запуск, успешное выполнение или сбой).

### <a name="sample-event"></a>Пример события
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Описания свойств
| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| caller | Всегда имеет значение Microsoft.Insights/autoscaleSettings. |
| каналов | Всегда имеет значение "Admin, Operation". |
| claims | Большой двоичный объект JSON с именем субъекта-службы (SPN) или типом ресурса системы автомасштабирования. |
| correlationId | Глобальный уникальный идентификатор (GUID) в строковом формате. |
| description |Статическое текстовое описание события автомасштабирования. |
| eventDataId |Уникальный идентификатор события автомасштабирования. |
| level |Уровень события. Одно из таких значений: Critical, Error, Warning, Informational. |
| имя_группы_ресурсов |Имя группы ресурсов для параметра автомасштабирования. |
| resourceProviderName |Имя поставщика ресурсов для параметра автомасштабирования. |
| ResourceId |Идентификатор ресурса параметра автомасштабирования. |
| operationId |События, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. |
| properties.Description | Подробное описание действий, выполненных системой автоматического масштабирования. |
| properties.ResourceName | Идентификатор ресурса для затронутого ресурса (ресурса, в отношении которого выполнялось действие масштабирования). |
| properties.OldInstancesCount | Число экземпляров до выполнения действия автомасштабирования. |
| properties.NewInstancesCount | Число экземпляров после выполнения действия автомасштабирования. |
| properties.LastScaleActionTime | Метка времени, когда произошло действие автоматического масштабирования. |
| status |Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Обычно для автоматического масштабирования имеет значение null. |
| eventTimestamp |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |

## <a name="security"></a>Безопасность
Эта категория содержит запись любых предупреждений, созданных центром безопасности Azure. Примером типа события в этой категории является "Выполнен подозрительный файл с двойным расширением".

### <a name="sample-event"></a>Пример события
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Описания свойств
| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| каналов | Всегда значение Operation. |
| correlationId | Глобальный уникальный идентификатор (GUID) в строковом формате. |
| description |Статическое текстовое описание события безопасности. |
| eventDataId |Уникальный идентификатор события безопасности. |
| eventName |Понятное имя события безопасности. |
| id |Уникальный идентификатор ресурса события безопасности. |
| level |Уровень события. Одно из таких значений: Critical, Error, Warning или Informational. |
| имя_группы_ресурсов |Имя группы ресурсов для ресурса. |
| resourceProviderName |Имя поставщика ресурсов для центра безопасности Azure. Всегда значение Microsoft.Security. |
| тип_ресурса |Тип ресурса, создавшего событие безопасности, например Microsoft.Security/locations/alerts. |
| ResourceId |Идентификатор ресурса оповещения системы безопасности. |
| operationId |События, относящиеся к одной операции, совместно используют один GUID. |
| operationName |Имя операции. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием события. Эти свойства будут различаться в зависимости от типа оповещения системы безопасности. [Здесь](../security-center/security-center-alerts-type.md) описаны типы предупреждений, поступающих из центра безопасности. |
| properties.Severity |Уровень серьезности. Возможные значения: High, Medium или Low. |
| status |Строка, описывающая состояние операции. Обычные значения: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Обычно имеет значение null для событий безопасности. |
| eventTimestamp |Метка времени, когда служба Azure создала событие при обработке соответствующего этому событию запроса. |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |

## <a name="recommendation"></a>Рекомендации
Эта категория содержит запись о любых новых рекомендациях, которые создаются для служб. Примером рекомендации будет "использование группы доступности для повышения отказоустойчивости". Существует 4 типа событий рекомендации, которые могут быть созданы: высокий уровень доступности, производительности, безопасности и оптимизации затрат. 

### <a name="sample-event"></a>Пример события
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Описания свойств
| Имя элемента | ОПИСАНИЕ |
| --- | --- |
| каналов | Всегда значение Operation. |
| correlationId | Глобальный уникальный идентификатор (GUID) в строковом формате. |
| description |Статическое текстовое описание события рекомендации |
| eventDataId | Уникальный идентификатор события рекомендации. |
| category | Всегда "Рекомендация" |
| id |Уникальный идентификатор ресурса события рекомендации. |
| level |Уровень события. Одно из таких значений: Critical, Error, Warning или Informational. |
| operationName |Имя операции.  Всегда "Microsoft.Advisor/generateRecommendations/action"|
| имя_группы_ресурсов |Имя группы ресурсов для ресурса. |
| resourceProviderName |Имя поставщика ресурсов для ресурса, к которому применяется данная рекомендация, например "MICROSOFT.COMPUTE" |
| тип_ресурса |Имя типа ресурсов для ресурса, к которому применяется данная рекомендация, например "MICROSOFT.COMPUTE/virtualmachines" |
| ResourceId |Идентификатор ресурса для ресурса, к которому применяется рекомендация |
| status | Всегда "Active" |
| submissionTimestamp |Метка времени, когда событие стало доступно для запросов. |
| subscriptionId |Идентификатор подписки Azure. |
| properties |Набор пар `<Key, Value>` (например, Dictionary) c подробным описанием рекомендации.|
| properties.recommendationSchemaVersion| Версия схемы свойств рекомендации, опубликована в записи журнала действий |
| properties.recommendationCategory | Категория рекомендации. Возможные значения: "Высокая доступность", "Производительность", "Безопасность" и "Стоимость" |
| properties.recommendationImpact| Влияние рекомендации Возможные значения: High, Medium или Low |
| properties.recommendationRisk| Риск рекомендации. Возможные значения: Error, Warning, None |

## <a name="mapping-to-diagnostic-logs-schema"></a>Сопоставление со схемой журналов диагностики

При потоковой передаче журнала действий Azure в учетную запись хранения или пространство имен концентраторов событий данные соответствуют [схеме журналов системы диагностики Azure](./monitoring-diagnostic-logs-schema.md). Ниже приведено сопоставление свойств приведенной выше схемы и схемы журналов диагностики:

| Свойство схемы журналов диагностики | Свойство схемы журнала действий REST API | Примечания |
| --- | --- | --- |
| Twitter в режиме реального | eventTimestamp |  |
| ResourceId | ResourceId | Параметры subscriptionId, resourceType и resourceGroupName получаются из параметра resourceId. |
| operationName | operationName.value |  |
| category | Часть имени операции | Разделитель типа операции — "Запись", "Удаление" или "Действие" |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMs | Недоступно | Всегда 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | свойства удостоверений и авторизации |  |
| Уровень | Уровень |  |
| location | Недоступно | Расположение, где было обработано событие. *Это не расположение ресурса, а место, где событие было обработано. Это свойство будет удалено в будущем обновлении.* |
| properties | properties.eventProperties |  |
| properties.eventCategory | category | Если свойство properties.eventCategory не задано, параметр category равен "Административная" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>Дополнительная информация
* [Дополнительные сведения о журнале действий (прежнее название — журналы аудита)](monitoring-overview-activity-logs.md)
* [Потоковая передача журнала действий Azure в концентраторы событий](monitoring-stream-activity-logs-event-hubs.md)
