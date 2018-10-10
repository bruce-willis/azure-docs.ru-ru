---
title: Краткое руководство. Создание учетной записи хранения (служба хранилища Azure)
description: В этом руководстве вы научитесь создавать учетную запись хранения с помощью портала Azure, Azure PowerShell или Azure CLI. Учетная запись хранения Azure предоставляет уникальное пространство имен в Microsoft Azure для хранения объектов данных в службе хранилища Azure и предоставления доступа к ним.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 09/18/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: a695e333f48ed0bbf1ad5656c20964232feff4d7
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990133"
---
# <a name="create-a-storage-account"></a>Создание учетной записи хранения

В этом руководстве вы научитесь создавать учетную запись хранения с помощью [портала Azure](https://portal.azure.com/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) или [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).  

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

# <a name="portaltabportal"></a>[Портал](#tab/portal)

Отсутствует.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Для работы с этим кратким руководством требуется модуль Azure PowerShell 3.6 или более поздней версии. Чтобы узнать, какая версия используется сейчас, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить установку или обновление, см. статью [об установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps).

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

Вы можете войти в Azure и выполнить команды Azure CLI одним из двух способов:

- Выполнить команды CLI на портале Azure в Azure Cloud Shell. 
- Установить CLI и выполнить команды CLI локально.  

### <a name="use-azure-cloud-shell"></a>Использование Azure Cloud Shell

Azure Cloud Shell — это бесплатная оболочка Bash, которую можно запускать непосредственно на портале Azure. Она включает предварительно установленный интерфейс Azure CLI и настроена для использования с вашей учетной записью. Нажмите кнопку меню **Cloud Shell** в правом верхнем углу окна портала Azure.

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Эта кнопка запускает интерактивную оболочку, с помощью которой можно выполнять действия, описанные в этом кратком руководстве:

[![Снимок экрана с окном Azure Cloud Shell на портале](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Установка CLI локально

Azure CLI также можно установить и применять локально. Для этого руководства требуется Azure CLI версии 2.0.4 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

---

## <a name="log-in-to-azure"></a>Вход в Azure

# <a name="portaltabportal"></a>[Портал](#tab/portal)

Войдите на [портал Azure](https://portal.azure.com).

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Чтобы выполнить проверку подлинности, войдите в подписку Azure с помощью команды `Connect-AzureRmAccount` и следуйте инструкциям на экране.

```powershell
Connect-AzureRmAccount
```

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

Чтобы запустить Azure Cloud Shell, войдите на [портал Azure](https://portal.azure.com).

Чтобы войти в локальную установку CLI, выполните команду входа:

```cli
az login
```

---

## <a name="create-a-storage-account"></a>Создание учетной записи хранения

Теперь вы готовы создать свою учетную запись.

Каждая учетная запись хранения должна принадлежать группе ресурсов Azure. Группа ресурсов — это логический контейнер для группирования служб Azure. При создании учетной записи хранения у вас есть возможность создать новую или использовать существующую группу ресурсов. В этом кратком руководстве показано, как создать группу ресурсов. 

Учетная запись хранения **общего назначения версии 2** предоставляет доступ ко всем службам хранилища Azure (большим двоичным объектам, файлам, очередям, таблицам и дискам). Это краткое руководство создает учетную запись хранения общего назначения версии 2, но создание любого типа учетной записи хранения происходит также.   

# <a name="portaltabportal"></a>[Портал](#tab/portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Сначала создайте новую группу ресурсов с помощью PowerShell, используя команду [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

Если вы не уверены, какой регион необходимо задать для параметра `-Location`, вы можете получить список поддерживаемых регионов для своей подписки, выполнив команду [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location 
$location = "westus"
```

Затем создайте учетную запись хранения общего назначения версии 2 с локально избыточным хранилищем (LRS). Используйте команду [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). 

```powershell
New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 
```

Чтобы создать учетную запись общего назначения версии 2 с хранилищем, избыточным в пределах зоны (ZRS, предварительная версия), геоизбыточным хранилищем (GRS) или геоизбыточным хранилищем с доступом на чтение (RA-GRS), замените нужное значение параметром **SkuName**, как показано ниже. 

|Параметр репликации  |Параметр SkuName  |
|---------|---------|
|Локально избыточное хранилище (LRS)     |Standard_LRS         |
|Хранилище, избыточное в пределах зоны (ZRS)     |Standard_ZRS         |
|Геоизбыточное хранилище (GRS)     |Standard_GRS         |
|Геоизбыточное хранилище с доступом для чтения (RA-GRS)     |Standard_RAGRS         |

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

Сначала создайте новую группу ресурсов с помощью Azure CLI, используя команду [az group create](/cli/azure/group#az_group_create). 

```azurecli-interactive
az group create \
    --name storage-quickstart-resource-group \
    --location westus
```

Если вы не уверены, какой регион необходимо задать для параметра `--location`, вы можете получить список поддерживаемых регионов для своей подписки, выполнив команду [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Затем создайте учетную запись хранения общего назначения версии 2 с локально избыточным хранилищем. Используйте команду [az storage account create](/cli/azure/storage/account#az_storage_account_create).

```azurecli-interactive
az storage account create \
    --name storagequickstart \
    --resource-group storage-quickstart-resource-group \
    --location westus \
    --sku Standard_LRS \
    --kind StorageV2
```

Чтобы создать учетную запись общего назначения версии 2 с хранилищем, избыточным в пределах зоны (ZRS, предварительная версия), геоизбыточным хранилищем (GRS) или геоизбыточным хранилищем с доступом на чтение (RA-GRS), замените нужное значение на параметр **Sku**, как показано ниже. 

|Параметр репликации  |параметр sku  |
|---------|---------|
|Локально избыточное хранилище (LRS)     |Standard_LRS         |
|Хранилище, избыточное в пределах зоны (ZRS)     |Standard_ZRS         |
|Геоизбыточное хранилище (GRS)     |Standard_GRS         |
|Геоизбыточное хранилище с доступом для чтения (RA-GRS)     |Standard_RAGRS         |

---

Дополнительные сведения о доступных вариантах репликации см. в статье [Репликация службы хранилища Azure](storage-redundancy.md).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если нужно очистить ресурсы, созданные при работе с этим кратким руководством, можно просто удалить группу ресурсов. При этом удаляется связанная учетная запись хранения и другие ресурсы, связанные с этой группой ресурсов.

# <a name="portaltabportal"></a>[Портал](#tab/portal)

Чтобы удалить группу ресурсов с помощью портала Azure, сделайте следующее:

1. На портале Azure разверните меню слева, чтобы открыть меню служб, и выберите **Resource Groups** (Группы ресурсов), чтобы просмотреть список групп ресурсов.
2. Найдите группу ресурсов, которую нужно удалить, и щелкните правой кнопкой мыши кнопку **More** (Дополнительно) (**...**) справа от списка.
3. Выберите **Удалить группу ресурсов** и подтвердите выбор.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup): 

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

Чтобы удалить группу ресурсов и связанные с ней ресурсы, включая новую учетную запись хранения, используйте команду [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

---

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы создали стандартную учетную запись хранения общего назначения. Сведения об отправке и скачивании больших двоичных объектов в учетную запись хранения и обратно см. в кратком руководстве по хранилищу BLOB-объектов.

# <a name="portaltabportal"></a>[Портал](#tab/portal)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью портала Azure](../blobs/storage-quickstart-blobs-portal.md)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure PowerShell](../blobs/storage-quickstart-blobs-powershell.md)

# <a name="azure-clitabazure-cli"></a>[интерфейс командной строки Azure](#tab/azure-cli)

> [!div class="nextstepaction"]
> [Краткое руководство по передаче, скачиванию и составлению списка больших двоичных объектов с помощью Azure CLI](../blobs/storage-quickstart-blobs-cli.md)

---
