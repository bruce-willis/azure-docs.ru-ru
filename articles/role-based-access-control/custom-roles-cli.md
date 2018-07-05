---
title: Создание пользовательских ролей с помощью Azure CLI | Документы Майкрософт
description: Узнайте, как создавать пользовательские роли для управления доступом на основе ролей (RBAC) с помощью Azure CLI. Здесь приводится описание процедур получения списка, создания, обновления и удаления пользовательских ролей.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: b1df50c73497e3f5ad64a7ba7210079871b155e0
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36324179"
---
# <a name="create-custom-roles-using-azure-cli"></a>Создание пользовательских ролей с помощью Azure CLI

Если [встроенные роли](built-in-roles.md) не соответствуют потребностям вашей организации, вы можете создать собственные пользовательские роли. В этой статье описывается создание пользовательских ролей и управление ими с помощью Azure CLI.

## <a name="prerequisites"></a>предварительным требованиям

Для создания пользовательских ролей в Azure требуются:

- разрешения на создание пользовательских ролей, такие как [Владелец](built-in-roles.md#owner) или [Администратор доступа пользователя](built-in-roles.md#user-access-administrator);
- локально установленный [Azure CLI](/cli/azure/install-azure-cli).

## <a name="list-custom-roles"></a>Вывод списка настраиваемых ролей

Чтобы получить список пользовательских ролей, доступных для назначения, используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list). В следующих примерах перечислены все пользовательские роли в текущей подписке.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Чтобы создать пользовательскую роль, используйте команду [az role definition create](/cli/azure/role/definition#az-role-definition-create). Определением роли может быть описание JSON или путь к файлу, содержащему описание JSON.

```azurecli
az role definition create --role-definition <role_definition>
```

В следующем примере показано создание пользовательской роли *Оператор виртуальной машины*. Пользовательская роль предоставляет доступ ко всем операциям чтения поставщиков ресурсов *Microsoft.Compute*, *Microsoft.Storage* и *Microsoft.Network*, а также доступ для запуска, перезапуска и мониторинга виртуальных машин. Эту настраиваемую роль можно использовать в двух подписках. В этом примере в качестве входных данных используется JSON-файл.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Обновление пользовательской роли

Чтобы обновить пользовательскую роль, сначала используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list) для получения определения роли. Затем внесите необходимые изменения в определение роли. И наконец, сохраните обновленное определение роли с помощью команды [az role definition update](/cli/azure/role/definition#az-role-definition-update).

```azurecli
az role definition update --role-definition <role_definition>
```

В следующем примере операция *Microsoft.Insights/diagnosticSettings/* добавляется в раздел *Actions* пользовательской роли *Оператор виртуальной машины*.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли

Чтобы удалить пользовательскую роль, используйте команду [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Чтобы указать роль, которую необходимо удалить, используйте имя или идентификатор роли. Чтобы определить идентификатор роли, используйте команду [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

В следующем примере показано удаление пользовательской роли *Оператор виртуальной машины*.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Дополнительная информация

- [Учебник. Создание пользовательской роли с помощью Azure CLI](tutorial-custom-role-cli.md)
- [Пользовательские роли в Azure](custom-roles.md)
- [Операции поставщиков ресурсов Azure Resource Manager](resource-provider-operations.md)