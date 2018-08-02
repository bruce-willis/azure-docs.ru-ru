---
title: Управление доступом с помощью RBAC и шаблонов Azure Resource Manager | Документация Майкрософт
description: Узнайте, как управлять доступом пользователей, групп и приложений с помощью управления доступом на основе ролей (RBAC) и шаблонов Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5e080614d4f0001a0bf1b44dd402f37db2463e03
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39206695"
---
# <a name="manage-access-using-rbac-and-azure-resource-manager-templates"></a>Управление доступом с помощью RBAC и шаблонов Azure Resource Manager

[Управление доступом на основе ролей (RBAC)](overview.md) — это способ управления доступом к ресурсам в Azure. В дополнение к использованию Azure PowerShell или Azure CLI доступом к ресурсам Azure можно управлять с помощью RBAC и [шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Шаблоны могут быть полезны для согласованного и многократного развертывания ресурсов. В этой статье описывается, как управлять доступом с помощью RBAC и шаблонов.

## <a name="example-template-to-create-a-role-assignment"></a>Пример шаблона для создания и назначения роли

При использовании RBAC, чтобы предоставить доступ, нужно создать назначение ролей. Приведенный ниже шаблон демонстрирует:
- как назначить роли пользователю, группе или приложению в области действия группы ресурсов;
- как задать роли владельца, участника или читателя в качестве параметра.

Чтобы использовать шаблон, необходимо указать следующие входные данные:
- имя группы ресурсов;
- уникальный идентификатор пользователя, группы или приложения, которым назначается роль;
- роль для назначения;
- уникальный идентификатор, который будет использоваться для назначения роли.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

Ниже приведен пример назначения роли читателя пользователю после развертывания шаблона.

![Назначение роли с помощью шаблона](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Развертывание шаблона с помощью Azure PowerShell

Чтобы развернуть предыдущий шаблон с помощью Azure PowerShell, выполните такие действия.

1. Создайте файл с именем rbac rg.json и скопируйте предыдущий шаблон.

1. Войдите в [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Получите уникальный идентификатор пользователя, группы или приложения. Например, чтобы получить список пользователей Azure AD, можно использовать команду [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser).

    ```azurepowershell
    Get-AzureRmADUser
    ```

1. Используйте средство GUID для создания уникального идентификатора, который будет использоваться для назначения роли. Этот идентификатор имеет следующий формат: `11111111-1111-1111-1111-111111111111`

1. Создайте пример группы ресурсов.

    ```azurepowershell
    New-AzureRmResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Чтобы начать развертывание, используйте команду [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment).

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    Вам будет предложено указать необходимые параметры. Ниже приведен пример выходных данных.

    ```Output
    PS /home/user> New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="deploy-template-using-the-azure-cli"></a>Развертывание шаблона с помощью интерфейса командной строки Azure

Чтобы развернуть предыдущий шаблон с помощью Azure CLI, выполните такие действия.

1. Создайте файл с именем rbac rg.json и скопируйте предыдущий шаблон.

1. Войдите в [Azure CLI](/cli/azure/authenticate-azure-cli).

1. Получите уникальный идентификатор пользователя, группы или приложения. Например, чтобы перечислить пользователей Azure AD, можно использовать команду [az ad user list](/cli/azure/ad/user#az-ad-user-list).

    ```azurecli
    az ad user list
    ```

1. Используйте средство GUID для создания уникального идентификатора, который будет использоваться для назначения роли. Этот идентификатор имеет следующий формат: `11111111-1111-1111-1111-111111111111`

1. Создайте пример группы ресурсов.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Чтобы начать развертывание, используйте команду [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create).

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    Вам будет предложено указать необходимые параметры. Ниже приведен пример выходных данных.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```
    
## <a name="next-steps"></a>Дополнительная информация

- [Создание и развертывание первого шаблона Azure Resource Manager](../azure-resource-manager/resource-manager-create-first-template.md)
- [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [шаблонов быстрого запуска Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
