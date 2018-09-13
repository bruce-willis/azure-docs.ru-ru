---
title: Как настроить управляемые удостоверения, назначаемые системой и назначаемые пользователем, на виртуальной машине Azure с помощью REST
description: Пошаговые инструкции по настройке управляемых удостоверений, назначаемых системой и назначаемых пользователем, на виртуальной машине Azure с использованием CURL для выполнения вызовов REST API.
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
ms.openlocfilehash: b4ed347e9b55b3884ce0cfd1083ea002978d7ddd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339621"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью вызовов REST API

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Функция управляемых удостоверений для ресурсов Azure предоставляет службам Azure автоматически управляемые системные удостоверения в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять приведенные ниже операции с управляемыми удостоверениями для ресурсов Azure на виртуальной машине Azure, используя CURL для выполнения вызовов к конечной точке REST Azure Resource Manager.

- Включение и отключение управляемого удостоверения, назначаемого системой, на виртуальной машине Azure
- Добавление и удаление управляемого удостоверения, назначаемого пользователем, в виртуальной машине Azure

## <a name="prerequisites"></a>Предварительные требования

- Если вы не работали с управляемыми удостоверениями для ресурсов Azure, изучите [общие сведения](overview.md). **Обратите внимание на [различие между управляемыми удостоверениями, назначаемыми системой и назначаемыми пользователями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Для выполнения операций управления, описанных в этой статье, учетной записи нужно назначить следующие роли:
    - [Участник виртуальных машин](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) для создания виртуальной машины, а также включения и удаления управляемого удостоверения, назначаемого системой и (или) управляемого удостоверения, назначаемого пользователем, для виртуальной машины Azure.
    - Роль [Участник управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-contributor): для создания удостоверения, назначаемого пользователем.
    - Роль [Оператор управляемого удостоверения](/azure/role-based-access-control/built-in-roles#managed-identity-operator) для назначения и удаления управляемого удостоверения, назначаемого пользователем, для виртуальной машины.
- Если вы используете Windows, установите [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или используйте [Azure Cloud Shell](../../cloud-shell/overview.md) на портале Azure.
- Если вы используете [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или [ОС на базе дистрибутива Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [установите локальную консоль Azure CLI](/cli/azure/install-azure-cli).
- Если вы используете локальную консоль Azure CLI, войдите в Azure с помощью `az login` с учетной записью, привязанной к подписке Azure, в которой вы хотите управлять удостоверениями, назначаемые пользователями или назначаемыми системой.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое системой

Этот раздел описывает, как включить и отключить управляемое удостоверение, назначаемое системой, на виртуальной машине Azure, используя CURL для выполнения вызовов к конечной точке REST Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Включение управляемого удостоверения, назначаемого системой, во время создания виртуальной машины Azure

Для создания виртуальной машины Azure с включенным управляемым удостоверением, назначаемым системой, создайте виртуальную машину и получите маркер доступа, чтобы использовать CURL для вызова конечной точки Resource Manager со значением типа управляемого удостоверения, назначаемого системой.

1. Создайте [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#terminology) с помощью параметра [az group create](/cli/azure/group/#az-group-create), чтобы сохранить и развернуть виртуальную машину и связанные с ней ресурсы. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Создайте [сетевой интерфейс](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) для виртуальной машины:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Создайте виртуальную машину, используя CURL для вызова конечной точки REST Azure Resource Manager. Приведенный ниже пример создает виртуальную машину *myVM* с управляемым удостоверением, назначаемым системой, в соответствии со значением `"identity":{"type":"SystemAssigned"}` в тексте запроса. Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Включение удостоверения, назначаемого системой, на имеющейся виртуальной машине Azure

Чтобы включить удостоверение, назначаемое системой, на существующей виртуальной машине, нужно получить маркер доступа, а затем воспользоваться CURL для вызова конечной точки REST Resource Manager для изменения типа удостоверения.

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Используйте приведенную ниже команду CURL для вызова конечной точки REST Azure Resource Manager, чтобы включить управляемое удостоверение, назначаемое системой, на виртуальной машине в соответствии со значением `{"identity":{"type":"SystemAssigned"}` в тексте запроса для виртуальной машины *myVM*.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
   
   > [!IMPORTANT]
   > Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, для виртуальной машины, нужно получить список этих удостоверений с помощью следующей команды CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Если для виртуальной машины заданы управляемые удостоверения, назначаемые пользователем, как указано в значении `identity` в ответе, перейдите к шагу 3, где описано, как сохранить управляемые удостоверения, назначаемые пользователем, при включении на виртуальной машине управляемого удостоверения, назначаемого системой.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Чтобы включить управляемое удостоверение, назначаемое системой, на виртуальной машине с существующими управляемыми удостоверениями, назначаемыми пользователем, нужно добавить `SystemAssigned` в значение `type`.  
   
   Например, если для виртуальной машины назначены управляемые удостоверения, назначаемые пользователем, `ID1` и `ID2`, и вы хотите добавить для нее управляемое удостоверение, назначаемое системой, используйте приведенный ниже вызов CURL. Замените `<ACCESS TOKEN>` и `<SUBSCRIPTION ID>` значениями, уместными для вашей среды.

   API версии `2018-06-01` хранит управляемые удостоверения, назначаемые пользователем, в значении `userAssignedIdentities` в формате словаря в отличие от значения `identityIds` в формате массива, используемого в API версии `2017-12-01` и более ранних версиях.
   
   **API версии 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API версии 2017-12-01 и более ранних версий**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Отключение управляемого удостоверения, назначаемого системой, на виртуальной машине Azure

Чтобы отключить управляемое удостоверение, назначаемое системой, на существующей виртуальной машине, нужно получить маркер доступа, а затем воспользоваться CURL для вызова конечной точки REST Resource Manager для изменения типа удостоверения на `None`.

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Измените виртуальную машину, используя CURL для вызова конечной точки REST Azure Resource Manager, чтобы отключить управляемое удостоверение, назначаемое системой.  Приведенный ниже пример отключает управляемое удостоверение, назначаемое системой, на виртуальной машине *myVM* в соответствии со значением `{"identity":{"type":"None"}}` в тексте запроса.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.

   > [!IMPORTANT]
   > Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, для виртуальной машины, нужно получить список этих удостоверений с помощью следующей команды CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Если для виртуальной машины заданы управляемые удостоверения, назначаемые пользователем, как указано в значении `identity` в ответе, перейдите к шагу 3, где описано, как сохранить управляемые удостоверения, назначаемые пользователем, при отключении на виртуальной машине управляемого удостоверения, назначаемого системой.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Чтобы удалить управляемое удостоверение, назначаемое системой, из виртуальной машины, в которой есть управляемые удостоверения, назначаемые пользователем, удалите `SystemAssigned` из значения `{"identity":{"type:" "}}`, сохранив значение `UserAssigned` и значения словаря `userAssignedIdentities`, если используется **API версии 2018-06-01**. При использовании **API версии 2017-12-01** или более ранних версий сохраните массив `identityIds`.

## <a name="user-assigned-managed-identity"></a>Управляемое удостоверение, назначаемое пользователем

В этом разделе описывается, как добавить и удалить управляемое удостоверение, назначаемое пользователем, в виртуальной машине Azure, используя CURL для выполнения вызовов к конечной точке REST Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Задание управляемого удостоверения, назначаемого пользователем, во время создания виртуальной машины Azure

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Создайте [сетевой интерфейс](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) для виртуальной машины:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Создайте управляемое удостоверение, назначаемое пользователем, как описано в разделе [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью вызовов REST API](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Создайте виртуальную машину, используя CURL для вызова конечной точки REST Azure Resource Manager. Приведенный ниже пример создает в группе ресурсов *myResourceGroup* виртуальную машину *myVM* с управляемым удостоверением, назначаемым пользователем, `ID1` в соответствии со значением `"identity":{"type":"UserAssigned"}` в тексте запроса. Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
 
   
   **API версии 2018-06-01**
    
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 

   **API версии 2017-12-01 и более ранних версий**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Задание управляемого удостоверения, назначаемого пользователем, для имеющейся виртуальной машины Azure

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Создайте управляемое удостоверение, назначаемое пользователем, как описано в разделе [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью вызовов REST API](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем или назначаемых системой, для виртуальной машины, нужно получить список назначенных ей типов удостоверений с помощью приведенной ниже команды CURL. Если у вас есть управляемые удостоверения, назначенные масштабируемому набору виртуальных машин, они перечислены в значении `identity`.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Если для виртуальной машины заданы управляемые удостоверения, назначаемые пользователем или назначаемые системой, как указано в значении `identity` в ответе, перейдите к шагу 5, где описано, как сохранить управляемое удостоверение, назначаемое системой, при добавлении в виртуальную машину управляемого удостоверения, назначаемого пользователем.

4. Если для виртуальной машины не заданы какие-либо управляемые удостоверения, назначаемые пользователем, используйте приведенную ниже команду CURL для вызова конечной точки REST Azure Resource Manager, чтобы назначить виртуальной машине первое управляемое удостоверение, назначаемое пользователем.

   Следующий пример задает управляемое удостоверение, назначаемое пользователем `ID1` для виртуальной машины *myVM* в группе ресурсов *myResourceGroup*.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.

   **API версии 2018-06-01**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API версии 2017-12-01 и более ранних версий**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Если для виртуальной машины заданы управляемые удостоверения, назначаемые пользователем или назначаемые системой, сделайте следующее.
   
   **API версии 2018-06-01**

   Добавьте управляемое удостоверение, назначаемое пользователем, в значение словаря `userAssignedIdentities`.
    
   Например, если для виртуальной машины заданы управляемое удостоверение, назначаемое системой, и управляемое удостоверение, назначаемое пользователем, `ID1` и вы хотите добавить для него управляемое удостоверение, назначаемое пользователем, `ID2`, сделайте следующее.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API версии 2017-12-01 и более ранних версий**

   При добавлении нового управляемого удостоверения, назначаемого пользователем, сохраните управляемые удостоверения, назначаемые пользователем, которые вы хотите оставить в значении массива `identityIds`.

   Например, если для виртуальной машины заданы управляемое удостоверение, назначаемое системой, и управляемое удостоверение, назначаемое пользователем, `ID1` и вы хотите добавить для него управляемое удостоверение, назначаемое пользователем, `ID2`, сделайте следующее. 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Удаление управляемого удостоверения, назначаемого пользователем, из виртуальной машины Azure

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, которые нужно сохранить для виртуальной машины, или удалить управляемое удостоверение, назначаемое системой, нужно получить список управляемых удостоверений с помощью следующей команды CURL. 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Если у вас есть управляемые удостоверения, назначенные виртуальной машине, они перечислены в значении `identity` в ответе.

   Например, если для виртуальной машины заданы управляемые удостоверения, назначаемые пользователем, `ID1` и `ID2`, и вы хотите оставить назначенным только `ID1`, при этом сохранив удостоверение, назначаемое системой, сделайте следующее.
   
   **API версии 2018-06-01**

   К управляемому удостоверению, назначаемому пользователем, которое требуется удалить, необходимо добавить `null`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   **API версии 2017-12-01 и более ранних версий**

   В массиве `identityIds` оставьте только те управляемые удостоверения, назначаемые пользователем, которые вы хотите сохранить.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Если у виртуальной машины есть управляемые удостоверения, назначаемые системой и назначаемые пользователем, вы можете удалить все управляемые удостоверения, назначаемые пользователем, переключившись на использование только управляемого удостоверения, назначаемого системой, с помощью следующей команды.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Если ваша виртуальная машина имеет только управляемые удостоверения, назначаемые пользователем, и вы хотите удалить их все, используйте следующую команду.

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>Дополнительная информация

Сведения о создании и удалении управляемых удостоверений, назначаемых пользователем, а также получении их списка с помощью REST:

- [Создание и удаление управляемых удостоверений, назначаемых пользователем, а также получение их списка с помощью вызовов REST API](how-to-manage-ua-identity-rest.md)
