---
title: Как управлять управляемыми удостоверениями Azure, назначаемыми пользователем, с помощью REST
description: Пошаговые инструкции по созданию, получению списка и удалению управляемых удостоверений, назначаемых пользователем, для совершения вызовов REST API.
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
ms.openlocfilehash: a6241c105019f04df09080a89e8fe3b77b5f9385
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888769"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-rest-api-calls"></a>Создание, получение списка и удаление пользовательских удостоверений с помощью вызовов REST API

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Это управляемое удостоверение можно использовать для проверки подлинности в службах, которые поддерживают проверку подлинности Azure AD, без учетных данных в коде. 

В этой статье вы узнаете, как создавать, получать список и удалять управляемое удостоверение, назначаемое пользователем, с помощью CURL для совершения вызовов REST API.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Если вы используете Windows, установите [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или используйте [Azure Cloud Shell](../../cloud-shell/overview.md) на портале Azure.
- Если вы используете [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или [дистрибутив Linux OS](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [установите локальную консоль Azure CLI](/cli/azure/install-azure-cli).
- Если вы используете локальную консоль Azure CLI, войдите в Azure с помощью `az login` с учетной записью, привязанной к подписке Azure, где вы хотите развернуть или получить управляемое удостоверение, назначаемое пользователем.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания, чтения (вывода списка) и удаления назначаемого пользователем удостоверения.
    - [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для чтения (вывода списка) свойств назначаемого пользователем удостоверения.
- Извлечение маркера доступа носителя с помощью `az account get-access-token` для выполнения следующих операций с управляемыми удостоверениями, назначаемыми пользователем.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого пользовательского удостоверения 

Чтобы создать управляемое удостоверение, назначаемое пользователем, используйте следующий запрос CURL к API Azure Resource Manager. Замените значения `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>`,`<LOCATION>` и `<ACCESS TOKEN>` своими значениями:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="list-user-assigned-managed-identities"></a>Отображение управляемых удостоверений, назначаемых пользователем

Чтобы отобразить список управляемых удостоверений, назначаемых пользователем, используйте следующий запрос CURL к API Azure Resource Manager. Замените значения `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<ACCESS TOKEN>` своими значениями:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```
## <a name="delete-a-user-assigned-managed-identity"></a>Удаление управляемого удостоверения, назначаемого пользователем

Чтобы удалить управляемое удостоверение, назначаемое пользователем, используйте следующий запрос CURL к API Azure Resource Manager. Замените значения параметров `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<ACCESS TOKEN>` своими значениями:

> [!NOTE]
> Удаление пользовательского удостоверения не удаляет ссылки из ресурсов, которым оно было назначено. Чтобы удалить управляемое удостоверение, назначаемое пользователем, из виртуальной машины с помощью CURL, см. раздел [Удаление назначенного пользователем удостоверения из виртуальной машины Azure](qs-configure-rest-vm.md#remove-a-user-assigned identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как назначить пользовательское удостоверение для виртуальной машины или масштабируемого набора виртуальных машин Azure, см. в разделе [Настройка управляемого удостоверения на виртуальной машине Azure с помощью CURL](qs-configure-rest-vm.md#user-assigned-identity) и [Настройка управляемого удостоверения на масштабируемом наборе виртуальных машин с помощью CURL ](qs-configure-rest-vmss.md#user-assigned-identity).


