---
title: Настройка удостоверений, назначаемых системой и пользователем, в виртуальной машине Azure с помощью REST
description: Пошаговые инструкции по настройке удостоверений, назначаемых системой и пользователем, в виртуальной машине Azure с использованием CURL для выполнения вызовов REST API.
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
ms.openlocfilehash: 9cc645d91bebf07ed7e657ed39c2454254958959
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902893"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>Настройка управляемого удостоверения в виртуальной машине Azure с помощью вызовов REST API

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Управляемое удостоверение предоставляет службам Azure автоматически управляемое системное удостоверение в Azure Active Directory. Это удостоверение можно использовать для аутентификации в любой службе, которая поддерживает аутентификацию Azure AD, не храня какие-либо учетные данные в коде. 

В этой статье вы узнаете, как выполнять следующие операции с управляемым удостоверением для виртуальной машины Azure, используя CURL для направления вызовов к конечной точке REST Azure Resource Manager:

- включать и отключать назначенное системой удостоверение в виртуальной машине Azure;
- добавлять и удалять назначенное пользователем удостоверение в виртуальной машине Azure.

## <a name="prerequisites"></a>предварительным требованиям

- Если вы не работали с компонентом "Управляемое удостоверение службы", изучите [общие сведения](overview.md). **Обратите внимание на [различие между назначенным системой и пользовательским удостоверениями](overview.md#how-does-it-work)**.
- Если у вас нет учетной записи Azure, [зарегистрируйтесь для получения бесплатной пробной учетной записи](https://azure.microsoft.com/free/), прежде чем продолжать.
- Если вы используете Windows, установите [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или используйте [Azure Cloud Shell](../../cloud-shell/overview.md) на портале Azure.
- Если вы используете [подсистему Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about) или [ОС на базе дистрибутива Linux](/cli/azure/install-azure-cli-apt?view=azure-cli-latest), [установите локальную консоль Azure CLI](/azure/install-azure-cli).
- Если вы используете локальную консоль Azure CLI, войдите в Azure с помощью `az login` с учетной записью, привязанной к подписке Azure, где вы хотите управлять удостоверениями, назначаемые пользователями или системой.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Системное удостоверение

Этот раздел описывает, как включить и отключить системное удостоверение в виртуальной машине Azure, используя CURL для направления вызовов к конечной точке REST Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Включение назначенного системой удостоверения во время создания виртуальной машины Azure

Для создания виртуальной машины Azure с включенным удостоверением, назначаемым системой, создайте виртуальную машину и получите маркер доступа, чтобы использовать CURL для вызова конечной точки диспетчера ресурсов со значением типа системного удостоверения.

1. Создайте [группу ресурсов](../../azure-resource-manager/resource-group-overview.md#terminology) с помощью параметра [az group create](/cli/azure/group/#az_group_create), чтобы сохранить и развернуть виртуальную машину и связанные с ней ресурсы. Если вы уже создали группу ресурсов, которую можно использовать, этот шаг можно пропустить:

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

4. Создайте виртуальную машину, используя CURL для вызова конечной точки REST Azure Resource Manager. Приведенный ниже пример создает виртуальную машину *myVM* с назначаемым системой удостоверением в соответствии со значением `"identity":{"type":"SystemAssigned"}` в тексте запроса. Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Включение назначенного системой удостоверения в имеющейся виртуальной машине Azure

Чтобы включить назначаемое системой удостоверение на существующей виртуальной машине, нужно получить маркер доступа, а затем воспользоваться CURL для вызова конечной точки REST диспетчера ресурсов для изменения типа удостоверения.

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Используйте приведенную ниже команду CURL для вызова конечной точки REST Azure Resource Manager, чтобы включить назначаемое системой удостоверение на виртуальной машине в соответствии со значением `{"identity":{"type":"SystemAssigned"}` в тексте запроса для виртуальной машины *myVM*.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
   
   > [!IMPORTANT]
   > Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, для виртуальной машины, нужно перечислить данные пользовательские удостоверения с помощью этой команды CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Если у вас есть назначаемые пользователем удостоверения для виртуальной машины, как указано в значении `identity` в ответе, перейдите к шагу 3, где описано, как сохранить пользовательские удостоверения при включении системного удостоверения на виртуальной машине.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Чтобы включить назначаемое системой удостоверение на виртуальной машине с существующими пользовательскими удостоверениями, нужно добавить `SystemAssigned` в значение `type`.  
   
   Например, если для виртуальной машины назначены пользовательские удостоверения `ID1` и `ID2` и вы хотите добавить для нее системное удостоверение, используйте приведенный ниже вызов CURL. Замените `<ACCESS TOKEN>` и `<SUBSCRIPTION ID>` значениями, уместными для вашей среды.
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Отключение системного удостоверения в виртуальной машине Azure

Чтобы отключить назначаемое системой удостоверение на существующей виртуальной машине, нужно получить маркер доступа, а затем воспользоваться CURL для вызова конечной точки REST диспетчера ресурсов для изменения типа удостоверения на `None`.

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Измените виртуальную машину, используя CURL для вызова конечной точки REST Azure Resource Manager, чтобы отключить системное удостоверение.  Приведенный ниже пример отключает назначаемое системой удостоверение в виртуальной машине *myVM* в соответствии со значением `{"identity":{"type":"None"}}` в тексте запроса.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.

   > [!IMPORTANT]
   > Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, для виртуальной машины, нужно перечислить данные пользовательские удостоверения с помощью этой команды CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Если у вас есть назначаемые пользователем удостоверения для виртуальной машины, как указано в значении `identity` в ответе, перейдите к шагу 3, где описано, как сохранить пользовательские удостоверения при отключении системного удостоверения на виртуальной машине.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Чтобы удалить системное удостоверение из виртуальной машины с пользовательскими удостоверениями, удалите `SystemAssigned` из значения `{"identity":{"type:" "}}`, сохранив значение `UserAssigned` и массив `identityIds`, определяющий, какие пользовательские удостоверения назначены этой виртуальной машине.

## <a name="user-assigned-identity"></a>Удостоверение, назначенное пользователем

Этот раздел описывает, как добавить и удалить пользовательское удостоверение в виртуальной машине Azure, используя CURL для направления вызовов к конечной точке REST Azure Resource Manager.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Добавление назначенного пользователем удостоверения во время создания виртуальной машины Azure

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

4. Создайте пользовательское удостоверение, как описано в разделе [Создание управляемого пользовательского удостоверения](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Создайте виртуальную машину, используя CURL для вызова конечной точки REST Azure Resource Manager. Приведенный ниже пример создает в группе ресурсов *myResourceGroup* виртуальную машину *myVM* с назначаемым пользователем удостоверением `ID1` в соответствии со значением `"identity":{"type":"UserAssigned"}` в тексте запроса. Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Присвоение назначенного пользователем удостоверения имеющейся виртуальной машине Azure

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Создайте пользовательское удостоверение, как описано в разделе [Создание управляемого пользовательского удостоверения](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем или системой, для виртуальной машины, нужно перечислить назначенные ей типы удостоверений с помощью приведенной ниже команды CURL. Если у вас есть управляемые удостоверения, назначенные масштабируемому набору виртуальных машин, они перечислены в значении `identity`.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Если у вас есть назначаемые пользователем или системой удостоверения для виртуальной машины, как указано в значении `identity` в ответе, перейдите к шагу 5, где описано, как сохранить пользовательские удостоверения при включении системного удостоверения на виртуальной машине.

4. Если вашей виртуальной машине не назначены пользовательские удостоверения, используйте приведенную ниже команду CURL для вызова конечной точки REST Azure Resource Manager, чтобы назначить первое пользовательское удостоверение виртуальной машине.  Если виртуальной машине назначены какие-либо пользовательские удостоверения, перейдите к следующему шагу, где показано, как добавить несколько пользовательских удостоверений в виртуальную машину.

   Следующий пример назначает пользовательское удостоверение `ID1` виртуальной машине *myVM* в группе ресурсов *myResourceGroup*.  Замените `<ACCESS TOKEN>` значением, полученным на предыдущем шаге при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Если виртуальной машине назначены пользовательские или системные удостоверения, нужно добавить новое пользовательское удостоверение в массив `identityIDs`, при этом сохранив пользовательские и системные удостоверения, назначенные виртуальной машине в настоящее время.

   Например, если для виртуальной машины назначено системное удостоверение и пользовательское удостоверение `ID1`, и вы хотите добавить для нее пользовательское удостоверение `ID2`, используйте приведенную ниже команду CURL. Замените `<ACCESS TOKEN>` значением, полученным при запросе маркера доступа носителя, а вместо `<SUBSCRIPTION ID>` укажите значение, подходящее для вашей среды.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Удаление назначенного пользователем удостоверения из виртуальной машины Azure

1. Получите маркер доступа носителя, который понадобится в следующем шаге в заголовке авторизации для создания виртуальной машины с удостоверением, назначаемым системой.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Чтобы предотвратить удаление существующих управляемых удостоверений, назначаемых пользователем, которые нужно сохранить для виртуальной машины, или удалить системное удостоверение, нужно перечислить управляемые удостоверения с помощью следующей команды CURL: 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Если у вас есть управляемые удостоверения, назначенные виртуальной машине, они перечислены в значении `identity` в ответе.

   Например, если имеются пользовательские удостоверения `ID1` и `ID2`, назначенные виртуальной машине, и вы хотите оставить `ID1` назначенным и сохранить системное удостоверение, нужно использовать ту же команду CURL, что и при назначении пользовательского управляемого удостоверения для виртуальной машины, сохранив значение `ID1`, и оставить значение `SystemAssigned`. При этом пользовательское удостоверение `ID2` удаляется из виртуальной машины, а системное удостоверение сохраняется.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Если у виртуальной машины есть как назначенное системой, так и назначенные пользователем удостоверения, вы можете удалить все назначенные пользователем удостоверения, переключившись на использование только системного удостоверения с помощью следующей команды:

```bash
curl 'https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Если ваша виртуальная машина имеет только удостоверения, назначаемые пользователем, и вы хотите удалить их все, используйте следующую команду:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>Дополнительная информация

Сведения о создании, перечислении или удалении удостоверения, назначаемого пользователем, с помощью REST см. в разделе:

- [Создание, получение списка и удаление пользовательских удостоверений с помощью вызовов REST API](how-to-manage-ua-identity-rest.md)