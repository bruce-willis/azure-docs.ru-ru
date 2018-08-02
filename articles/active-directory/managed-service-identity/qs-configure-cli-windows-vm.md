---
title: Настройка удостоверений, назначаемых системой и пользователем, в виртуальной машине Azure с помощью интерфейса командной строки Azure
description: Пошаговые инструкции по настройке удостоверений, назначаемых системой и пользователем, в виртуальной машине Azure с помощью Azure CLI.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: cb23db13d67047225102c6888e27e8f79a3e5abf
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259319"
---
# <a name="configure-managed-service-identity-on-an-azure-vm-using-azure-cli"></a>Настройка управляемого удостоверения службы на виртуальной машине Azure с помощью Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с Управляемым удостоверением службы в виртуальной машине Azure VM с помощью Azure CLI:
- включать и отключать назначенное системой удостоверение в виртуальной машине Azure;
- добавлять и удалять назначенное пользователем удостоверение в виртуальной машине Azure.

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи требуются следующие роли.
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания виртуальной машины, а также включения и удаления назначаемого системой и (или) пользователем управляемого удостоверения для виртуальной машины Azure.
    - [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания назначаемого пользователем удостоверения.
    - [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для назначения и удаления назначаемого пользователем удостоверения для виртуальной машины и из нее.
- Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:
    - использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
    - использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
    - [установить последнюю версию интерфейса командной строки 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 или более позднюю версию), если вы предпочитаете использовать локальную консоль интерфейса командной строки. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Системное удостоверение

В этом разделе вы узнаете, как включить и отключить назначенное системой удостоверение в виртуальной машине Azure с помощью Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Включение назначенного системой удостоверения во время создания виртуальной машины Azure

Чтобы создать виртуальную машину Azure с включенным удостоверением, назначенным системой, выполните указанные ниже действия.

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Используйте учетную запись, которая связана с подпиской Azure, с помощью которой нужно развернуть виртуальную машину.

   ```azurecli-interactive
   az login
   ```

2. Создайте [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#terminology) с помощью параметра [az group create](/cli/azure/group/#az_group_create), чтобы сохранить и развернуть виртуальную машину и связанные с ней ресурсы. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Создайте виртуальную машину, выполнив команду [az vm create](/cli/azure/vm/#az_vm_create). В приведенном ниже примере создается виртуальная машина *myVM* с назначенным системой удостоверением, как запрошено параметром `--assign-identity`. В параметрах `--admin-username` и `--admin-password` определяются имя и пароль учетной записи администратора для входа в виртуальную машину. Подставьте соответствующие значения для своей среды: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Включение назначенного системой удостоверения в имеющейся виртуальной машине Azure

Если нужно включить назначенное системой удостоверение в имеющейся виртуальной машине Azure, выполните указанные ниже действия.

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az_login). Используйте учетную запись, связанную с подпиской Azure, которая содержит виртуальную машину.

   ```azurecli-interactive
   az login
   ```

2. Чтобы включить назначенное системой удостоверение в имеющейся виртуальной машине, используйте команду [az vm identity assign](/cli/azure/vm/identity/#az_vm_identity_assign) с параметром `identity assign`:

   ```azurecli-interactive
   az vm identity assign -g myResourceGroup -n myVm
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Отключение назначенного системой удостоверения в виртуальной машине Azure

Если на виртуальной машине больше не требуется назначаемое системой удостоверение, но по-прежнему требуются назначаемые пользователем удостоверения, используйте следующую команду.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Если имеется виртуальная машина, для которой не требуется назначаемое системой удостоверение и на которой нет назначаемых пользователем удостоверений, используйте следующую команду.

> [!NOTE]
> В значении `none` учитывается регистр. Оно должно содержать строчные буквы. 

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none"
```

Чтобы удалить расширение виртуальной машины MSI, выполните команду [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) с параметром `-n ManagedIdentityExtensionForWindows` или `-n ManagedIdentityExtensionForLinux` (в зависимости от типа виртуальной машины).

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы узнаете, как добавить и удалить назначаемое пользователем удостоверение для виртуальной машины Azure с помощью Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Добавление назначенного пользователем удостоверения во время создания виртуальной машины Azure

В этом разделе описывается создание виртуальной машины и присвоение ей назначенного пользователем удостоверения. Если у вас уже есть виртуальная машина, которую можно использовать, пропустите этот раздел и перейдите к следующему.

1. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить. Создайте [группу ресурсов](~/articles/azure-resource-manager/resource-group-overview.md#terminology) для хранения и развертывания компонента "Управляемое удостоверение службы", используя команду [az group create](/cli/azure/group/#az_group_create). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<LOCATION>` собственными. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Создайте назначенное пользователем удостоверение с помощью команды [az identity create](/cli/azure/identity#az_identity_create).  Параметр `-g` указывает группу ресурсов, в которой создается назначенное пользователем удостоверение, а параметр `-n` — его имя.    
    
   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g myResourceGroup -n myUserAssignedIdentity
   ```
   Ответ содержит подробные сведения о созданном назначенном пользователем удостоверении, подобные приведенным ниже. Значение идентификатора ресурса, присвоенное назначенному пользователем удостоверению, используется в следующем шаге.

   ```json
   {
       "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
       "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
       "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
       "location": "westcentralus",
       "name": "<MSI NAME>",
       "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
       "resourceGroup": "<RESOURCE GROUP>",
       "tags": {},
       "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
       "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Создайте виртуальную машину, выполнив команду [az vm create](/cli/azure/vm/#az_vm_create). В приведенном ниже примере создается виртуальная машина, связанная с новым назначенным пользователем удостоверением, в соответствии с параметром `--assign-identity`. Не забудьте заменить значения параметров `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` и `<MSI ID>` своими значениями. В качестве `<MSI ID>` используйте свойство ресурса `id` назначенного пользователем удостоверения, созданное в предыдущем шаге: 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Присвоение назначенного пользователем удостоверения имеющейся виртуальной машине Azure

1. Создайте назначенное пользователем удостоверение с помощью команды [az identity create](/cli/azure/identity#az-identity-create).  Параметр `-g` указывает группу ресурсов, в которой создается назначенное пользователем удостоверение, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<MSI NAME>` собственными:

    > [!IMPORTANT]
    > При создании назначенных пользователем удостоверений не поддерживается использование специальных символов (например, символа подчеркивания) в имени. Используйте буквенно-цифровые символы. Загляните сюда позже, чтобы проверить наличие новой информации.  Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
Ответ содержит подробные сведения о созданном назначаемом пользователем управляемом удостоверении, подобные приведенным ниже. Значение `id` ресурса, присвоенное назначенному пользователем удостоверению, используется в следующем шаге.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Присвойте назначенное пользователем удостоверение виртуальной машине с помощью команды [az vm identity assign](/cli/azure/vm#az-vm-identity-assign). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. `<MSI ID>` будет свойством ресурса `id` назначенного пользователем удостоверения, созданным в предыдущем шаге:

    ```azurecli-interactive
    az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Удаление назначенного пользователем удостоверения из виртуальной машины Azure

Чтобы удалить назначаемое пользователем удостоверение из виртуальной машины, используйте команду [az vm identity remove](/cli/azure/vm#az-vm-identity-remove). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. `<MSI NAME>` будет свойством `name` назначенного пользователем удостоверения, которое можно найти в разделе удостоверений виртуальной машины с помощью команды `az vm identity show`:

```azurecli-interactive
az vm identity remove -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
```

Если на виртуальной машине нет назначаемого системой удостоверения и вы хотите удалить из нее все назначаемые пользователем удостоверения, используйте следующую команду.

> [!NOTE]
> В значении `none` учитывается регистр. Оно должно содержать строчные буквы.

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type="none" identity.identityIds=null
```

Если у виртуальной машины есть как назначенное системой, так и назначенные пользователем удостоверения, вы можете удалить все назначенные пользователем удостоверения, переключившись на использование только назначенного системой. Используйте следующую команду:

```azurecli-interactive
az vm update -n myVM -g myResourceGroup --set identity.type='SystemAssigned' identity.identityIds=null 
```

## <a name="related-content"></a>Связанная информация
- [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md)
- Ниже приведены комплексные краткие руководства по созданию виртуальных машин Azure: 
  - [Создание виртуальной машины Windows с помощью Azure CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Создание виртуальной машины Linux с помощью Azure CLI](../../virtual-machines/linux/quick-create-cli.md) 

















