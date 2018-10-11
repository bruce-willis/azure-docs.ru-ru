---
title: Включение поддержки нескольких источников для учетных записей Azure Cosmos DB
description: В этой статье описывается включение поддержки нескольких источников при создании учетной записи Azure Cosmos DB с помощью портала Azure, PowerShell, CLI или шаблона Azure Resource Manager.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b6fc04fc728f753dc8a3900b26c6c01f03cc7710
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077244"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Включение поддержки нескольких источников для учетных записей Azure Cosmos DB

Поддержка нескольких источников включается при создании учетной записи Azure Cosmos DB. Учетную запись Azure Cosmos DB можно создать с помощью портала Azure, PowerShell, CLI или шаблона Azure Resource Manager.

> [!IMPORTANT]
> Сейчас поддержку нескольких источников можно включить только для новых учетных записей Azure Cosmos DB. Имеющиеся учетные записи Azure Cosmos DB не могут использовать эту функцию. Мы работаем над обеспечением поддержки для имеющихся учетных записей и объявим об этой поддержке, когда она станет доступна.

После создания учетной записи Azure Cosmos DB с поддержкой нескольких источников можно создавать базы данных, контейнеры, отправлять документы и назначать политики разрешения конфликтов. Сведения о разрешениях конфликтов в нескольких источниках и примеры кода см. в [этой статье](multi-master-conflict-resolution.md#code-samples).

## <a name="enable-multi-master-using-azure-portal"></a>Включение поддержки нескольких источников с помощью портала Azure

1. Войдите на [портал Azure](https://portal.azure.com/)

2. Щелкните **Создать ресурс > Базы данных > Azure Cosmos DB**.

3. На странице **Новая учетная запись** введите следующие параметры для новой учетной записи Azure Cosmos DB.

   |**Параметр**  |**Рекомендуемое значение** |**Описание**|
   |---------|---------|---------|
   |Подписка   | {Ваша подписка}  |Выберите подписку Azure, которую необходимо использовать для этой учетной записи Azure Cosmos DB.  |
   |Группа ресурсов  |   {Имя вашей группы ресурсов}    |  Выберите имеющуюся группу ресурсов или щелкните  **Создать** и введите новое имя группы ресурсов для учетной записи. |
   |Имя учетной записи | {имя вашей учетной записи}   |  Введите уникальное имя для идентификации вашей учетной записи Azure Cosmos DB.        |
   |API  |   Любой   |  Выберите тип API.   |
   |Расположение  | Выберите любой регион   | Выберите географическое расположение, в котором будет размещена учетная запись Azure Cosmos DB. Вы можете выбрать любой регион, так как эта учетная запись будет доступна в нескольких регионах.  |
   |Включение геоизбыточности   |  Включение  |  Выберите, чтобы активировать параметр использования нескольких источников для выбора ниже.   |
   |Включить поддержку нескольких источников | Включение  | Выберите, чтобы включить поддержку нескольких источников для этой учетной записи. |


## <a name="using-multi-master-in-sdks"></a>Использование нескольких источников в пакетах SDK

Используя учетную запись с поддержкой нескольких источников в приложениях, вы можете воспользоваться преимуществами нескольких источников за счет применения ConnectionPolicy, как показано ниже.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add(LocationNames.WestUS);
policy.PreferredLocations.Add(LocationNames.NorthEurope);
policy.PreferredLocations.Add(LocationNames.SoutheastAsia);
```

## <a name="enable-multi-master-using-powershell"></a>Включение поддержки нескольких источников с помощью PowerShell

Создать учетную запись Cosmos DB с поддержкой нескольких источников также можно, задав для параметра `enableMultipleWriteLocations` значение true. Чтобы создать учетную запись Azure Cosmos DB с поддержкой нескольких источников, откройте окно PowerShell и выполните следующий сценарий:

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>Включение поддержки нескольких источников с помощью CLI

Поддержку нескольких источников можно включить, задав для параметра enable-multiple-write-locations значение true. Чтобы создать учетную запись Azure Cosmos DB с поддержкой нескольких источников, откройте Azure CLI или Cloud Shell и выполните следующую команду:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Включение поддержки нескольких источников с помощью шаблона Resource Manager

Следующий код JSON — это пример шаблона Resource Manager, который можно использовать для развертывания учетной записи Azure Cosmos DB. Дополнительные сведения о формате шаблона Resource Manager и синтаксисе см. в документации по [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). В этом шаблоне следует обратить внимание на параметр "enableMultipleWriteLocations": true.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```

## <a name="next-steps"></a>Дополнительная информация

В этой статье вы узнали о включении поддержки нескольких источников для учетных записей Azure Cosmos DB. Далее рекомендуем ознакомиться со следующими ресурсами:

* [Использование нескольких источников Azure Cosmos DB в работе с базами данных NoSQL с открытым кодом](multi-master-oss-nosql.md)

* [Разрешение конфликтов с несколькими источниками в Azure Cosmos DB](multi-master-conflict-resolution.md)
