---
title: Расширение оповещений из Log Analytcs в Azure
description: В этой статье описываются средства и API, с помощью которых можно расширить оповещения из Log Analytics в оповещения Azure.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 21ba95a7b3efff177afe63d22da3f6ba9848ded2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301037"
---
# <a name="extend-alerts-from-log-analytics-into-azure-alerts"></a>Расширение оповещений из Log Analytics в оповещения Azure
В службе анализа журналов Azure функция оповещения заменяется на оповещения Azure. В рамках этого процесса оповещения, которые были изначально настроены в Log Analytics, будут расширены в Azure. Если вы не хотите ждать, когда они будут перенесены в Azure автоматически, то процесс инициализации можно запустить:

- Вручную, с портала Operations Management Suite. 
- Программным образом, с помощью API AlertsVersion.  

> [!NOTE]
> Корпорация Майкрософт автоматически расширит предупреждения, созданные в Log Analytics для оповещений Azure начиная с 14 мая 2018 г., в повторяющейся серии до завершения. Корпорация Майкрософт определяет время миграции оповещений в Azure, и в процессе перехода ими можно управлять как с портала Operations Management Suite, так и с портала Azure. Процесс не является разрушительным или непрерывным.  

## <a name="option-1-initiate-from-the-operations-management-suite-portal"></a>Вариант 1. Запуск с портала Operations Management Suite
Ниже описано, как отображать оповещения для рабочей области на портале Operations Management Suite.  

1. На портале Azure щелкните **Все службы**. В списке ресурсов введите **Log Analytics**. Как только вы начнете вводить символы, список отфильтруется соответствующим образом. Выберите **Log Analytics**.
2. В области подписок Log Analytics выберите рабочую область, а затем выберите плитку **Портал OMS**.
![Снимок экрана с выделенной плиткой портала OMS области подписки Log Analytics](./media/monitor-alerts-extend/azure-portal-01.png) 
3. После того как вас перенаправили на портал Operations Management Suite, необходимо выбрать значок **Параметры**.
![Снимок экрана с выделенным значком "Параметры" на портале Operations Management Suite](./media/monitor-alerts-extend/oms-portal-settings-option.png) 
4. На странице **Параметры** выберите **Оповещения**.  
5. Выберите **Extend into Azure** (Расширить в Azure).
![Снимок экрана страницы параметров оповещений портала Operations Management Suite с выделенным расширением в Azure](./media/monitor-alerts-extend/ExtendInto.png)
6. В области **Оповещения** появится мастер, который состоит из 3 шагов. Ознакомьтесь с общими сведениями и нажмите кнопку **Далее**.
![Снимок экрана первого шага мастера](./media/monitor-alerts-extend/ExtendStep1.png)  
7. На втором шаге можно просмотреть сводку предлагаемых изменений, перечислив соответствующие [Группы действий](monitoring-action-groups.md) для оповещений. Если подобные действия будут выполняться для нескольких оповещений, мастер предложит связать одну группу действий со всеми остальными.  Соглашение об именовании соответствует следующему формату: *WorkspaceName_AG_#Number*. Чтобы продолжить, нажмите кнопку **Далее**.
![Снимок экрана второго шага мастера](./media/monitor-alerts-extend/ExtendStep2.png)  
8. На последнем шаге мастера нажмите кнопку **Готово** и при появлении запроса подтвердите его. (Необязательно.) Чтобы получать уведомления о завершении процесса и об успешном перемещении всех оповещений в оповещения Azure, необходимо предоставить адрес электронной почты.
![Снимок экрана третьего шага мастера](./media/monitor-alerts-extend/ExtendStep3.png)

После завершения работы мастера параметр расширения оповещений в Azure будет удален со страницы **Параметры оповещений**. Оповещения перемещаются в Azure в фоновом режиме, на что может потребоваться некоторое время. Во время проведения этой операции делать изменения оповещений на портале Operations Management Suite запрещено. Текущее состояние операции можно увидеть на баннере в верхней части портала. Если ранее вы предоставили адрес электронной почты, то при завершении процесса вы получите уведомление.  


Даже после того, как оповещения были успешно перемещены в Azure, они все еще будут отображаться на портале Operations Management Suite.
![Снимок экрана страницы "Параметры оповещений" портала Operations Management Suite](./media/monitor-alerts-extend/PostExtendList.png)


## <a name="option-2-use-the-alertsversion-api"></a>Вариант 2. Использование API AlertsVersion
Для расширения оповещений из Log Analytics в оповещения Azure из любого клиента, с которого можно выполнить вызов REST API, можно использовать API AlertsVersion Log Analytics. Использовав [ARMClient](https://github.com/projectkudu/ARMClient) — инструмент командной строки с открытым исходным кодом, можно получить доступ к API из PowerShell. Вывод результатов можно выполнить в формате JSON.  

Перед использованием API необходимо создать запрос GET. С помощью этого запроса можно оценить и возвратить сводку предложенных изменений, не предпринимая фактической попытки расширения в Azure с помощью запроса POST. Список результатов оповещений и предложенный список [Группы действий](monitoring-action-groups.md) находятся в формате JSON. Если подобные действия будут выполняться для нескольких оповещений, служба предложит связать одну группу действий со всеми остальными. Соглашение об именовании соответствует следующему формату: *WorkspaceName_AG_#Number*.

```
armclient GET  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Если запрос GET выполнен успешно, то в качестве данных формата JSON будет возвращен код состояния HTTP со значением 200 вместе со списком оповещений и предложенных групп действий. Далее представлен пример ответа на запрос:

```json
{
    "version": 1,
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}

```
Если в указанной рабочей области не установлено никаких определенных правил, в формате JSON будут представлены следующие данные:

```json
{
    "version": 1,
    "Message": "No Alerts found in the workspace for migration."
}
```

Если все правила оповещений в указанной рабочей области уже были расширены в Azure, на запрос GET будет получен следующий ответ:

```json
{
    "version": 2
}
```

Чтобы начать миграцию оповещений в Azure, инициируйте ответ POST. Ответ POST подтверждает выбранные намерения, также как и принятие условия, чтобы оповещения могли быть расширены с Log Analytics в оповещения Azure. Основываясь на результатах, полученных ранее во время выполнения ответа GET, действие выполняется по расписанию и оповещения обрабатываются так, как было сказано. При необходимости можно предоставить список адресов электронной почты, на которые Log Analytics отправит отчет об успешном завершении запланированного фонового процесса миграции оповещений. Можно использовать следующий пример запроса.

```
$emailJSON = “{‘Recipients’: [‘a@b.com’, ‘b@a.com’]}”
armclient POST  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $emailJSON
```

> [!NOTE]
> Результат миграции оповещений в оповещения Azure может изменяться в зависимости от сводной информации, предоставленной ответом GET. Оповещения, которые находятся в Log Analytics, временно недоступны для изменения на портале Operations Management Suite во время планирования. Тем не менее оповещение можно создать. 

При успешном выполнении запроса POST он возвращает состояние HTTP 200 OK вместе со следующим ответом:

```json
{
    "version": 2
}
```

Он указывает, что оповещение было успешно расширено в оповещения Azure. Эта версия свойства используется, только чтобы проверить, было ли оповещение расширено в Azure и является ли оно связанным с [API поиска по службе Log Analytics](../log-analytics/log-analytics-api-alerts.md). При успешном расширении оповещений в Azure отчет будет отправлен на все адреса электронной почты, которые входят в запрос POST. Если все оповещения в указанной рабочей области уже запланированы для перемещения, ответом на запрос POST будет сообщение о том, что попытка перемещения запрещена (код состояния 403). Чтобы просмотреть сообщения об ошибке или понять, на каком этапе процесса возникли проблемы, можно отправить запрос GET. Если в качестве ответа будет получено сообщение об ошибке, оно будет возвращено вместе со сводными сведениями.

```json
{
    "version": 1,
    "message": "OMS was unable to extend your alerts into Azure, Error: The subscription is not registered to use the namespace 'microsoft.insights'. OMS will schedule extending your alerts, once remediation steps illustrated in the troubleshooting guide are done.",
    "recipients": [
       "john.doe@email.com",
       "jane.doe@email.com"
     ],
    "migrationSummary": {
        "alertsCount": 2,
        "actionGroupsCount": 2,
        "alerts": [
            {
                "alertName": "DemoAlert_1",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_1"
            },
            {
                "alertName": "DemoAlert_2",
                "alertId": " /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/savedSearches/<savedSearchId>/schedules/<scheduleId>/actions/<actionId>",
                "actionGroupName": "<workspaceName>_AG_2"
            }
        ],
        "actionGroups": [
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                "actions": {
                    "emailIds": [
                        "JohnDoe@mail.com"
                    ],
                    "webhookActions": [
                        {
                            "name": "Webhook_1",
                            "serviceUri": "http://test.com"
                        }
                    ],
                    "itsmAction": {}
                }
            },
            {
                "actionGroupName": "<workspaceName>_AG_1",
                "actionGroupResourceId": "/subscriptions/<subscriptionid>/resourceGroups/<resourceGroupName>/providers/microsoft.insights/actionGroups/<workspaceName>_AG_1",
                 "actions": {
                    "emailIds": [
                        "test1@mail.com",
                          "test2@mail.com"
                    ],
                    "webhookActions": [],
                    "itsmAction": {
                        "connectionId": "<Guid>",
                        "templateInfo":"{\"PayloadRevision\":0,\"WorkItemType\":\"Incident\",\"UseTemplate\":false,\"WorkItemData\":\"{\\\"contact_type\\\":\\\"email\\\",\\\"impact\\\":\\\"3\\\",\\\"urgency\\\":\\\"2\\\",\\\"category\\\":\\\"request\\\",\\\"subcategory\\\":\\\"password\\\"}\",\"CreateOneWIPerCI\":false}"
                    }
                }
            }
        ]
    }
}              

```


## <a name="option-3-use-a-custom-powershell-script"></a>Вариант 3. Использование настраиваемого сценария PowerShell
 Если расширение оповещений с портала Operations Management Suite в Microsoft Azure завершилось ошибкой, то до 5 июля 2018 г. это можно сделать вручную. Предыдущие разделы охватывают информацию о двух вариантах для ручного расширения.

После 5 июля 2018 г. все оповещения с портала Operations Management Suite расширены в Azure. У пользователей, которые не приняли [предложенные действия, которые необходимы для исправления](#troubleshooting), оповещения будут запускаться без предварительных действий или уведомлений, в связи с отсутствием связанных [групп действий](monitoring-action-groups.md). 

Для ручного создания оповещений [групп действий](monitoring-action-groups.md) в Log Analytics используйте следующие примеры сценария.
```PowerShell
########## Input Parameters Begin ###########


$subscriptionId = ""
$resourceGroup = ""
$workspaceName = "" 


########## Input Parameters End ###########

armclient login

try
{
    $workspace = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/"$workspaceName"?api-version=2015-03-20 | ConvertFrom-Json
    $workspaceId = $workspace.properties.customerId
    $resourceLocation = $workspace.location
}
catch
{
    "Please enter valid input parameters i.e. Subscription Id, Resource Group and Workspace Name !!"
    exit
}

# Get Extend Summary of the Alerts
"`nGetting Extend Summary of Alerts for the workspace...`n"
try
{

    $value = armclient get /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview

    "Extend preview summary"
    "=========================`n"

    $value

    $result = $value | ConvertFrom-Json
}
catch
{

    $ErrorMessage = $_.Exception.Message
    "Error occured while fetching/parsing Extend summary: $ErrorMessage"
    exit 
}

if ($result.version -eq 2)
{
    "`nThe alerts in this workspace have already been extended to Azure."
    exit
}

$in = Read-Host -Prompt "`nDo you want to continue extending the alerts to Azure? (Y/N)"

if ($in.ToLower() -ne "y")
{
    exit
} 


# Check for resource provider registration
try
{
    $val = armclient get subscriptions/$subscriptionId/providers/microsoft.insights/?api-version=2017-05-10 | ConvertFrom-Json
    if ($val.registrationState -eq "NotRegistered")
    {
        $val = armclient post subscriptions/$subscriptionId/providers/microsoft.insights/register/?api-version=2017-05-10
    }
}
catch
{
    "`nThe user does not have required access to register the resource provider. Please try with user having Contributor/Owner role in the subscription"
    exit
}

$actionGroupsMap = @{}
try
{
    "`nCreating new action groups for alerts extension...`n"
    foreach ($actionGroup in $result.migrationSummary.actionGroups)
    {
        $actionGroupName = $actionGroup.actionGroupName
        $actions = $actionGroup.actions
        if ($actionGroupsMap.ContainsKey($actionGroupName))
        {
            continue
        } 
        
        # Create action group payload
        $shortName = $actionGroupName.Substring($actionGroupName.LastIndexOf("AG_"))
        $properties = @{"groupShortName"= $shortName; "enabled" = $true}
        $emailReceivers = New-Object Object[] $actions.emailIds.Count
        $webhookReceivers = New-Object Object[] $actions.webhookActions.Count
        
        $count = 0
        foreach ($email in $actions.emailIds)
        {
            $emailReceivers[$count] = @{"name" = "Email$($count+1)"; "emailAddress" = "$email"}
            $count++
        }

        $count = 0
        foreach ($webhook in $actions.webhookActions)
        {
            $webhookReceivers[$count] = @{"name" = "$($webhook.name)"; "serviceUri" = "$($webhook.serviceUri)"}
            $count++
        }

        $itsmAction = $actions.itsmAction
        if ($itsmAction.connectionId -ne $null)
        {
            $val = @{
            "name" = "ITSM"
            "workspaceId" = "$subscriptionId|$workspaceId"
            "connectionId" = "$($itsmAction.connectionId)"
            "ticketConfiguration" = $itsmAction.templateInfo
            "region" = "$resourceLocation"
            }
            $properties["itsmReceivers"] = @($val)  
        }

        $properties["emailReceivers"] = @($emailReceivers)
        $properties["webhookReceivers"] = @($webhookReceivers)
        $armPayload = @{"properties" = $properties; "location" = "Global"} | ConvertTo-Json -Compress -Depth 4

    
        # Azure Resource Manager call to create action group
        $response = $armPayload | armclient put /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroupName/?api-version=2017-04-01

        "Created Action Group with name $actionGroupName" 
        $actionGroupsMap[$actionGroupName] = $actionGroup.actionGroupResourceId.ToLower()
        $index++
    }

    "`nSuccessfully created all action groups!!"
}
catch
{
    $ErrorMessage = $_.Exception.Message

    #Delete all action groups in case of failure
    "`nDeleting newly created action groups if any as some error happened..."
    
    foreach ($actionGroup in $actionGroupsMap.Keys)
    {
        $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
    }

    "`nError: $ErrorMessage"
    "`nExiting..."
    exit
}

# Update all alerts configuration to the new version
"`nExtending OMS alerts to Azure...`n"

try
{
    $index = 1
    foreach ($alert in $result.migrationSummary.alerts)
    {
        $uri = $alert.alertId + "?api-version=2015-03-20"
        $config = armclient get $uri | ConvertFrom-Json
        $aznsNotification = @{
            "GroupIds" = @($actionGroupsMap[$alert.actionGroupName])
        }
        if ($alert.customWebhookPayload)
        {
            $aznsNotification.Add("CustomWebhookPayload", $alert.customWebhookPayload)
        }
        if ($alert.customEmailSubject)
        {
            $aznsNotification.Add("CustomEmailSubject", $alert.customEmailSubject)
        }      

        # Update alert version
        $config.properties.Version = 2

        $config.properties | Add-Member -MemberType NoteProperty -Name "AzNsNotification" -Value $aznsNotification
        $payload = $config | ConvertTo-Json -Depth 4
        $response = $payload | armclient put $uri
    
        "Extended alert with name $($alert.alertName)"
        $index++
    }
}
catch
{
    $ErrorMessage = $_.Exception.Message   
    if ($index -eq 1)
    {
        "`nDeleting all newly created action groups as no alerts got extended..."
        foreach ($actionGroup in $actionGroupsMap.Keys)
        {
            $response = armclient delete /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.insights/actionGroups/$actionGroup/?api-version=2017-04-01      
        }
        "`nDeleted all action groups."  
    }
    
    "`nError: $ErrorMessage"
    "`nPlease resolve the issue and try extending again!!"
    "`nExiting..."
    exit
}

"`nSuccessfully extended all OMS alerts to Azure!!" 

# Update version of workspace to indicate extension
"`nUpdating alert version information in OMS workspace..." 

$response = armclient post "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.OperationalInsights/workspaces/$workspaceName/alertsversion?api-version=2017-04-26-preview&iversion=2"

"`nExtension complete!!"
```


### <a name="about-the-custom-powershell-script"></a>Сведения о пользовательском сценарии PowerShell 
Информация, приведенная ниже, является очень важной для использования сценария.
- Предварительным требованием является установка [ARMClient](https://github.com/projectkudu/ARMClient) — инструмента командной строки с открытым исходным кодом, который упрощает вызов API Azure Resource Manager.
- Чтобы запустить сценарий, вы должны быть участником или обладать ролью владельца в подписке Azure.
- Необходимо предоставить следующие параметры:
    - $subscriptionId. Идентификатор подписки Azure, который связан с рабочей областью Log Analytics Operations Management Suite.
    - $resourceGroup. Группа ресурсов Azure для рабочей области Log Analytics Operations Management Suite.
    - $workspaceName. Имя рабочей области Log Analytics Operations Management Suite.

### <a name="output-of-the-custom-powershell-script"></a>Выходные данные пользовательского сценария PowerShell
Сценарий является подробным, что приводит к тому, что во время выполнения будут выполнены следующие действия: 
- Сценарий отобразит общие сведения, которые содержат информацию о существующих оповещениях Log Analytics Operations Management Suite в рабочей области. Также в общих сведениях содержится информация о группах действий Azure, которые будут созданы для действий, связанных с ними. 
- Пользователю будет предложено выйти после просмотра общих сведений или продолжить расширение.
- Если пользователь выберет продолжить расширение, будут созданы группы действий, и существующие оповещения будут с ними связаны. 
- Выполнение сценария завершается отображением сообщения "Расширение завершено". В случае каких-либо сбоев на среднем уровне сценарий отображает последующие ошибки.

## <a name="troubleshooting"></a>Устранение неполадок 
Во время процесса расширения оповещений могут возникнуть проблемы, из-за которых система не сможет создать необходимые [группы действий](monitoring-action-groups.md). В таких случаях на баннере раздела **Оповещение** портала Operations Management Suite либо в вызове GET, который выполняется для всех API, будет отображено сообщение об ошибке.

> [!IMPORTANT]
> Если до 5 июля 2018 г. не предпринять следующие действия по исправлению, оповещения продолжат выполняться в Azure, но любое их действие или уведомление работать не будет. Для получения уведомлений об оповещениях необходимо изменить и добавить [группы действий](monitoring-action-groups.md) вручную или использовать предыдущий [пользовательский сценарий PowerShell](#option-3---using-custom-powershell-script).

Здесь перечислены действия по исправлению для каждой возможной ошибки.
- **Ошибка. Блокировка области присутствует на уровне подписки или группы ресурсов для операций записи**. ![Снимок страницы параметров оповещения портала Operations Management Suite с выделенным сообщением о блокировке области](./media/monitor-alerts-extend/ErrorScopeLock.png)

    Когда функция "Блокировка области" включена, она ограничивает любое изменение в подписке или группе ресурсов, которое содержится в рабочей области Log Analytics (Operations Management Suite). Системе не удалось расширить оповещения в Azure или создать необходимые группы.
    
    Чтобы устранить эту проблему, из подписки или группы ресурсов, которые содержат рабочую область, необходимо удалить блокировку *ReadOnly*. Это можно сделать с помощью портала Azure, PowerShell, Azure CLI или API. Дополнительные сведения см. в статье [Блокировка ресурсов для предотвращения непредвиденных изменений](../azure-resource-manager/resource-group-lock-resources.md). 
    
    После устранения проблемы с помощью действий, приведенных в статье, Operations Management Suite расширяет оповещения в Azure на следующий день в рамках запланированного запуска. Выполнять дальнейшие действия или инициировать что-либо не требуется.

- **Ошибка. Политика присутствует на уровне подписки или группы ресурсов**. ![Снимок страницы параметров оповещения портала Operations Management Suite с выделенным сообщением об ошибке политики](./media/monitor-alerts-extend/ErrorPolicy.png)

    Когда применяется [политика Azure](../azure-policy/azure-policy-introduction.md), она ограничивает любые ресурсы, созданные в подписке или группе ресурсов, которые содержатся в рабочей области Log Analytics (Operations Management Suite). Системе не удалось расширить оповещения в Azure или создать необходимые группы.
    
    Для устранения отредактируйте политику, которая вызвала ошибку *[RequestDisallowedByPolicy](../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md)*, препятствующую созданию новых ресурсов в вашей подписке или группе ресурсов, в которых содержится рабочая область. Это можно сделать с помощью портала Azure, PowerShell, Azure CLI или API. Чтобы найти соответствующую политику, из-за которой происходит ошибка, можно использовать аудит действий. Дополнительные сведения см. в статье [Просмотр журналов действий для аудита действий с ресурсами](../azure-resource-manager/resource-group-audit.md). 
    
    После устранения проблемы с помощью действий, приведенных в статье, Operations Management Suite расширяет оповещения в Azure на следующий день в рамках запланированного запуска. Выполнять дальнейшие действия или инициировать что-либо не требуется.


## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения см. в статье [Функция "Унифицированные оповещения" в Azure Monitor](monitoring-overview-unified-alerts.md).
* Дополнительные сведения см. в статье [Оповещения журнала в Azure Monitor. Оповещения Azure (предварительная версия)](monitor-alerts-unified-log.md).
