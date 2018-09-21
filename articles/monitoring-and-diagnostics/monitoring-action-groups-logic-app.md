---
title: Как активировать сложные действия с помощью оповещений Azure Monitor
description: Сведения о том, как создать действие приложения логики для обработки оповещений Azure Monitor.
author: dkamstra
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dukek
ms.component: alerts
ms.openlocfilehash: 3c1bff98debf426fc02dbd4518c0bb798d7ba96d
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576029"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Как активировать сложные действия с помощью оповещений Azure Monitor

Эта статья описывает, как настроить и активировать приложение логики, чтобы создать беседу в Microsoft Teams при срабатывании оповещения.

## <a name="overview"></a>Обзор
Когда активируется оповещение Azure Monitor, оно вызывает [группу действий](monitoring-action-groups.md). Группы действий позволяют активировать одно или несколько действий для уведомления других лиц об оповещении и устранения его причины.

Общий процесс приведен ниже:

-   Создание приложения логики для соответствующего типа оповещений.

-   Импорт схемы для соответствующего типа оповещений в приложение логики.

-   Определение поведения приложения логики.

-   Копирование конечной точки HTTP приложения логики в группу действий Azure.

Если требуется, чтобы приложение логики выполняло другое действие, процесс настройки аналогичен.

## <a name="create-an-activity-log-alert-administrative"></a>Создание оповещения журнала действий на портале администрирования

1.  В верхнем левом углу портала Azure выберите **Создать ресурс**.

2.  Найдите и выберите элемент **Приложение логики** и **Создать**.

3.  Присвойте **Имя** приложению логики, выберите значение **Группа ресурсов** и т. д.

    ![Создание приложения логики](media/monitoring-action-groups/create-logic-app-dialog.png "Создание приложения логики")

4.  Выберите **Создать**, чтобы создать приложение логики. Отображается всплывающее сообщение, указывающее на создание приложения логики. Выберите **Launch Resource** (Запустить ресурс), чтобы открыть **Конструктор Logic Apps**.

5.  Выберите триггер **При получении HTTP-запроса**.

    ![Триггеры приложения логики](media/monitoring-action-groups/logic-app-triggers.png "Триггеры приложения логики")

6.  Выберите **Изменить**, чтобы изменить триггер HTTP-запроса.

    ![Триггеры HTTP-запроса](media/monitoring-action-groups/http-request-trigger-shape.png "Триггеры HTTP-запроса")

7.  Выберите **Использовать пример полезной нагрузки, чтобы создать схему**.

    ![Использование примера полезных данных](media/monitoring-action-groups/use-sample-payload-button.png "Использование примера полезных данных")

8.  Скопируйте следующий пример схемы и вставьте его в диалоговое окно:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. Отображается всплывающее окно **Конструктор приложений логики** с напоминанием о том, что запрос, отправляемый в приложение логики, должен задавать в заголовке **Content-Type** значение **application/json**. Закройте всплывающее окно. Оповещение Azure Monitor задает заголовок.

    ![Задание заголовка Content-Type](media/monitoring-action-groups/content-type-header.png "Задание заголовка Content-Type")

10. Выберите **+** **Новый шаг**, а затем щелкните **Добавить действие**.

    ![Добавление действия](media/monitoring-action-groups/add-action.png "Добавление действия")

11. Найдите и выберите соединитель Microsoft Teams. Выберите действие **Microsoft Teams – Post message** (Microsoft Teams — публикация сообщения).

    ![Действия Microsoft Teams](media/monitoring-action-groups/microsoft-teams-actions.png "Microsoft Teams actions")

12. Настройте действие Microsoft Teams. **Конструктор Logic Apps** предлагает пройти проверку подлинности для учетной записи Office 365. Выберите значения параметров **Идентификатор команды** и **Идентификатор канала** для отправки сообщения.

13. Настройте сообщение, указав статический текст и ссылки на \<поля\> динамического содержимого. Скопируйте следующий текст и вставьте его в поле **Сообщение**:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Найдите и замените \<поля\> тегами динамического содержимого с такими же именами.

    > [!NOTE]
    > Имеются два динамических поля с именем **status**. Добавьте оба эти поля в сообщение. Используйте поле в контейнере свойств **activityLog** и удалите другое. Наведите указатель мыши на поле **status**, чтобы отобразить подсказку с полным именем поля, как показано на следующем снимке экрана:

    ![Действие Microsoft Teams: публикация сообщения](media/monitoring-action-groups/teams-action-post-message.png "Действие Microsoft Teams: публикация сообщения")

14. В верхней части окна **Конструктор Logic Apps** выберите **Сохранить**, чтобы сохранить приложение логики.

15. Откройте существующую группу действий и добавьте в нее действие, которое ссылается на приложение логики. При отсутствии группы действия создайте ее, как описано в статье [Создание групп действий и управление ими на портале Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups). Обязательно сохраните изменения.

    ![Обновление группы действий](media/monitoring-action-groups/update-action-group.png "Обновление группы действий")

При следующем вызове группы действий оповещением будет вызвано ваше приложение логики.

## <a name="create-a-service-health-alert"></a>Создание оповещения о работоспособности служб

Записи работоспособности служб Azure являются частью журнала действий. Процедура создания оповещения похожа на [создание предупреждения журнала действий](#create-an-activity-log-alert-administrative), но с некоторыми отличиями:

- Шаги с 1 по 7 остаются без изменений.
- В шаге 8 используйте следующий пример схемы для триггера HTTP-запроса:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "…",
                        "service": "…",
                        "region": "Global",
                        "communication": "…",
                        "incidentType": "Incident",
                        "trackingId": "…",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "…",
                        "defaultLanguageContent": "…",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Шаги 9 и 10 остаются без изменений.
-  В шагах с 11 по 14 используйте следующий процесс:

   1. Выберите **+** **Новый шаг**, а затем щелкните **Добавить условие**. Задайте следующие условия, чтобы приложение логики выполнялось, только когда входные данные соответствуют указанным ниже значениям.  При вводе значения версии в текстовое поле поместите его в кавычки ("0.1.1"), чтобы гарантировать, что оно будет вычисляться как строка, а не числовой тип.  Система не отображает кавычки, если вы вернетесь к странице, однако базовый код сохраняет строковый тип.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Условие полезных данных о работоспособности служб"](media/monitoring-action-groups/service-health-payload-condition.png "Условие полезных данных о работоспособности служб")

   1. В условии **Если истинно** выполните инструкции для шагов с 11 по 13 из раздела [Создание оповещения журнала действий](#create-an-activity-log-alert-administrative), чтобы добавить действие Microsoft Teams.

   1. Определите сообщение, используя сочетание кода HTML и динамического содержимого. Скопируйте приведенный ниже текст и вставьте его в поле **Сообщение**. Замените поля `[incidentType]`, `[trackingID]`, `[title]` и `[communication]` тегами динамического содержимого с таким же именем:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Действие публикации для условия true оповещения о работоспособности служб"](media/monitoring-action-groups/service-health-true-condition-post-action.png "Действие публикации для условия true оповещения о работоспособности служб")

   1. Для условия **Если ложно** введите имеющее смысл сообщение:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Действие публикации для условия false оповещения о работоспособности служб"](media/monitoring-action-groups/service-health-false-condition-post-action.png "Service Health false condition post action")

- Шаг 15 остается без изменений. Следуйте инструкциям, чтобы сохранить приложение логики и обновить группу действий.

## <a name="create-a-metric-alert"></a>Создание оповещения для метрики

Процедура создания оповещения для метрики похожа на [создание предупреждения для журнала действий](#create-an-activity-log-alert-administrative), но с некоторыми отличиями:

- Шаги с 1 по 7 остаются без изменений.
- В шаге 8 используйте следующий пример схемы для триггера HTTP-запроса:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "…",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "…",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "…",
        "portalLink": "…"
        },
        "properties": {}
    }
    }
    ```

- Шаги 9 и 10 остаются без изменений.
- В шагах с 11 по 14 используйте следующий процесс:

   1. Выберите **+** **Новый шаг**, а затем щелкните **Добавить условие**. Задайте следующие условия, чтобы приложение логики выполнялось, только когда входные данные соответствуют указанным ниже значениям. При вводе значения версии в текстовое поле поместите его в кавычки ("2.0"), чтобы гарантировать, что оно будет вычисляться как строка, а не числовой тип.  Система не отображает кавычки, если вы вернетесь к странице, однако базовый код сохраняет строковый тип. 
       - `schemaId == AzureMonitorMetricAlert`
       - `version == "2.0"`
       
       !["Условие для полезных данных оповещения метрики"](media/monitoring-action-groups/metric-alert-payload-condition.png "Metric alert payload condition")

   1. В условии **Если истинно** добавьте цикл **For each** и действие Microsoft Teams. Определите сообщение, используя сочетание кода HTML и динамического содержимого.

       !["Действие публикации для условия true оповещения метрики"](media/monitoring-action-groups/metric-alert-true-condition-post-action.png "Metric alert true condition post action")

   1. В условии **Если ложно** определите действие Microsoft Teams, которое сообщает, что оповещение метрики не соответствует ожиданиям приложения логики. Включите полезные данные JSON. Обратите внимание на то, как указываются ссылки на динамическое содержимое `triggerBody` в выражении `json()`.

       !["Действие публикации для условия false оповещения метрики"](media/monitoring-action-groups/metric-alert-false-condition-post-action.png "Metric alert false condition post action")

- Шаг 15 остается без изменений. Следуйте инструкциям, чтобы сохранить приложение логики и обновить группу действий.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Вызов других приложений помимо Microsoft Teams
Служба Logic Apps имеет ряд различных соединителей, которые позволяют активировать действия в самых разных приложениях и базах данных. К таким приложениям, например, относятся Slack, SQL Server, Oracle, Salesforce. Дополнительные сведения о соединителях Azure Logic Apps см. в [этой статье](../connectors/apis-list.md).  

## <a name="next-steps"></a>Дополнительная информация
* Изучите [обзор оповещений журнала действий Azure](monitoring-overview-alerts.md) и узнайте, как получать оповещения.  
* Узнайте, как [настроить оповещения при поступлении уведомлений о Работоспособности служб Azure](monitoring-activity-log-alerts-on-service-notifications.md).
* Дополнительные сведения о группах действий см. в статье [Создание групп действий и управление ими на портале Azure](monitoring-action-groups.md).
