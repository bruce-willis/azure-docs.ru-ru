---
title: Использование назначаемого системой управляемого удостоверения виртуальной машины Windows для доступа к Azure Cosmos DB
description: В этом руководстве объясняется, как получить доступ к Azure Cosmos DB с помощью назначаемого системой управляемого удостоверения на виртуальной машине Windows.
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
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: c2b4f048785c60a54f40c50bbec4a2f12373efe2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336968"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Руководство. Использование назначаемого системой управляемого удостоверения виртуальной машины Windows для доступа к Azure Cosmos DB

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

В этом руководстве описано, как получить доступ к Cosmos DB с помощью назначаемого системой управляемого удостоверения для виртуальной машины Windows. Вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание учетной записи Cosmos DB
> * предоставление доступа назначаемому системой управляемому удостоверению виртуальной машины Windows к ключам доступа для учетной записи Cosmos DB;
> * получение маркера доступа с помощью назначаемого системой управляемого удостоверения виртуальной машины Windows и вызов Azure Resource Manager;
> * Получение ключей доступа из Azure Resource Manager для создания вызовов Cosmos DB

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Войдите на портал Azure](https://portal.azure.com).

- [Создайте виртуальную машину Windows](/azure/virtual-machines/windows/quick-create-portal).

- [Включите назначаемое системой управляемое удостоверение на виртуальной машине](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm).

## <a name="create-a-cosmos-db-account"></a>Создание учетной записи Cosmos DB 

Если у вас еще нет учетной записи Cosmos DB, создайте ее. Этот шаг можно пропустить и использовать имеющуюся учетную запись Cosmos DB. 

1. Нажмите кнопку **+ Создание службы** в верхнем левом углу портала Azure.
2. Выберите **Databases** (Базы данных),а затем — **Azure Cosmos DB**, после чего отобразится панель New account (Новая учетная запись).
3. Введите **идентификатор** учетной записи Cosmos DB, которая будет использоваться далее.  
4. **API** должно иметь значение SQL. Подход, описанный в этом руководстве, можно использовать с другими доступными типами API, но указанные в этом руководстве действия подходят только для API SQL.
5. Убедитесь, что значения **подписки** и **группы ресурсов** соответствуют указанным при создании виртуальной машины на предыдущем шаге.  Выберите **расположение**, в котором доступен Cosmos DB.
6. Нажмите кнопку **Создать**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Создание коллекции в учетной записи Cosmos DB

Затем добавьте коллекцию данных в учетную запись Cosmos DB, с помощью которой можно создавать запросы на последующих шагах.

1. Перейдите к созданной учетной записи Cosmos DB.
2. На вкладке **Обзор** нажмите кнопку **Добавить коллекцию**. Откроется панель "Добавить коллекцию".
3. Присвойте коллекции идентификатор базы данных, идентификатор коллекции, выберите емкость хранилища, введите ключ секции и значение пропускной способности, а затем нажмите кнопку **ОК**.  Для этого руководства в качестве идентификатора базы данных и коллекции достаточно будет использовать значение Test. Выберите фиксированное значение емкости хранилища и самую низкую пропускную способность (400 ЕЗ/с).  

## <a name="grant-windows-vm-system-assigned-managed-identity-access-to-the-cosmos-db-account-access-keys"></a>Предоставление назначаемому системой управляемому удостоверению виртуальной машины Windows доступа к ключам доступа для учетной записи Cosmos DB

В Cosmos DB не встроена поддержка аутентификации Azure AD. Но можно использовать назначаемое системой управляемое удостоверение, чтобы извлечь ключ доступа к Cosmos DB из Resource Manager и с помощью этого ключа получить доступ к Cosmos DB. На этом шаге назначаемому системой управляемому удостоверению предоставляется доступ к ключам учетной записи Cosmos DB.

Чтобы с помощью PowerShell предоставить назначаемому системой управляемому удостоверению доступ к учетной записи Cosmos DB в Azure Resource Manager, укажите значения `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<COSMOS DB ACCOUNT NAME>` для своей среды. Замените `<PRINCIPALID>` свойством `principalId`, возвращенным командой `az resource show` при [извлечении principalID назначаемого системой управляемого удостоверения виртуальной машины Linux](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Cosmos DB поддерживает два уровня детализации при использовании ключей доступа: доступ на чтение и запись для учетной записи и доступ только для чтения для учетной записи.  Назначьте роль `DocumentDB Account Contributor`, если вы хотите получить ключи для записи и чтения для учетной записи, или назначьте роль `Cosmos DB Account Reader Role`, чтобы получить ключи только для чтения для учетной записи.

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vm-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Получение маркера доступа с помощью назначаемого системой управляемого удостоверения виртуальной машины Windows и вызов Azure Resource Manager

Далее в этом руководстве мы будем работать с виртуальной машиной, которую только что создали. 

На этом этапе вы примените командлеты PowerShell для Azure Resource Manager.  Если у вас он не установлен, [скачайте последнюю версию](https://docs.microsoft.com/powershell/azure/overview), прежде чем продолжить.

Вам также потребуется установить последнюю версию [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) на виртуальной машине Windows.

1. На портале Azure перейдите к разделу **Виртуальные машины**, выберите свою виртуальную машину Windows и вверху в разделе **Обзор** щелкните **Подключить**. 
2. Введите **имя пользователя** и **пароль**, добавленные при создании виртуальной машины Windows. 
3. Теперь, когда создано **подключение к удаленному рабочему столу** с виртуальной машиной, откройте PowerShell в удаленном сеансе.
4. С помощью командлета Powershell Invoke-WebRequest выполните запрос к локальным управляемым удостоверениям для конечной точки ресурсов Azure, чтобы получить маркер доступа для Azure Resource Manager.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```

    > [!NOTE]
    > Значение параметра resource должно точно совпадать со значением, которое будет использоваться в Azure AD. Если используется идентификатор ресурса Resource Manager, добавьте косую черту после универсального кода ресурса (URI).
    
    Затем извлеките элемент content, который хранится в виде форматированной строки JSON в объекте $response. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Затем извлеките маркер доступа из ответа.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Получение ключей доступа из Azure Resource Manager для создания вызовов Cosmos DB

Теперь мы с помощью PowerShell обратимся к Resource Manager, используя полученный на предыдущем этапе маркер доступа, и получим ключ доступа к учетной записи Cosmos DB. Получив ключ доступа, можно создать запрос к Cosmos DB. Не забудьте заменить значения параметров `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` и `<COSMOS DB ACCOUNT NAME>` своими значениями. Замените значение `<ACCESS TOKEN>` маркером доступа, который вы получили ранее.  Если вы хотите получить ключи для чтения и записи, используйте тип операции ключа `listKeys`.  Если вы хотите получить ключи только для чтения, используйте тип операции ключа `readonlykeys`.

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
В ответе будет содержаться список ключей.  Например, при получении ключей только для чтения вы увидите следующее:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Теперь, когда у вас есть ключ доступа для учетной записи Cosmos DB, вы можете передать его в пакет SDK Cosmos DB и выполнять вызовы для получения доступа к учетной записи.  Чтобы получить краткий пример, вы можете передать ключ доступа в Azure CLI.  Вы можете получить <COSMOS DB CONNECTION URL> на вкладке **Обзор** в колонке учетной записи Cosmos DB на портале Azure.  Замените <ACCESS KEY> полученным выше значением.

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Эта команда CLI возвращает сведения о коллекции.

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как получить доступ к Cosmos DB с помощью назначаемого системой управляемого удостоверения на виртуальной машине Windows.  Дополнительные сведения о Cosmos DB см. здесь:

> [!div class="nextstepaction"]
>[Добро пожаловать в базу данных Azure Cosmos DB](/azure/cosmos-db/introduction)


