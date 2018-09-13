---
title: Как настроить управляемые удостоверения, назначаемые системой и назначаемые пользователем, в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI
description: Пошаговые инструкции по настройке управляемых удостоверений, назначаемых системой и назначаемых пользователем, в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI.
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: d5c285f13ce45047619b327b9440fbd69c18267e
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338580"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-azure-cli"></a>Настройка управляемых удостоверений для ресурсов Azure в масштабируемом наборе виртуальных машин с помощью Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с управляемыми удостоверениями для ресурсов Azure в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI.
- Включение и отключение управляемого удостоверения, назначаемого системой, в масштабируемом наборе виртуальных машин Azure.
- Добавление и удаление управляемого удостоверения, назначаемого пользователем, в масштабируемом наборе виртуальных машин Azure.


## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания масштабируемого набора виртуальных машин, а также для включения и удаления управляемого удостоверения, назначаемого системой, и (или) управляемого удостоверения, назначаемого пользователем, из масштабируемого набора виртуальных машин.
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor): для создания управляемого удостоверения, назначаемого пользователем.
    - Роль [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator): для назначения и удаления управляемого удостоверения, назначаемого пользователем, в масштабируемом наборе виртуальных машин.
- Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:
    - использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
    - использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
    - Если вы предпочитаете использовать локальную консоль CLI, [установите последнюю версию интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
      
      > [!NOTE]
      > Команды были обновлены в соответствии с последней версией [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое системой

В этом разделе вы узнаете, как включить и отключить управляемое удостоверение, назначаемое системой, в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Включение управляемого удостоверения, назначаемого системой, во время создания масштабируемого набора виртуальных машин Azure

Чтобы создать масштабируемый набор виртуальных машин с включенным управляемым удостоверением, назначаемым системой, сделайте следующее.

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, с помощью которой нужно развернуть масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы сохранить и развернуть масштабируемый набор виртуальных машин и связанные с ним ресурсы, создайте [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#terminology) с помощью команды [az group create](/cli/azure/group/#az-group-create). Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss/#az-vmss-create). В приведенном ниже примере создается масштабируемый набор виртуальных машин *myVMSS* с заданным управляемым удостоверением, назначаемым системой, как указывает параметр `--assign-identity`. В параметрах `--admin-username` и `--admin-password` определяются имя и пароль учетной записи администратора для входа в виртуальную машину. Подставьте соответствующие значения для своей среды: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Включение управляемого удостоверения, назначаемого системой, в существующем масштабируемом наборе виртуальных машин Azure

Если нужно включить управляемое удостоверение, назначаемое системой, в существующем масштабируемом наборе виртуальных машин Azure, сделайте следующее.

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы включить управляемое удостоверение, назначаемое системой, в имеющейся виртуальной машине, используйте команду [az vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign).

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение управляемого удостоверения, назначаемого системой, в масштабируемом наборе виртуальных машин Azure

Если в масштабируемом наборе виртуальных машин не требуется управляемое удостоверение, назначаемое системой, но по-прежнему требуются управляемые удостоверения, назначаемые пользователем, используйте следующую команду.

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Если имеется виртуальная машина, для которой не требуется управляемое удостоверение, назначаемое системой, и у которой нет управляемых удостоверений, назначаемых пользователем, используйте следующую команду.

> [!NOTE]
> В значении `none` учитывается регистр. Оно должно содержать строчные буквы. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

Чтобы удалить расширение виртуальной машины для управляемых удостоверений для ресурсов Azure, используйте команду [az vmss identity remove](/cli/azure/vmss/identity/#az-vmss-remove-identity) для удаления управляемого удостоверения, назначаемого системой, из масштабируемого набора виртуальных машин.

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое пользователем

В этом разделе вы узнаете, как добавить и удалить управляемое удостоверение, назначаемое пользователем, с помощью Azure CLI.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Задание управляемого удостоверения, назначаемого пользователем, при создании масштабируемого набора виртуальных машин

В этом разделе описывается создание масштабируемого набора виртуальных машин и задание для него управляемого удостоверения, назначаемого пользователем. Если у вас уже есть масштабируемый набор виртуальных машин, который можно использовать, пропустите этот раздел и перейдите к следующему.

1. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить. Создайте [группу ресурсов](~/articles/azure-resource-manager/resource-group-overview.md#terminology) для хранения и развертывания управляемого удостоверения, назначаемого пользователем, используя команду [az group create](/cli/azure/group/#az-group-create). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<LOCATION>` собственными. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Создайте управляемое удостоверение, назначаемое пользователем, с помощью команды [az identity create](/cli/azure/identity#az-identity-create).  Параметр `-g` указывает группу ресурсов, в которой создается управляемое удостоверение, назначаемое пользователем, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными:

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Ответ содержит подробные сведения о созданном управляемом удостоверении, назначаемом пользователем, как показано ниже. Значение `id` ресурса, присвоенное управляемому удостоверению, назначаемому пользователем, используется на следующем шаге.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss/#az-vmss-create). В приведенном ниже примере создается масштабируемый набор виртуальных машин, связанный с новым управляемым удостоверением, назначаемым пользователем, в соответствии с параметром `--assign-identity`. Не забудьте заменить значения параметров `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` и `<USER ASSIGNED IDENTITY>` своими значениями. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Задание управляемого удостоверения, назначаемого пользователем, для существующего масштабируемого набора виртуальных машин

1. Создайте управляемое удостоверение, назначаемое пользователем, с помощью команды [az identity create](/cli/azure/identity#az-identity-create).  Параметр `-g` указывает группу ресурсов, в которой создается управляемое удостоверение, назначаемое пользователем, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными:

    > [!IMPORTANT]
    > При создании управляемых удостоверений, назначаемых пользователем, не поддерживается использование специальных знаков (например, символа подчеркивания) в имени. Используйте буквенно-цифровые символы. Загляните сюда позже, чтобы проверить наличие новой информации.  Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Ответ содержит подробные сведения о созданном управляемом удостоверении, назначаемом пользователем, как показано ниже.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY >/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY>",
        "location": "westcentralus",
        "name": "<USER ASSIGNED IDENTITY>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Задайте управляемое удостоверение, назначаемое пользователем, для масштабируемого набора виртуальных машин с помощью команды [az vmss identity assign](/cli/azure/vmss/identity#az-vm-assign-identity). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<USER ASSIGNED IDENTITY>` — свойство ресурса `name` удостоверения, назначаемого пользователем, созданное на предыдущем шаге.

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Удаление управляемого удостоверения, назначаемого пользователем, из масштабируемого набора виртуальных машин Azure

Чтобы удалить управляемое удостоверение, назначаемое пользователем, из масштабируемого набора виртуальных машин, используйте команду [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Если это единственное управляемое удостоверение, назначаемое пользователем, заданное для масштабируемого набора виртуальных машин, то `UserAssigned` будет удалено из значения типа удостоверения.  Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<USER ASSIGNED IDENTITY>` будет свойством `name` управляемого удостоверения, назначаемого пользователем, которое можно найти в разделе удостоверений виртуальной машины с помощью команды `az vmss identity show`.

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
```

Если в масштабируемом наборе виртуальных машин нет управляемого удостоверения, назначаемого системой, и вы хотите удалить из него все управляемые удостоверения, назначаемые пользователем, используйте следующую команду.

> [!NOTE]
> В значении `none` учитывается регистр. Оно должно содержать строчные буквы.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Если в масштабируемом наборе виртуальных машин есть управляемые удостоверения, как назначаемое системой, так и назначаемые пользователем, вы можете удалить все управляемые удостоверения, назначаемые пользователем, переключившись на использование только управляемого удостоверения, назначаемого системой. Используйте следующую команду:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Дополнительная информация

- [Обзор управляемых удостоверений для ресурсов Azure](overview.md).
- Полное руководство по созданию масштабируемого набора виртуальных машин см. здесь: 

  - [Создание масштабируемого набора виртуальных машин с помощью CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















