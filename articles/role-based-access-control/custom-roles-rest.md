---
title: Создание пользовательских ролей с помощью REST API — Azure | Документы Майкрософт
description: Узнайте, как создавать пользовательские роли для управления доступом на основе ролей (RBAC) с помощью REST API. Здесь приводится описание процедур получения списка, создания, обновления и удаления пользовательских ролей.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 8a1bbe8217e2d4a9846f56124e248e19cbe70b19
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436068"
---
# <a name="create-custom-roles-using-the-rest-api"></a>Создание пользовательских ролей с помощью REST API

Если [встроенные роли](built-in-roles.md) не соответствуют потребностям вашей организации, вы можете создать собственные пользовательские роли. В этой статье описывается создание пользовательских ролей и управление ими с помощью REST API.

## <a name="list-roles"></a>Вывод списка ролей

Для получения списка всех ролей или сведений об одной роли по ее отображаемому имени используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list). Для вызова этого API необходим доступ к операции `Microsoft.Authorization/roleDefinitions/read` в области. Доступ к этой операции предоставляется нескольким [встроенным ролям](built-in-roles.md).

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. В URI замените *{scope}* на область, для которой нужно получить список ролей.

    | Область | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Подписка |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{filter}* на условие, по которому требуется отфильтровать список ролей.

    | Фильтр | ОПИСАНИЕ |
    | --- | --- |
    | `$filter=atScopeAndBelow()` | Вывод списка ролей, доступных для назначения в указанной области и любой из ее дочерних областей. |
    | `$filter=roleName%20eq%20'{roleDisplayName}'` | Используйте точное отображаемое имя роли в формате URL-адреса. Например, `$filter=roleName%20eq%20'Virtual%20Machine%20Contributor'` |

### <a name="get-information-about-a-role"></a>Получение сведений о роли

Для получения сведений о роли по ее идентификатору используйте REST API [Определения ролей — получение](/rest/api/authorization/roledefinitions/get). Для вызова этого API необходим доступ к операции `Microsoft.Authorization/roleDefinitions/read` в области. Доступ к этой операции предоставляется нескольким [встроенным ролям](built-in-roles.md).

Чтобы получить сведения о роли по ее отображаемому имени, ознакомьтесь с предыдущим разделом [Вывод списка ролей](custom-roles-rest.md#list-roles).

1. Для получения идентификатора GUID роли используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list). Для встроенных ролей вы также можете получить идентификатор, обратившись к разделу [Встроенные роли](built-in-roles.md).

1. Можете начать со следующего запроса:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на область, для которой нужно получить список ролей.

    | Область | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Подписка |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{roleDefinitionId}* на идентификатор GUID определения роли.

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Чтобы создать пользовательскую роль, используйте REST API [Определения ролей — создание или обновление](/rest/api/authorization/roledefinitions/createorupdate). Для вызова этого API необходим доступ к операции `Microsoft.Authorization/roleDefinitions/write` для всех `assignableScopes`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator). 

1. Просмотрите список доступных [операций поставщиков ресурсов](resource-provider-operations.md) для создания разрешений для пользовательской роли.

1. Используйте средство GUID для создания уникального идентификатора, который будет использоваться в качестве идентификатора пользовательской роли. Этот идентификатор имеет следующий формат: `00000000-0000-0000-0000-000000000000`

1. Можете начать со следующего запроса и основного текста:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. В URI замените *{scope}* на первую область `assignableScopes` пользовательской роли.

    | Область | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Подписка |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{role-definition-id}* на идентификатор GUID пользовательской роли.

1. В тексте запроса в свойстве `assignableScopes` замените *{roleDefinitionId}* на идентификатор GUID.

1. Замените *{subscriptionId}* на идентификатор подписки.

1. В свойстве `actions` добавьте операции, которые разрешают выполнение роли.

1. В свойстве `notActions` добавьте операции, которые исключены из допустимых `actions`.

1. В свойствах `roleName` и `description` укажите уникальное имя и описание роли. Дополнительные сведения о свойствах см. в разделе [Пользовательские роли](custom-roles.md).

    Ниже приведен пример текста запроса:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
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
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Обновление пользовательской роли

Чтобы обновить пользовательскую роль, используйте REST API [Определения ролей — создание или обновление](/rest/api/authorization/roledefinitions/createorupdate). Для вызова этого API необходим доступ к операции `Microsoft.Authorization/roleDefinitions/write` для всех `assignableScopes`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator). 

1. Чтобы получить сведения о пользовательской роли, используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list) или [Определения ролей — получение](/rest/api/authorization/roledefinitions/get). Дополнительные сведения см. в разделе [Список ролей](custom-roles-rest.md#list-roles) выше.

1. Можете начать со следующего запроса:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на первую область `assignableScopes` пользовательской роли.

    | Область | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Подписка |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{role-definition-id}* на идентификатор GUID пользовательской роли.

1. На основе сведений о пользовательской роли создайте текст запроса в следующем формате:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId}"
        ]
      }
    }
    ```

1. Внесите необходимые изменения для пользовательской роли в текст запроса.

    Ниже приведен пример текста запроса с добавленным действием "Параметры диагностики":

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
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
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Удаление настраиваемой роли

Чтобы удалить пользовательскую роль, используйте REST API [Определения ролей — удаление](/rest/api/authorization/roledefinitions/delete). Для вызова этого API необходим доступ к операции `Microsoft.Authorization/roleDefinitions/delete` для всех `assignableScopes`. Из встроенных ролей эту операцию могут выполнять только [владелец](built-in-roles.md#owner) и [администратор доступа пользователей](built-in-roles.md#user-access-administrator). 

1. Чтобы получить идентификатор GUID пользовательской роли, используйте REST API [Определения ролей — список](/rest/api/authorization/roledefinitions/list) или [Определения ролей — получение](/rest/api/authorization/roledefinitions/get). Дополнительные сведения см. в разделе [Список ролей](custom-roles-rest.md#list-roles) выше.

1. Можете начать со следующего запроса:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. В URI замените *{scope}* на область, для которой нужно удалить пользовательскую роль.

    | Область | type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Подписка |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Группа ресурсов |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Ресурс |

1. Замените *{role-definition-id}* на идентификатор GUID пользовательской роли.

## <a name="next-steps"></a>Дополнительная информация

- [Пользовательские роли в Azure](custom-roles.md)
- [Управление доступом с помощью RBAC и REST API](role-assignments-rest.md)
- [Справочник по REST API Azure](/rest/api/azure/)