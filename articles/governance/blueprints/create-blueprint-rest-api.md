---
title: Создание схемы Azure с помощью REST API
description: Схемы Azure позволяют создавать, определять и развертывать артефакты.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: b873ee869b2044977ebefcfd65331567c24e7ec8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974210"
---
# <a name="define-and-assign-an-azure-blueprint-with-rest-api"></a>Определение и назначение схемы Azure с помощью REST API

Понимание того, как создавать и назначать схемы в Azure, позволяет организации определять стандартные согласованные модели и разрабатывать быстро развертываемые конфигурации для повторного использования на основе шаблонов Resource Manager, политик, правил безопасности и других элементов. В этом руководстве вы научитесь использовать службу Azure Blueprint для выполнения некоторых общих задач, связанных с созданием, публикацией и назначением схемы в вашей организации, таких как:

> [!div class="checklist"]
> - создание схемы и добавление различных поддерживаемых артефактов;
> - внесение изменений в существующую схему на стадии **черновика**;
> - обозначение схемы как готовой к назначению с помощью **публикации**;
> - назначение схемы существующей подписке;
> - проверка состояния и хода выполнения назначенной схемы;
> - удаление схемы, назначенной подписке.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

## <a name="getting-started-with-rest-api"></a>Начало работы с REST API

Если у вас нет опыта работы с интерфейсом REST API, сначала ознакомьтесь со [справочником по REST API Azure](/rest/api/azure/), чтобы получить общее представление о нем, в частности об URI запроса и тексте запроса. В данной статье эти понятия используются в указаниях по работе со схемами Azure, поэтому предполагается, что у вас есть соответствующие практические навыки. Начинающим рекомендуется применять такие средства, как [ARMClient](https://github.com/projectkudu/ARMClient), обеспечивающие автоматическую авторизацию.

### <a name="rest-api-and-powershell"></a>REST API и PowerShell

Если у вас еще нет средства для выполнения вызовов REST API, рекомендуем использовать среду PowerShell. Ниже приведен пример заголовка для проверки подлинности в Azure. Создайте заголовок проверки подлинности, иногда называемый **токеном носителя**, и укажите универсальный код ресурса (URI) REST API, к которому нужно подключиться, а также параметры или **текст запроса**.

```powershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2016-06-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

Замените `{subscriptionId}` в значении приведенной выше переменной **$restUri**, чтобы получить сведения о своей подписке. Переменная $response содержит результат выполнения командлета `Invoke-RestMethod`, который может быть проанализирован с помощью таких командлетов, как [ConvertFrom-Json](/powershell/module/microsoft.powershell.utility/convertfrom-json). Если конечная точка службы REST API требует **текста запроса**, укажите переменную в формате JSON в качестве значения параметра `-Body` командлета `Invoke-RestMethod`.

## <a name="create-a-blueprint"></a>Создание схемы

Первым этапом при определении стандартной модели соответствия требованиям является формирование схемы на основе доступных ресурсов. В этом примере вы создадите схему с именем MyBlueprint с целью настройки назначений ролей и политик для подписки, добавите группу ресурсов, а затем создадите для нее шаблон Resource Manager и назначение ролей.

> [!NOTE]
> При использовании REST API сначала создается объект _схемы_. Для каждого _артефакта_ с параметрами, который необходимо добавить, необходимо заранее определить параметры в исходной _схеме_.

В каждом универсальном коде ресурса (URI) REST API есть переменные, которые необходимо заменить собственными значениями:

- `{YourMG}` — замените это значение именем своей группы управления
- `{subscriptionId}` — замените это значение идентификатором своей подписки

1. Создайте исходный объект _схемы_. **Текст запроса** включает в себя свойства схемы, группы ресурсов, которые необходимо создать, и все параметры уровня схемы, задаваемые при назначении и используемые артефактами, которые будут добавлены в дальнейшем.

   - Универсальный код ресурса (URI) REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
     ```

   - Текст запроса

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

1. Добавьте назначение ролей в подписку. В **тексте запроса** определяется _тип_ артефакта, свойства соответствуют идентификатору определения роли, а удостоверения субъектов передаются в виде массива значений. В приведенном ниже примере удостоверения субъектов, которым предоставляется указанная роль, передаются в параметре, задаваемом во время назначения схемы.

   - Универсальный код ресурса (URI) REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2017-11-11-preview
     ```

   - Текст запроса

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. Добавьте назначение политики в подписку. В **тексте запроса** определяется _тип_ артефакта и свойства, которые соответствуют определению политики или инициативы, а для назначения политики настраивается использование определенных параметров схемы, настраиваемых во время назначения схемы.

   - Универсальный код ресурса (URI) REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2017-11-11-preview
     ```

   - Текст запроса

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Добавьте в подписку еще одно назначение политики для тега хранилища (повторно используя параметр _storageAccountType_). Этот дополнительный артефакт назначения политики демонстрирует, что определенный в схеме параметр может использоваться несколькими артефактами. В примере параметр **storageAccountType** используется для применения к группе ресурсов тега, предоставляющего сведения об учетной записи хранения, которая будет создана в следующем шаге.

   - Универсальный код ресурса (URI) REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
     ```

   - Текст запроса

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

1. Добавьте шаблон в группу ресурсов. В **тексте запроса** для шаблона Resource Manager содержится обычный компонент JSON шаблона, определяется конечная группа ресурсов с помощью **properties.resourceGroup** и повторно используются параметры схемы **storageAccountType**, **tagName** и **tagValue**, каждый из которых передается в шаблон. Для того чтобы параметры схемы стали доступны шаблону, определяется структура **properties.parameters**. В коде JSON шаблона эта пара "ключ-значение" используется для внедрения значения. Имена параметров схемы и шаблона могут совпадать, но здесь они различаются, чтобы можно было наглядно увидеть, как каждый из них передается из схемы в артефакт шаблона.

   - Универсальный код ресурса (URI) REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2017-11-11-preview
     ```

   - Текст запроса

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
                         "metadata": {
                             "description": "Storage Account type"
                         }
                     },
                     "tagNameFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag name from blueprint"
                         }
                     },
                     "tagValueFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag value from blueprint"
                         }
                     }
                 },
                 "variables": {
                     "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
                 },
                 "resources": [{
                     "type": "Microsoft.Storage/storageAccounts",
                     "name": "[variables('storageAccountName')]",
                     "apiVersion": "2016-01-01",
                     "tags": {
                        "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
                     },
                     "location": "[resourceGroup().location]",
                     "sku": {
                         "name": "[parameters('storageAccountTypeFromBP')]"
                     },
                     "kind": "Storage",
                     "properties": {}
                 }],
                 "outputs": {
                     "storageAccountSku": {
                         "type": "string",
                         "value": "[variables('storageAccountName')]"
                     }
                 }
             },
             "resourceGroup": "storageRG",
             "parameters": {
                 "storageAccountTypeFromBP": {
                     "value": "[parameters('storageAccountType')]"
                 },
                 "tagNameFromBP": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValueFromBP": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. Добавьте назначение ролей в группу ресурсов. Так же как и в предыдущем фрагменте назначения ролей, в приведенном ниже примере используется идентификатор для роли **Владелец**, но из схемы для него передается другой параметр.

   - Универсальный код ресурса (URI) REST API

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
     ```

   - Текст запроса

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

## <a name="publish-a-blueprint"></a>Публикация схемы

Теперь, когда артефакты добавлены в схему, пора ее опубликовать. Публикация схемы позволяет назначать ее подписке.

- Универсальный код ресурса (URI) REST API

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2017-11-11-preview
  ```

Значение `{BlueprintVersion}` представляет собой строку, состоящую из букв, цифр и дефисов (без пробелов или других специальных символов) и содержащую не более 20 символов. Оно должно быть уникальным и информативным, например **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Назначение схемы

После публикации схемы с помощью REST API ее можно назначить подписке. Назначьте созданную схему одной из подписок в иерархии группы управления. В **тексте запроса** указываются назначаемая схема, имена и расположения всех групп ресурсов в определении схемы, а также все параметры, определенные в схеме и используемые одним или несколькими связанными артефактами.

1. Предоставьте субъекту-службе Azure Blueprint роль **владельца** в целевой подписке. Идентификатор AppId является статическим (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`), однако идентификатор субъекта-службы зависит от клиента. Подробные сведения для своего клиента можно запросить с помощью приведенного ниже REST API. Он использует [API Graph Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md), схема авторизации которого отличается.

   - Универсальный код ресурса (URI) REST API

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. Запустите развертывание схемы, назначив ее подписке. Так как в параметрах **contributors** и **owners** должны быть переданы массивы идентификаторов objectId субъектов, которым должно быть предоставлено назначение ролей, используйте [API Graph Azure Active Directory](../../active-directory/develop/active-directory-graph-api.md) для сбора идентификаторов objectId, которые вы затем будете использовать в **тексте запроса** для собственных пользователей, групп или субъектов служб.

   - Универсальный код ресурса (URI) REST API

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
     ```

   - Текст запроса

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

## <a name="unassign-a-blueprint"></a>Отмена назначения схемы

Если схемы больше не нужны или были заменены схемами с обновленными шаблонами, политиками и структурами, их можно удалить из подписки. При удалении схемы назначенные артефакты оставляются. Чтобы отменить назначение схемы, используйте следующую операцию REST API:

- Универсальный код ресурса (URI) REST API

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
  ```

## <a name="delete-a-blueprint"></a>Удаление схемы

Чтобы удалить саму схему, используйте следующую операцию REST API:

- Универсальный код ресурса (URI) REST API

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
  ```

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с [жизненным циклом схемы](./concepts/lifecycle.md)
- Узнайте, как использовать [статические и динамические параметры](./concepts/parameters.md)
- Научитесь настраивать [последовательность схемы](./concepts/sequencing-order.md)
- Узнайте, как применять [блокировку ресурсов схемы](./concepts/resource-locking.md)
- Узнайте, как [обновлять существующие назначения](./how-to/update-existing-assignments.md)
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](./troubleshoot/general.md)