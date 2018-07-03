---
title: Пример политики Azure — утвержденные образы виртуальных машин
description: Эта политика требует, чтобы в среде развертывались только утвержденные пользовательские образы.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 06/03/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 408968723ab9dc14a1db1321c4db3b3d8518d511
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36319305"
---
# <a name="approved-vm-images"></a>Утвержденные образы виртуальных машин

Эта политика требует, чтобы в среде развертывались только утвержденные пользовательские образы. Вы можете указать массив идентификаторов утвержденных образов.

Развернуть этот образец политики можно с помощью следующих средств.

- [портал Azure](#azure-portal).
- [Azure PowerShell](#azure-powershell)
- [интерфейс командной строки Azure](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>Пример политики

### <a name="policy-definition"></a>Определение политики

Полное описание политики JSON, использующееся API REST, кнопками «Развернуть в Azure» и вручную на портале.

[!code-json[full](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.json "Complete policy definition (JSON)")]

> [!NOTE]
> Если создавать политику на портале вручную, используйте вышеизложенные части **properties.parameters** и **properties.policyRule**. Создайте программу-оболочку двух разделов вместе с фигурными скобками `{}` для внесения допустимых данных JSON.

### <a name="policy-rules"></a>Правила политики

JSON определяет правила политики, используемые Azure CLI и Azure PowerShell.

[!code-json[rule](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>Параметры политики

JSON определяет параметры политики, используемые Azure CLI и Azure PowerShell.

[!code-json[parameters](../../../policy-templates/samples/compute/allowed-custom-images/azurepolicy.parameters.json "Policy parameters (JSON)")]

## <a name="parameters"></a>Параметры

|ИМЯ |type |Поле |ОПИСАНИЕ |
|---|---|---|---|
|imageIds |Массив, |Microsoft.Compute/imageIds |Список утвержденных образов виртуальных машин|

При создании задания через PowerShell или Azure CLI значения параметров могут передаваться как JSON или в строке, или через файл с использованием `-PolicyParameter` (PowerShell) или `--params` (Azure CLI).
PowerShell также поддерживает `-PolicyParameterObject`, требующий передать командлету хэш-таблицу имен и значений, где **Имя** является именем параметра, а **значение** — отдельным значением или массивом значений, передаваемых во время назначения.

В этом примере параметра допускается только _ContosoStdImage_ в группе ресурсов _YourResourceGroup_ или образ Windows Server 2016 Datacenter версии за май 2018 года, с расположением в "центральной части США".

```json
{
    "imageIds": {
        "value": [
            "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
            "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
        ]
    }
}
```

## <a name="azure-portal"></a>Портал Azure

[![Развертывание в Azure](../media/deploy/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)
[![Развертывание в Azure для государственных организаций](../media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FCompute%2Fallowed-custom-images%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

### <a name="deploy-with-azure-powershell"></a>Развертывание с помощью Azure PowerShell

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzureRmPolicyDefinition -Name 'allowed-custom-images' -DisplayName 'Approved VM images' -description 'This policy governs the approved VM images' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzureRmResourceGroup -Name 'YourResourceGroup'

# Set the Policy Parameter (JSON format)
$policyparam = '{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
$assignment = New-AzureRmPolicyAssignment -Name 'allowed-custom-images-assignment' -DisplayName 'Approved VM images Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition -PolicyParameter $policyparam
```

### <a name="remove-with-azure-powershell"></a>Удаление с помощью Azure PowerShell

Чтобы удалить предыдущее назначение и определение, выполните следующую команду.

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzureRmPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzureRmPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Пояснение Azure PowerShell

Чтобы развернуть и удалить скрипты используйте следующие команды. Для каждой команды в следующей таблице приведены ссылки на соответствующую документацию:

| Get-Help | Заметки |
|---|---|
| [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) | Создается новое определение политики Azure. |
| [Get-AzureRmResourceGroup](/powershell/module/azurerm.resources/get-azurermresourcegroup) | Получение одной группы ресурсов. |
| [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) | Создается новое назначение политики Azure. В этом примере политики предоставляется определение, но она также может взять на себя инициативу. |
| [Remove-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/remove-azurermpolicyassignment) | Удаление существующего назначения политики Azure. |
| [Remove-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/remove-azurermpolicydefinition) | Удаление существующего определения политики Azure. |

## <a name="azure-cli"></a>Инфраструктура CLI Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Развертывание с помощью интерфейса командной строки Azure

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'allowed-custom-images' --display-name 'Approved VM images' --description 'This policy governs the approved VM images' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/Compute/allowed-custom-images/azurepolicy.parameters.json' --mode All)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Set the Policy Parameter (JSON format)
policyparam='{ "imageIds": { "value": [ "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage", "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510" ] } }'

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'allowed-custom-images-assignment' --display-name 'Approved VM images Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r` --params "$policyparam")
```

### <a name="remove-with-azure-cli"></a>Удаление с помощью интерфейса командной строки Azure

Чтобы удалить предыдущее назначение и определение, выполните следующую команду.

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Пояснение Azure CLI

| Get-Help | Заметки |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | Создается новое определение политики Azure. |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | Получение одной группы ресурсов. |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | Создается новое назначение политики Azure. В этом примере политики предоставляется определение, но она также может взять на себя инициативу. |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | Удаление существующего назначения политики Azure. |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | Удаление существующего определения политики Azure. |

## <a name="rest-api"></a>ИНТЕРФЕЙС REST API

Существует несколько средств, которые могут использоваться для взаимодействия с диспетчером ресурсов REST API, такие как [ARMClient](https://github.com/projectkudu/ARMClient) или PowerShell. Пример вызова интерфейса API REST PowerShell можно найти в разделе **Псевдонимы** статьи [Структура определения службы "Политика Azure"](../policy-definition.md#aliases).

### <a name="deploy-with-rest-api"></a>Развертывание с помощью REST API

- Создайте определения политики (область подписки). Используйте [определения политики](#policy-definition) JSON для текста запроса.

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

- Создайте назначения политики (область группы ресурсов).

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

  Используйте следующий пример JSON для текста запроса.

  ```json
  {
      "properties": {
          "displayName": "Approved VM images Assignment",
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images",
          "parameters": {
              "imageIds": {
                  "value": [
                      "/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage",
                      "/Subscriptions/<subscriptionId>/Providers/Microsoft.Compute/Locations/centralus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2016.127.20180510"
                  ]
              }
          }
      }
  }
  ```

### <a name="remove-with-rest-api"></a>Удаление с помощью REST API

- Удаление назначения политики

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/allowed-custom-images-assignment?api-version=2017-06-01-preview
  ```

- Удаление определения политики

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/allowed-custom-images?api-version=2016-12-01
  ```

### <a name="rest-api-explanation"></a>Описание REST API

| Service | Группа | Операция | Заметки |
|---|---|---|---|
| Управление ресурсами | Определения политик | [Создание](/rest/api/resources/policydefinitions/createorupdate) | Создается новое определение политики Azure в подписке. Альтернативный вариант: [Создание в группе управления](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| Управление ресурсами | Назначения политик | [Создание](/rest/api/resources/policyassignments/create) | Создается новое назначение политики Azure. В этом примере политики предоставляется определение, но она также может взять на себя инициативу. |
| Управление ресурсами | Назначения политик | [Удалить](/rest/api/resources/policyassignments/delete) | Удаление существующего назначения политики Azure. |
| Управление ресурсами | Определения политик | [Удалить](/rest/api/resources/policydefinitions/delete) | Удаление существующего определения политики Azure. Альтернатива: [Удаление в группе управления](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>Дополнительная информация

- Просмотр дополнительных [примеров для Политики Azure](../json-samples.md)
- Просмотр [Структура определения службы "Политика Azure"](../policy-definition.md)
- Примеры политики Azure для виртуальных машин см. в статье [Применение политик к виртуальным машинам Windows с помощью Azure Resource Manager](../../virtual-machines/windows/policy.md).