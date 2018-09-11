---
title: Получение доступа к Azure Resource Manager с помощью назначаемого пользователем управляемого удостоверения виртуальной машины Linux
description: Из этого руководства вы узнаете, как получить доступ к Azure Resource Manager с помощью назначаемого пользователем управляемого удостоверения виртуальной машины Linux.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1bb342d1d6fece89684ea16b8101719379ca64bf
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338402"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>Руководство. Получение доступа к Azure Resource Manager с помощью назначаемого пользователем управляемого удостоверения виртуальной машины Linux

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

В этом руководстве объясняется, как создать назначаемое пользователем управляемое удостоверение, назначить его виртуальной машине Linux и с его помощью получить доступ к API Azure Resource Manager. Управляемые удостоверения для ресурсов Azure автоматически управляются платформой Azure. Они обеспечивают проверку подлинности для служб, поддерживающих проверку подлинности Azure AD, без необходимости внедрения учетных данных в код. 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание назначаемого пользователем управляемого удостоверения
> * Назначение назначаемого пользователем управляемого удостоверения виртуальной машине Linux 
> * Предоставление назначаемому пользователем управляемому удостоверению доступа к группе ресурсов в Azure Resource Manager. 
> * Получение маркера доступа с помощью назначаемого пользователем управляемого удостоверения и использование этого маркера для вызова Azure Resource Manager 

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

[Войдите на портал Azure](https://portal.azure.com).

[Создайте виртуальную машину Linux](/azure/virtual-machines/linux/quick-create-portal).

Если вы решили установить и использовать CLI локально, для выполнения инструкций в этом руководстве вам понадобится Azure CLI 2.0.4 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-user-assigned-managed-identity"></a>Создание назначаемого пользователем управляемого удостоверения

1. Если вы используете консоль CLI (вместо сеанса Azure Cloud Shell), сначала войдите в Azure. Используйте учетную запись, связанную с подпиской Azure, в рамках которой нужно создать назначаемое пользователем управляемое удостоверение.

    ```azurecli
    az login
    ```

2. Создайте назначаемое пользователем управляемое удостоверение с помощью команды [az identity create](/cli/azure/identity#az-identity-create). Параметр `-g` указывает группу ресурсов, в которой создается назначаемое пользователем управляемое удостоверение, а параметр `-n` — его имя. Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<UAMI NAME>` собственными:
    
[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

Ответ содержит подробные сведения о созданном назначаемом пользователем управляемом удостоверении, как в примере ниже. Запишите значение `id` для назначаемого пользователем управляемого удостоверения, так как оно будет использоваться на следующем шаге.

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-a-user-assigned-managed-identity-to-your-linux-vm"></a>Назначение назначаемого пользователем управляемого удостоверения виртуальной машине Linux

Клиенты могут использовать назначаемое пользователем управляемое удостоверение для многих ресурсов Azure. Чтобы назначить назначаемое пользователем управляемое удостоверение одной виртуальной машине, используйте следующие команды. Используйте свойство `Id`, возвращенное на предыдущем шаге, для параметра `-IdentityID`.

Назначьте назначаемое пользователем управляемое удостоверение виртуальной машине Linux с помощью команды [az vm assign-identity](/cli/azure/vm#az-vm-assign-identity). Не забудьте заменить значения параметров `<RESOURCE GROUP>` и `<VM NAME>` собственными. Используйте свойство `id`, возвращенное на предыдущем шаге, в качестве значения параметра `--identities`.

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-your-user-assigned-managed-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Предоставление назначаемому пользователем управляемому удостоверению доступа к группе ресурсов в Azure Resource Manager 

Управляемые удостоверения для ресурсов Azure предоставляют удостоверения. С их помощью в коде можно запрашивать маркеры доступа для аутентификации в API ресурсов с поддержкой аутентификации Azure AD. В этом руководстве код получит доступ к API Azure Resource Manager.  

Чтобы ваш код мог получить доступ к API, необходимо сначала предоставить удостоверению доступ к ресурсу в Azure Resource Manager. В этом случае — к группе ресурсов, в которой содержится виртуальная машина. Обновите `<SUBSCRIPTION ID>` и `<RESOURCE GROUP>` значениями, соответствующими вашей среде. Также замените `<UAMI PRINCIPALID>` свойством `principalId`, возвращенным командой `az identity create` при [создании назначаемого пользователем управляемого удостоверения](#create-a-user-assigned-managed-identity):

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

Ответ содержит подробные сведения о созданном назначении роли, подобные следующему примеру.

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Получение маркера доступа с помощью удостоверения виртуальной машины и вызов Resource Manager с его помощью 

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали.

Для выполнения этих действий вам потребуется клиент SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). 

1. Войдите на [портал](https://portal.azure.com) Azure.
2. На портале перейдите к разделу **Виртуальные машины**, выберите виртуальную машину Linux и в разделе **Обзор** щелкните **Подключить**. Скопируйте строку подключения к виртуальной машине.
3. Подключитесь к виртуальной машине с помощью выбранного клиента SSH. Если вы используете Windows, можно использовать клиент SSH в [подсистеме Windows для Linux](https://msdn.microsoft.com/commandline/wsl/about). Если вам нужна помощь в настройке ключей SSH-клиента, ознакомьтесь с разделом [Использование ключей SSH с Windows в Azure](~/articles/virtual-machines/linux/ssh-from-windows.md) или [Как создать и использовать пару из открытого и закрытого ключей SSH для виртуальных машин Linux в Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
4. В окне терминала с помощью cURL выполните запрос к конечной точке службы метаданных экземпляров (IMDS) Azure, чтобы получить маркер доступа для Azure Resource Manager.  

   В следующем примере показан запрос CURL для получения маркера доступа. Не забудьте заменить `<CLIENT ID>` свойством `clientId`, возвращенным командой `az identity create` при [создании назначаемого пользователем управляемого удостоверения](#create-a-user-assigned-managed-identity): 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > Значение параметра `resource` должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI). 
    
    Ответ включает маркер доступа, необходимый для доступа к Azure Resource Manager. 
    
    Пример ответа:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. Используйте маркер для доступа к Azure Resource Manager и просмотрите свойства группы ресурсов, к которой вы предоставили доступ назначаемому пользователем управляемому удостоверению. Не забудьте заменить `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` значениями, указанными ранее, а `<ACCESS TOKEN>` — маркером, возвращенным на предыдущем шаге.

    > [!NOTE]
    > URL-адрес указывается с учетом регистра, поэтому убедитесь, что используется тот же регистр, который использовался при присвоении имени группе ресурсов. Также проверьте, чтобы в значении `resourceGroups` была указана прописная буква "G".  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Ответ содержит сведения об определенной группе ресурсов, подобные следующему примеру: 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как создать назначаемое пользователем управляемое удостоверение и подключить его к виртуальной машине Linux, чтобы получить доступ к API Azure Resource Manager.  Сведения об Azure Resource Manager см. здесь:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)

