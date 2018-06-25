---
title: Запуск Runbook службы автоматизации Azure с помощью объекта webhook
description: Объект Webhook, который позволяет клиенту запустить модуль Runbook в службе автоматизации Azure с помощью HTTP-запроса.  В статье рассматривается создание объекта Webhook и вызов такого объекта для запуска модуля Runbook.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 06/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e047dffa86915b0cd6e8829ea27e0335e7f88cb2
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757162"
---
# <a name="starting-an-azure-automation-runbook-with-a-webhook"></a>Запуск Runbook службы автоматизации Azure с помощью объекта webhook

*Webhook* позволяет запустить модуль Runbook в службе автоматизации Azure с помощью одного HTTP-запроса. Это позволяет внешним службам, таким как Visual Studio Team Services, GitHub, Azure Log Analytics или пользовательским приложениям, запускать модули runbook без реализации полного решения с помощью API службы автоматизации Azure.  
![Обзор веб-перехватчиков](media/automation-webhooks/webhook-overview-image.png)

Сравнение объектов webhook с другими методами запуска модуля Runbook см. в разделе [Запуск модуля Runbook в службе автоматизации Azure](automation-starting-a-runbook.md).

## <a name="details-of-a-webhook"></a>Подробные сведения о Webhook

В следующей таблице описаны свойства, которые необходимо настроить для объекта Webhook.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| ИМЯ |Для объекта Webhook можно указать любое имя, так как оно не раскрывается клиенту. Имя используется для идентификации модуля Runbook в службе автоматизации Azure. <br> Рекомендуется задать веб-перехватчику имя, связанное с клиентом, который будет его использовать. |
| URL-адрес |URL-адрес Webhook — это уникальный адрес, который использует клиент для метода HTTP POST для запуска модуля Runbook, связанного с объектом Webhook. URL-адрес создается автоматически при создании объекта Webhook. Нельзя указать другой URL-адрес. <br> <br> URL-адрес содержит токен безопасности, который позволяет сторонней системе вызвать модуль Runbook без дополнительной проверки подлинности. По этой причине он должен рассматриваться как пароль. По соображениям безопасности просмотреть URL-адрес на портале Azure можно только в момент создания веб-перехватчика. Запишите URL-адрес в надежном месте, чтобы использовать его в дальнейшем. |
| Срок действия |Как и сертификат, каждый объект Webhook имеет срок действия, после которого он больше не используется. Этот срок можно изменить после создания объекта webhook. |
| Включено |При создании объекта Webhook он по умолчанию включается. Если его отключить, клиенты не смогут его использовать. Свойство **Включен** можно задать при создании объекта Webhook или в любое время после его создания. |

### <a name="parameters"></a>Параметры

Объект Webhook может определять значения для параметров модуля Runbook, которые используются при запуске модуля Runbook этим объектом Webhook. Объект Webhook должен содержать значения для всех обязательных параметров модуля Runbook и может также включать необязательные параметры. Значение параметра, который настроен для веб-перехватчика, можно изменить даже после создания веб-перехватчика. Несколько объектов Webhook, привязанных к одному модулю Runbook, могут использовать разные значения параметров.

Когда клиент запускает модуль Runbook с помощью объекта Webhook, клиент не может переопределить значения параметров, определяемые Webhook. Чтобы получать данные от клиента, модуль Runbook может принимать один параметр **$WebhookData** типа [object], который содержит данные, включаемые клиентом в POST-запрос.

![Свойства Webhookdata](media/automation-webhooks/webhook-data-properties.png)

Объект **$WebhookData** имеет следующие свойства:

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| WebhookName |Имя объекта Webhook. |
| RequestHeader |Хэш-таблица, содержащая заголовки входящего запроса POST. |
| RequestBody |Текст входящего запроса POST. Это сохранит все форматирование, такое как строка, JSON, XML или данные, закодированные в форме. Модуль Runbook должен быть написан для работы с форматом данных, который ожидается. |

Нет настройки объекта Webhook, которая была бы обязательной для поддержки параметра **$WebhookData** , однако модуль Runbook не обязан принимать его. Если модуль Runbook не определяет параметр, любые сведения в запросе клиента пропускаются.

Если указать значение для параметра $WebhookData в момент создания веб-перехватчика, это значение будет переопределено, когда веб-перехватчик запускает модуль Runbook с данными из клиентского POST-запроса, даже если клиент не включит данные в текст запроса. При запуске модуля Runbook, у которого параметр $WebhookData использует метод, отличный от Webhook, можно указать значение для $Webhookdata, которое распознается модулем Runbook. Это значение должно быть объектом с теми же [свойствами](#details-of-a-webhook) , что и $Webhookdata, чтобы модуль Runbook смог с ним правильно работать как с фактическим WebhookData, переданным веб-перехватчиком.

Например, если на портале Azure запускается следующий модуль Runbook и требуется передать несколько примеров WebhookData для тестирования, то, так как WebhookData является объектом, он должен быть передан в пользовательском интерфейсе как JSON.

![Параметр WebhookData из пользовательского интерфейса](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Для следующего модуля Runbook, если у вас есть приведенные ниже свойства для параметра WebhookData:

* WebhookName: *MyWebhook*
* RequestBody: *[{"ResourceGroup": "myResourceGroup","Name": "vm01"},{"ResourceGroup": "myResourceGroup","Name": "vm02"}]*

В пользовательском интерфейсе для параметра WebhookData следует передать следующее значение JSON. Следующий пример со знаками возврата каретки и знаками новой строки соответствует формату, который передается из веб-перехватчика.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Запуск параметра WebhookData из пользовательского интерфейса](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Значения входных параметров вводятся вместе с заданием Runbook. Это означает, что любые входные данные, предоставленные клиентом в запросе Webhook, будут записаны в журнал и станут доступны любому пользователю с доступом к заданию автоматизации.  По этой причине необходимо соблюдать осторожность при указании критических данных в вызовах Webhook.

## <a name="security"></a>Безопасность

Безопасность веб-перехватчика зависит от безопасности URL-адреса, который содержит токен безопасности, позволяющий вызывать объект веб-перехватчика. Служба автоматизации Azure не выполняет проверку подлинности для запроса, если он выполняется с правильным URL-адресом. По этой причине объекты Webhook не следует применять для модулей Runbook, которые выполняют важные функции, без использования дополнительной проверки запроса.

Можно включить логику в модуль Runbook, которая будет определять вызов объектом Webhook путем проверки свойства **WebhookName** для параметра $WebhookData. Модуль Runbook может выполнять последующие проверки путем поиска определенной информации в свойствах **RequestHeader** или **RequestBody**.

Еще одна стратегия заключается в том, чтобы модуль Runbook выполнял проверку внешнего условия после получения запроса Webhook. Например, рассмотрим модуль Runbook, вызываемый из GitHub каждый раз, когда на GitHub появляется обновление репозитория. Модуль Runbook может подключаться к GitHub, чтобы проверить доступность обновления.

## <a name="creating-a-webhook"></a>Создание объекта Webhook

Чтобы создать новый веб-перехватчик, связанный с модулем Runbook, на портале Azure, воспользуйтесь следующей процедурой.

1. На странице **Модули Runbook** на портале Azure щелкните модуль Runbook, запускаемый веб-перехватчиком, чтобы открыть страницу с подробной информацией о нем.
1. Щелкните **Веб-перехватчик** в верхней части страницы, чтобы открыть страницу **Добавить веб-перехватчик**.
1. Щелкните **Создать новый веб-перехватчик**, чтобы открыть страницу **Создать веб-перехватчик**.
1. Укажите для объекта webhook **имя**, **Срок действия** и необходимость его включения. Подробные сведения об этих свойствах см. в разделе [Details of a webhook](#details-of-a-webhook) (Сведения об объекте webhook).
1. Щелкните значок копирования и нажмите Ctrl+C, чтобы скопировать URL-адрес объекта Webhook. Сохраните URL-адрес в безопасном месте. **После создания объекта webhook URL-адрес нельзя получить повторно.**

   ![URL-адрес Webhook](media/automation-webhooks/copy-webhook-url.png)

1. Щелкните **Параметры** , чтобы указать значения для параметров модуля Runbook. Если модуль Runbook содержит обязательные параметры, то его нельзя будет создать, не указав значения.
1. Щелкните **Создать** , чтобы создать объект Webhook.

## <a name="using-a-webhook"></a>Использование объекта Webhook

Чтобы использовать объект Webhook после его создания, клиентское приложение должно создать запрос HTTP POST с URL-адресом объекта Webhook. Синтаксис веб-перехватчика будет иметь следующий формат:

```http
http://<Webhook Server>/token?=<Token Value>
```

Клиент получит один из следующих кодов возврата в ответ на запрос POST.

| Код | текст | ОПИСАНИЕ |
|:--- |:--- |:--- |
| 202 |Принято |Запрос был принят, и модуль Runbook успешно поставлен в очередь. |
| 400 |Ошибка запроса |Запрос не был принят по одной из следующих причин: <ul> <li>Срок действия объекта webhook истек.</li> <li>Объект webhook отключен.</li> <li>Недопустимый токен в URL-адресе.</li>  </ul> |
| 404 |Не найдено |Запрос не был принят по одной из следующих причин: <ul> <li>Объект webhook не найден.</li> <li>Модуль Runbook не найден.</li> <li>Учетная запись не найдена.</li>  </ul> |
| 500 |Внутренняя ошибка сервера |URL-адрес допустимый, но произошла ошибка. Отправьте запрос повторно. |

Если предположить, что запрос выполнен успешно, ответ Webhook будет содержать идентификатор задания в формате JSON, как показано далее. Он будет содержать идентификатор одного задания, но формат JSON предоставляет возможность потенциальных будущих улучшений.

```json
{"JobIds":["<JobId>"]}
```

Клиент не может определить момент завершения задания Runbook и состояние его завершения из объекта Webhook. Он может определить эти сведения с помощью идентификатора задания, используя другой метод, например [Windows PowerShell](http://msdn.microsoft.com/library/azure/dn690263.aspx) или [API службы автоматизации Azure](/rest/api/automation/job).

## <a name="sample-runbook"></a>Пример Runbook

Следующий пример модуля Runbook принимает данные веб-перехватчика и запускает виртуальные машины, указанные в тексте запроса. Для тестирования этого модуля Runbook в учетной записи автоматизации в разделе **Модули Runbook** нажмите кнопку **+ Добавить Runbook**. Если вы не знаете, как создать модуль Runbook, ознакомьтесь с [этой статьей](automation-quickstart-create-runbook.md).

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Retrieve VM's from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName" 

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal." 
            Add-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzureRMVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-example"></a>Тестирование примера

В следующем примере модуль Runbook запускается с помощью Webhook из Windows PowerShell. В любом языке, способном выполнить HTTP-запрос, можно использовать веб-перехватчик. Windows PowerShell используется здесь для примера.

Модуль Runbook ожидает список виртуальных машин, отформатированный в JSON, в теле запроса.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms

$response = Invoke-RestMethod -Method Post -Uri $uri -Body $body
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

В следующем примере показан текст запроса, доступный модулю Runbook в свойстве **RequestBody** объекта **WebhookData**. Оно отформатировано как JSON, так как это формат, который был включен в тело запроса.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

На следующем рисунке показан запрос, отправляемый из Windows PowerShell, а также получаемый в результате ответ. Идентификатор задания извлекается из ответа и преобразуется в строку.

![Кнопка Webhook](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Дополнительная информация

* Узнать, как использовать службу автоматизации Azure для выполнения действий на основе оповещений Azure, можно в статье [Использование оповещения для активации runbook службы автоматизации Azure](automation-create-alert-triggered-runbook.md).
