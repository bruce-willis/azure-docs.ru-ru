---
title: Как управлять управляемыми удостоверениями Azure, назначаемыми пользователем, с помощью REST
description: Пошаговые инструкции по созданию и удалению управляемых удостоверений, назначаемых пользователем, а также получению их списка для выполнения вызовов REST API.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/26/2018
ms.author: daveba
ms.openlocfilehash: 72f098cac32422ddbd2632336dd82cfc09a8bc93
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344698"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью вызовов REST API

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Управляемые удостоверения для ресурсов Azure можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без указания учетных данных в коде. 

В этой статье вы узнаете, как создавать и удалять управляемые удостоверения, назначаемые пользователем, а также получать их список с помощью CURL для выполнения вызовов REST API.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Если вы используете Windows, установите [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или используйте [Azure Cloud Shell](../../cloud-shell/overview.md) на портале Azure.
- Если вы используете [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или [дистрибутив Linux OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [установите локальную консоль Azure CLI](/cli/azure/install-azure-cli).
- Если вы используете локальную консоль Azure CLI, войдите в Azure с помощью `az login` с учетной записью, привязанной к подписке Azure, в которой вы хотите развернуть управляемое удостоверение, назначаемое пользователем, или получить его.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания, чтения (получения списка) и удаления управляемых удостоверений, назначаемых пользователем.
    - Роль [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для чтения (получения списка) свойств управляемого удостоверения, назначаемого пользователем.
- Извлечение маркера доступа носителя с помощью `az account get-access-token` для выполнения следующих операций с управляемыми удостоверениями, назначаемыми пользователем.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем 

Чтобы создать управляемое удостоверение, назначаемое пользователем, используйте следующий запрос CURL к API Azure Resource Manager. Замените значения `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>` и `<ACCESS TOKEN>` своими значениями:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Получение списка управляемых удостоверений, назначаемых пользователем

Чтобы отобразить список управляемых удостоверений, назначаемых пользователем, используйте следующий запрос CURL к API Azure Resource Manager. Замените значения `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<ACCESS TOKEN>` своими значениями:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Удаление управляемого удостоверения, назначаемого пользователем

Чтобы удалить управляемое удостоверение, назначаемое пользователем, используйте следующий запрос CURL к API Azure Resource Manager. Замените значения параметров `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<ACCESS TOKEN>` своими значениями:

> [!NOTE]
> При удалении управляемого удостоверения, назначаемого пользователем, не удаляются ссылки из ресурсов, которым оно было назначено. Чтобы удалить управляемое удостоверение, назначаемое пользователем, из виртуальной машины с помощью CURL, обратитесь к разделу [Удаление назначенного пользователем удостоверения из виртуальной машины Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины или масштабируемого набора виртуальных машин Azure, см. в разделах [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью вызовов REST API](qs-configure-rest-vm.md#user-assigned-managed-identity) и [Настройка управляемых удостоверений для ресурсов Azure в масштабируемом наборе виртуальных машин с помощью вызовов REST API](qs-configure-rest-vmss.md#user-assigned-managed-identity).


