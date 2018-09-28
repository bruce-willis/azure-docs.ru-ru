---
title: Как управлять управляемым удостоверением, назначаемым пользователем, используя Azure CLI
description: Пошаговые инструкции по созданию и удалению управляемых удостоверений, назначаемых пользователем, а также получению их списка с помощью Azure CLI.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 19963edc3742cc5f09ed02249d313728410711c8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973502"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-the-azure-cli"></a>Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью Azure CLI

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в службах, которые поддерживают аутентификацию Azure AD, без учетных данных в коде. 

В этой статье вы узнаете, как создавать и удалять управляемые удостоверения, назначаемые пользователем, а также получать их список с помощью Azure CLI.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания, чтения (получения списка) и удаления управляемых удостоверений, назначаемых пользователем.
    - Роль [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для чтения (получения списка) свойств управляемого удостоверения, назначаемого пользователем.
- Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:
    - использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
    - использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
    - [установить последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или выше), если вы предпочитаете использовать локальную консоль CLI. Войдите в Azure с помощью `az login`. Используйте учетную запись, которая связана с подпиской Azure, с помощью которой нужно развернуть управляемое удостоверение, назначаемое пользователем.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-user-assigned-managed-identity"></a>Создание управляемого удостоверения, назначаемого пользователем 

Чтобы создать управляемое удостоверение, назначаемое пользователем, используйте команду [az identity create](/cli/azure/identity#az-identity-create). Параметр `-g` указывает группу ресурсов, в которой создано управляемое удостоверение, назначаемое пользователем, а параметр `-n` — его имя. Замените значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными значениями.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Получение списка управляемых удостоверений, назначаемых пользователем

Для получения списка управляемых удостоверений, назначаемых пользователем, используйте команду [az identity list](/cli/azure/identity#az-identity-list). Замените `<RESOURCE GROUP>` собственным значением.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```
В ответе JSON управляемые удостоверения, назначаемые пользователем, содержат значение `"Microsoft.ManagedIdentity/userAssignedIdentities"`, возвращаемое для ключа `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Удаление управляемого удостоверения, назначаемого пользователем

Для удаления управляемого удостоверения, назначаемого пользователем, используйте команду [az identity delete](/cli/azure/identity#az-identity-delete).  Параметр -n указывает имя, а параметр -g — группу ресурсов, в которой было создано управляемое удостоверение, назначаемое пользователем. Замените значения параметров `<USER ASSIGNED IDENTITY NAME>` и `<RESOURCE GROUP>` собственными:

 ```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> При удалении управляемого удостоверения, назначаемого пользователем, не удаляются ссылки из ресурсов, которым оно было назначено. Удалите их из виртуальной машины или VMSS с помощью команды `az vm/vmss identity remove`.

## <a name="next-steps"></a>Дополнительная информация

Полный список команд Azure CLI, используемых для работы с удостоверениями, см. в разделе [az identity](/cli/azure/identity).

Сведения о том, как задать управляемое удостоверение, назначаемое пользователем, для виртуальной машины Azure, см. в статье [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью Azure CLI](qs-configure-cli-windows-vm.md#user-assigned-managed-identity).


 
