---
title: Настройка удостоверений, назначаемых системой и пользователем, в масштабируемом наборе виртуальных машин Azure с помощью интерфейса командной строки Azure
description: Пошаговые инструкции по настройке удостоверений, назначаемых системой и пользователем, в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI.
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
ms.openlocfilehash: 225fd7800f05514e989ec0153b5de22e63b62bde
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42142607"
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Настройка управляемого удостоверения службы (MSI) в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение службы предоставляет службы Azure с автоматически управляемыми удостоверениями в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с Управляемым удостоверением службы в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI:
- включать и отключать назначенное системой удостоверение в масштабируемом наборе виртуальных машин Azure;
- добавлять и удалять назначенное пользователем удостоверение в масштабируемом наборе виртуальных машин Azure.


## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания масштабируемого набора виртуальных машин, а также для включения и удаления назначаемого системой и (или) пользователем управляемого удостоверения из масштабируемого набора виртуальных машин.
    - [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) для создания назначаемого пользователем удостоверения.
    - [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator): для назначения и удаления назначаемого пользователем удостоверения в масштабируемом наборе виртуальных машин.
- Выполнить примеры сценариев для интерфейса командной строки можно тремя способами:
    - использовать [Azure Cloud Shell](../../cloud-shell/overview.md) с портала Azure (см. следующий раздел).
    - использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробуйте!", расположенной в правом верхнем углу каждого блока кода.
    - Если вы предпочитаете использовать локальную консоль CLI, [установите последнюю версию интерфейса командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
      
      > [!NOTE]
      > Команды были обновлены в соответствии с последней версией [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Системное удостоверение

В этом разделе вы узнаете, как включить и отключить назначенное системой удостоверение в масштабируемом наборе виртуальных машин Azure с помощью Azure CLI.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Включение назначенного системой удостоверения во время создания масштабируемого набора виртуальных машин Azure

Чтобы создать масштабируемый набор виртуальных машин с включенным удостоверением, назначенным системой, выполните указанные ниже действия.

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, с помощью которой нужно развернуть масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы сохранить и развернуть масштабируемый набор виртуальных машин и связанные с ним ресурсы, создайте [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#terminology) с помощью команды [az group create](/cli/azure/group/#az-group-create). Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss/#az-vmss-create). В приведенном ниже примере создается масштабируемый набор виртуальных машин *myVMSS* с назначенным системой удостоверением в соответствии с запросом параметра `--assign-identity`. В параметрах `--admin-username` и `--admin-password` определяются имя и пароль учетной записи администратора для входа в виртуальную машину. Подставьте соответствующие значения для своей среды: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Включение назначенного системой удостоверения в существующем масштабируемом наборе виртуальных машин Azure

Если нужно включить назначенное системой удостоверение в существующем масштабируемом наборе виртуальных машин Azure, выполните указанные ниже действия.

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, которая содержит масштабируемый набор виртуальных машин.

   ```azurecli-interactive
   az login
   ```

2. Чтобы включить назначенное системой удостоверение в имеющейся виртуальной машине, используйте команду [az vmss identity assign](/cli/azure/vmss/identity/#az-vmss-identity-assign):

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение назначенного системой удостоверения в масштабируемом наборе виртуальных машин Azure

Если в масштабируемом наборе виртуальных машин не требуется назначаемое системой удостоверение, но по-прежнему требуются назначаемые пользователем удостоверения, используйте следующую команду.

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type='UserAssigned' 
```

Если имеется виртуальная машина, для которой не требуется назначаемое системой удостоверение и на которой нет назначаемых пользователем удостоверений, используйте следующую команду.

> [!NOTE]
> В значении `none` учитывается регистр. Оно должно содержать строчные буквы. 

```azurecli-interactive
az vmss update -n myVM -g myResourceGroup --set identity.type="none"
```

Чтобы удалить расширение MSI для виртуальной машины, используйте команду [az vmss identity remove](/cli/azure/vmss/identity/#az-vmss-remove-identity) для удаления назначенного системой удостоверения из масштабируемого набора виртуальных машин:

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

В этом разделе вы узнаете, как добавить и удалить назначенное пользователем удостоверение с помощью Azure CLI.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Назначение пользовательского удостоверения при создании масштабируемого набора виртуальных машин

В этом разделе описывается создание масштабируемого набора виртуальных машин и присвоение ему назначенного пользователем удостоверения. Если у вас уже есть масштабируемый набор виртуальных машин, который можно использовать, пропустите этот раздел и перейдите к следующему.

1. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить. Создайте [группу ресурсов](~/articles/azure-resource-manager/resource-group-overview.md#terminology) для хранения и развертывания назначенного пользователем удостоверения, используя команду [az group create](/cli/azure/group/#az-group-create). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<LOCATION>` собственными. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Создайте назначенное пользователем удостоверение с помощью команды [az identity create](/cli/azure/identity#az-identity-create).  Параметр `-g` указывает группу ресурсов, в которой создается назначенное пользователем удостоверение, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными:

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurecli-interactive
   az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
   ```
   Ответ содержит подробные сведения о созданном назначенном пользователем удостоверении, подобные приведенным ниже. Значение `id` ресурса, присвоенное назначенному пользователем удостоверению, используется в следующем шаге.

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

3. Создайте масштабируемый набор виртуальных машин с помощью команды [az vmss create](/cli/azure/vmss/#az-vmss-create). В приведенном ниже примере создается масштабируемый набор виртуальных машин, связанный с новым назначенным пользователем удостоверением, в соответствии с параметром `--assign-identity`. Не забудьте заменить значения параметров `<RESOURCE GROUP>`, `<VMSS NAME>`, `<USER NAME>`, `<PASSWORD>` и `<USER ASSIGNED IDENTITY>` своими значениями. 

   ```azurecli-interactive 
   az vmss create --resource-group <RESOURCE GROUP> --name <VMSS NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <USER ASSIGNED IDENTITY>
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-virtual-machine-scale-set"></a>Установите пользовательское удостоверения существующему масштабируемому набору виртуальных машин

1. Создайте назначенное пользователем удостоверение с помощью команды [az identity create](/cli/azure/identity#az-identity-create).  Параметр `-g` указывает группу ресурсов, в которой создается назначенное пользователем удостоверение, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<USER ASSIGNED IDENTITY NAME>` собственными:

    > [!IMPORTANT]
    > При создании назначенных пользователем удостоверений не поддерживается использование специальных символов (например, символа подчеркивания) в имени. Используйте буквенно-цифровые символы. Загляните сюда позже, чтобы проверить наличие новой информации.  Дополнительные сведения см. в разделе [Часто задаваемые вопросы и известные проблемы](known-issues.md).

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
    ```
Ответ содержит подробные сведения о созданном назначенном пользователем удостоверении, подобные приведенным ниже.

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

2. Присвойте назначенное пользователем удостоверение масштабируемому набору виртуальных машин с помощью команды [az vmss identity assign](/cli/azure/vmss/identity#az-vm-assign-identity). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<USER ASSIGNED IDENTITY>` — свойство ресурса `name` назначенного пользователем удостоверения, созданное на предыдущем шаге.

    ```azurecli-interactive
    az vmss identity assign -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
    ```

### <a name="remove-a-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Удаление назначаемого пользователем удостоверения из масштабируемого набора виртуальных машин Azure

Чтобы удалить назначаемое пользователем удостоверение из масштабируемого набора виртуальных машин, используйте команду [az vmss identity remove](/cli/azure/vmss/identity#az-vmss-identity-remove). Если это только назначенное пользователем удостоверение, присвоенное масштабируемому набору виртуальных машин, то `UserAssigned` будет удалено из значения типа удостоверения.  Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VMSS NAME>` собственными. `<USER ASSIGNED IDENTITY>` будет свойством `name` назначенного пользователем удостоверения, которое можно найти в разделе удостоверений виртуальной машины с помощью команды `az vmss identity show`.

```azurecli-interactive
az vmss identity remove -g <RESOURCE GROUP> -n <VMSS NAME> --identities <USER ASSIGNED IDENTITY>
```

Если в масштабируемом наборе виртуальных машин нет назначаемого системой удостоверения и вы хотите удалить из него все назначаемые пользователем удостоверения, используйте следующую команду.

> [!NOTE]
> В значении `none` учитывается регистр. Оно должно содержать строчные буквы.

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type="none" identity.userAssignedIdentities=null
```

Если в масштабируемом наборе виртуальных машин есть как назначаемое системой, так и назначаемые пользователем удостоверения, вы можете удалить все назначаемые пользователем удостоверения, переключившись на использование только назначаемого системой удостоверения. Используйте следующую команду:

```azurecli-interactive
az vmss update -n myVMSS -g myResourceGroup --set identity.type='SystemAssigned' identity.userAssignedIdentities=null 
```

## <a name="next-steps"></a>Дополнительная информация

- [Управляемое удостоверение службы (MSI) для Azure Active Directory](overview.md)
- Полное руководство по созданию масштабируемого набора виртуальных машин см. здесь: 

  - [Создание масштабируемого набора виртуальных машин с помощью CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

















