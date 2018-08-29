---
title: Настройка удостоверений, назначаемых системой и пользователем, в масштабируемом наборе виртуальных машин Azure с помощью REST
description: Пошаговые инструкции по настройке удостоверений, назначаемых системой и пользователем, в масштабируемом наборе виртуальных машин Azure с использованием CURL для выполнения вызовов REST API.
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
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 704342db2d1ff56a66eb70b33d3da10874844f2d
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42144996"
---
# <a name="configure-managed-identity-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Настройка управляемого удостоверения в масштабируемом наборе виртуальных машин с помощью вызовов REST API

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение предоставляет службам Azure автоматически управляемое системное удостоверение в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с управляемым удостоверением для масштабируемого набора виртуальных машин, используя CURL для направления вызовов к конечной точке REST Azure Resource Manager:

- Включение и отключение назначаемого системой удостоверения в масштабируемом наборе виртуальных машин Azure
- Добавление и удаление назначаемого пользователем удостоверения в масштабируемом наборе виртуальных машин Azure

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor): для создания масштабируемого набора виртуальных машин, а также для включения и удаления назначаемого системой и (или) пользователем управляемого удостоверения из масштабируемого набора виртуальных машин.
    - [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor): для создания назначаемого пользователем удостоверения.
    - [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator): для назначения и удаления назначаемого пользователем удостоверения в масштабируемом наборе виртуальных машин.
- Если вы используете Windows, установите [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или используйте [Azure Cloud Shell](../../cloud-shell/overview.md) на портале Azure.
- Если вы используете [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или [ОС на базе дистрибутива Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [установите локальную консоль Azure CLI](/azure/install-azure-cli).
- Если вы используете локальную консоль Azure CLI, войдите в Azure с помощью `az login` с учетной записью, привязанной к подписке Azure, где вы хотите управлять удостоверениями, назначаемые пользователями или системой.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Системное удостоверение

Этот раздел описывает, как включить и отключить системное удостоверение в масштабируемом наборе виртуальных машин, используя CURL для направления вызовов к конечной точке REST Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-a-virtual-machine-scale-set"></a>Включение назначаемого системой удостоверения во время создания масштабируемого набора виртуальных машин

Для создания масштабируемого набора виртуальных машин с включенным удостоверением, назначаемым системой, создайте масштабируемый набор виртуальных машин и получите маркер доступа, чтобы использовать CURL для вызова конечной точки диспетчера ресурсов со значением типа системного удостоверения.

1. Чтобы сохранить и развернуть масштабируемый набор виртуальных машин и связанные с ним ресурсы, создайте [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#terminology) с помощью команды [az group create](/cli/azure/group/#az-group-create). Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Создайте [сетевой интерфейс](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) для масштабируемого набора виртуальных машин:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания масштабируемого набора виртуальных машин с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Создайте масштабируемый набор виртуальных машин, используя CURL для вызова конечной точки REST Azure Resource Manager. Приведенный ниже пример создает в группе ресурсов *myResourceGroup* масштабируемый набор виртуальных машин *myVMSS* с назначаемым системой удостоверением в соответствии со значением `"identity":{"type":"SystemAssigned"}` в тексте запроса. Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
 
    ```bash   
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Включение назначенного системой удостоверения в существующем масштабируемом наборе виртуальных машин Azure

Чтобы включить назначаемое системой удостоверение в существующем масштабируемом наборе виртуальных машин, нужно получить маркер доступа, а затем воспользоваться CURL для вызова конечной точки REST диспетчера ресурсов для изменения типа удостоверения.

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания масштабируемого набора виртуальных машин с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Используйте приведенную ниже команду CURL для вызова конечной точки REST Azure Resource Manager, чтобы включить назначаемое системой удостоверение в масштабируемом наборе виртуальных машин в соответствии со значением `{"identity":{"type":"SystemAssigned"}` в тексте запроса для масштабируемого набора виртуальных машин *myVMSS*.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
   
   > [!IMPORTANT]
   > Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, для масштабируемого набора виртуальных машин, нужно перечислить эти пользовательские удостоверения с помощью этой команды CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Если у вас есть назначаемые пользователем удостоверения для масштабируемого набора виртуальных машин, как указано в значении `identity` в ответе, перейдите к шагу 3, где описано, как сохранить пользовательские удостоверения при включении системного удостоверения в масштабируемом наборе виртуальных машин.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Чтобы включить назначаемое системой удостоверение в масштабируемом наборе виртуальных машин с существующими пользовательскими удостоверениями, нужно добавить `SystemAssigned` в значение `type`.  
   
   Например, если для масштабируемого набора виртуальных машин присвоены назначенные пользователем удостоверения `ID1` и `ID2` и вы хотите добавить для него удостоверение, назначенное системой, используйте приведенный ниже вызов CURL. Замените `<ACCESS TOKEN>` и `<SUBSCRIPTION ID>` значениями, уместными для вашей среды.

   API версии `2018-06-01` хранит пользовательские идентификаторы в значении `userAssignedIdentities` в формате словаря в отличие от значения `identityIds` в формате массива, используемого в версии API `2017-12-01` и более ранних версиях.
   
   **API версии 2018-06-01**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```
   
   **API версии 2017-12-01 и более ранних версий**

   ```bash
   curl -v 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Отключение назначенного системой удостоверения в масштабируемом наборе виртуальных машин Azure

Чтобы отключить назначаемое системой удостоверение в существующем масштабируемом наборе виртуальных машин, нужно получить маркер доступа, а затем воспользоваться CURL для вызова конечной точки REST диспетчера ресурсов для изменения типа удостоверения на `None`.

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания масштабируемого набора виртуальных машин с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Измените масштабируемый набор виртуальных машин, используя CURL для вызова конечной точки REST Azure Resource Manager, чтобы отключить системное удостоверение.  Приведенный ниже пример отключает назначаемое системой удостоверение в масштабируемом наборе виртуальных машин *myVMSS* в соответствии со значением `{"identity":{"type":"None"}}` в тексте запроса.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.

   > [!IMPORTANT]
   > Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, для масштабируемого набора виртуальных машин, нужно перечислить эти пользовательские удостоверения с помощью этой команды CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Если у вас есть назначаемое пользователем удостоверение для масштабируемого набора виртуальных машин, перейдите к шагу 3, где описано, как сохранить пользовательские удостоверения при удалении системного удостоверения из масштабируемого набора виртуальных машин.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Чтобы удалить удостоверение, назначенное системой из масштабируемого набора виртуальных машин, у которого есть удостоверения, назначенные пользователем, удалите `SystemAssigned` из значения `{"identity":{"type:" "}}`, сохраняя значение `UserAssigned` и значения словаря `userAssignedIdentities`, при использовании **API версии 2018-06-01**. При использовании **API версии 2017-12-01** или более ранних версий сохраните массив `identityIds`.

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

Этот раздел описывает, как добавить и удалить пользовательское удостоверение в масштабируемом наборе виртуальных машин, используя CURL для направления вызовов к конечной точке REST Azure Resource Manager.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Назначение пользовательского удостоверения при создании масштабируемого набора виртуальных машин

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания масштабируемого набора виртуальных машин с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Создайте [сетевой интерфейс](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) для масштабируемого набора виртуальных машин:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания масштабируемого набора виртуальных машин с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Создайте пользовательское удостоверение, как описано в разделе [Создание управляемого пользовательского удостоверения](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Создайте масштабируемый набор виртуальных машин, используя CURL для вызова конечной точки REST Azure Resource Manager. Приведенный ниже пример создает в группе ресурсов *myResourceGroup* масштабируемый набор виртуальных машин *myVMSS* с назначаемым пользователем удостоверением `ID1` в соответствии со значением `"identity":{"type":"UserAssigned"}` в тексте запроса. Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
 
   **API версии 2018-06-01**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   **API версии 2017-12-01 и более ранних версий**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Назначение пользовательского удостоверения существующему масштабируемому набору виртуальных машин Azure

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания масштабируемого набора виртуальных машин с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Создайте пользовательское удостоверение, как описано в разделе [Создание управляемого пользовательского удостоверения](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем или системой, для масштабируемого набора виртуальных машин, нужно перечислить назначенные ему типы удостоверений с помощью приведенной ниже команды CURL. Если у вас есть управляемые удостоверения, назначенные масштабируемому набору виртуальных машин, они перечислены в значении `identity`.
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

4. Если вашему масштабируемому набору виртуальных машин не назначены пользовательские или системные удостоверения, используйте приведенную ниже команду CURL для вызова конечной точки REST Azure Resource Manager, чтобы назначить первое пользовательское удостоверение масштабируемому набору виртуальных машин.  Если у вас есть пользовательские или системные удостоверения, назначенные масштабируемому набору виртуальных машин, перейдите к шагу 5, где описано, как добавить несколько назначаемых пользователем удостоверений масштабируемому набору виртуальных машин, сохранив при этом удостоверение, назначаемое системой.

   Следующий пример назначает пользовательское удостоверение `ID1` масштабируемому набору виртуальных машин *myVMSS* в группе ресурсов *myResourceGroup*.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.

   **API версии 2018-06-01**

    ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   
    
   **API версии 2017-12-01 и более ранних версий**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. При наличии удостоверения, назначенного пользователем или системой, для масштабируемого набора виртуальных машин:
   
   **API версии 2018-06-01**

   Добавьте удостоверение, назначенное пользователем, в значение словаря `userAssignedIdentities`.

   Например, если масштабируемой виртуальной машине присвоены удостоверение, назначенное системой, и назначенное пользователем удостоверение `ID1` и вы хотите добавить для него назначенное пользователем удостоверение `ID2`.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API версии 2017-12-01 и более ранних версий**

   При добавлении нового удостоверения, назначенного пользователем, сохраните назначенные пользователем удостоверения, которые вы хотите оставить в значении массива `identityIds`.

   Например, если для масштабируемого набора виртуальных машин присвоено удостоверение, назначенное системой, и назначенное пользователем удостоверение `ID1` и вы хотите добавить для него назначенное пользователем удостоверение `ID2`. 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-a-virtual-machine-scale-set"></a>Удаление назначаемого пользователем удостоверения из масштабируемого набора виртуальных машин

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания масштабируемого набора виртуальных машин с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, которые нужно сохранить для масштабируемого набора виртуальных машин, или удалить системное удостоверение, нужно перечислить управляемые удостоверения с помощью следующей команды CURL: 
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```
   
   Если у вас есть управляемые удостоверения, назначенные виртуальной машине, они перечислены в значении `identity` в ответе. 
    
   Например, если масштабируемому набору виртуальных машин присвоены назначенные пользователем удостоверения `ID1` и `ID2` и требуется оставить только `ID1`, сохранив при этом удостоверение, назначенное системой.

   **API версии 2018-06-01**

   К удостоверению, назначенному пользователем, которое требуется удалить, необходимо добавить `null`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API версии 2017-12-01 и более ранних версий**

   В массиве `identityIds` оставьте только те удостоверения, назначенные пользователем, которые вы хотите сохранить.   

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Если у масштабируемого набора виртуальных машин есть как назначенное системой, так и назначенные пользователем удостоверения, вы можете удалить все назначенные пользователем удостоверения, переключившись на использование только системного удостоверения с помощью следующей команды:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Если ваш масштабируемый набор виртуальных машин имеет только удостоверения, назначаемые пользователем, и вы хотите удалить их все, используйте следующую команду:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

## <a name="next-steps"></a>Дополнительная информация

Сведения о создании, перечислении или удалении удостоверения, назначаемого пользователем, с помощью REST см. в разделе:

- [Создание, получение списка и удаление пользовательских удостоверений с помощью вызовов REST API](how-to-manage-ua-identity-rest.md)