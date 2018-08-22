---
title: Пользовательские роли в Azure | Документы Майкрософт
description: Сведения об определении пользовательских ролей при управлении доступом на основе ролей (RBAC) для точного управления доступом к ресурсам в Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/07/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 002eb9b70c2f3f9d0f6633b2d81425c688495d19
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714059"
---
# <a name="custom-roles-in-azure"></a>Пользовательские роли в Azure

Если [встроенные роли](built-in-roles.md) не соответствуют потребностям вашей организации, вы можете создать собственные пользовательские роли. Пользовательские роли, так же как и встроенные, можно назначать пользователям, группам и субъектам-службам в рамках подписки, группы ресурсов или области ресурсов. Настраиваемые роли хранятся в клиенте Azure Active Directory (Azure AD) и могут использоваться несколькими подписками. В каждом клиенте можно иметь до 2000 пользовательских ролей. Настраиваемые роли можно создавать с помощью Azure PowerShell, Azure CLI и интерфейса REST API.

## <a name="custom-role-example"></a>Пример пользовательской роли

Ниже представлена пользовательская роль, отображаемая в формате JSON. Эта пользовательская роль позволяет выполнять мониторинг и перезапуск виртуальных машин.

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
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
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

После создания пользовательская роль отображается на портале Azure с оранжевым значком ресурса.

![Имя пользовательской роли](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Процедура создания пользовательской роли

1. Определение требуемых разрешений

    При создании пользовательской роли вам нужно знать, какие операции поставщика ресурсов доступны, чтобы определить разрешения. Чтобы просмотреть список операций, используйте команду [Get-AzureRMProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) или [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).
    Чтобы указать разрешения для пользовательской роли, можно добавить операции в свойство `Actions` или `NotActions` [определения роли](role-definitions.md). При наличии операций с данными их можно добавить в свойство `DataActions` или `NotDataActions`.

2. Создание настраиваемой роли

    Для создания пользовательской роли вы можете использовать Azure PowerShell или Azure CLI. В общем случае вы начинаете с существующей встроенной роли и видоизменяете ее под свои потребности. После этого можно воспользоваться командой [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) или [az role definition create](/cli/azure/role/definition#az-role-definition-create) для создания настраиваемой роли. Чтобы создать пользовательскую роль, нужно иметь разрешение `Microsoft.Authorization/roleDefinitions/write` для всех объектов `AssignableScopes`, таких как [Владелец](built-in-roles.md#owner) или [Администратор доступа пользователей](built-in-roles.md#user-access-administrator).

3. Проверка пользовательской роли

    Создав пользовательскую роль, нужно проверить правильность ее работы. Если позже потребуется корректировка, вы можете обновить пользовательскую роль.

Пошаговое руководство по созданию пользовательской роли см. в статье [Руководство. Создание пользовательских ролей с помощью Azure PowerShell](tutorial-custom-role-powershell.md) или [Руководство. Создание пользовательских ролей с помощью Azure CLI](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Свойства пользовательской роли

Пользовательская роль имеет указанные ниже свойства.

| Свойство | Обязательно | type | ОПИСАНИЕ |
| --- | --- | --- | --- |
| `Name` | Yes | Строка | Отображаемое имя пользовательской роли. Оно должно быть уникальным для клиента. Может содержать буквы, цифры, пробелы и специальные знаки. Максимальное количество знаков — 128. |
| `Id` | Yes | Строка | Уникальный идентификатор настраиваемой роли. Для Azure PowerShell и Azure CLI этот идентификатор формируется автоматически при создании роли. |
| `IsCustom` | Yes | Строка | Указывает, является ли эта роль настраиваемой. Для настраиваемых пролей задайте значение `true`. |
| `Description` | Yes | Строка | Описание пользовательской роли. Может содержать буквы, цифры, пробелы и специальные знаки. Максимальное количество знаков — 1024. |
| `Actions` | Yes | String[] | Массив строк, описывающий операции управления, которые роль разрешает выполнять. Дополнительные сведения см. в разделе [Actions](role-definitions.md#actions). |
| `NotActions` | Нет  | String[] | Массив строк, указывающий операции управления, которые исключаются из разрешенных `Actions`. Дополнительные сведения см. в разделе [NotActions](role-definitions.md#notactions). |
| `DataActions` | Нет  | String[] | Массив строк, указывающий операции с данными, которые роль разрешает выполнять с вашими данными внутри этого объекта. Дополнительные сведения см. в разделе [DataActions (предварительная версия)](role-definitions.md#dataactions-preview). |
| `NotDataActions` | Нет  | String[] | Массив строк, указывающий операции с данными, которые исключаются из разрешенных `DataActions`. Дополнительные сведения см. в разделе [NotDataActions (предварительная версия)](role-definitions.md#notdataactions-preview). |
| `AssignableScopes` | Yes | String[] | Массив строк, который указывает области, в которых пользовательская роль может быть назначена. Сейчас для него нельзя задать корневую область (`"/"`) или область группы управления. Дополнительные сведения см. в разделе [AssignableScopes](role-definitions.md#assignablescopes) и [Пользовательское определение и назначение роли RBAC](../azure-resource-manager/management-groups-overview.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Кто может создавать, удалять, обновлять или просматривать пользовательскую роль

Как и в случае со встроенными ролями, свойство `AssignableScopes` указывает области, в которых роль может быть назначена. Свойство `AssignableScopes` пользовательской роли также определяет, кто может создавать, удалять, изменять или просматривать пользовательскую роль.

| Задача | Операция | ОПИСАНИЕ |
| --- | --- | --- |
| Создание или удаление пользовательской роли | `Microsoft.Authorization/ roleDefinition/write` | Пользователи с разрешением на эту операцию для всех `AssignableScopes` пользовательской роли могут создавать (или удалять) пользовательские роли для использования в этих областях. Например, [владельцы](built-in-roles.md#owner) или [администраторы доступа пользователей](built-in-roles.md#user-access-administrator) подписок, групп ресурсов и ресурсов. |
| Обновление пользовательской роли | `Microsoft.Authorization/ roleDefinition/write` | Пользователи с разрешением на эту операцию для всех `AssignableScopes` пользовательской роли могут обновлять пользовательские роли в этих областях. Например, [владельцы](built-in-roles.md#owner) или [администраторы доступа пользователей](built-in-roles.md#user-access-administrator) подписок, групп ресурсов и ресурсов. |
| Просмотр пользовательской роли | `Microsoft.Authorization/ roleDefinition/read` | Пользователи с разрешением на эту операцию в определенной области могут просматривать пользовательские роли, которые доступны для назначения в этой области. Все встроенные роли обеспечивают доступность пользовательских ролей для назначения. |

## <a name="next-steps"></a>Дополнительная информация
- [Создание пользовательских ролей с помощью Azure PowerShell](custom-roles-powershell.md)
- [Создание пользовательских ролей с помощью Azure CLI](custom-roles-cli.md)
- [Understand role definitions](role-definitions.md) (Определения ролей)
