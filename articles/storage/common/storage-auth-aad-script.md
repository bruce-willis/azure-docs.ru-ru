---
title: Выполнение команд Azure CLI или PowerShell с удостоверением Azure AD для получения доступа к службе хранилища Azure (предварительная версия) | Документация Майкрософт
description: Azure CLI и PowerShell поддерживают вход с помощью удостоверения Azure AD для выполнения команд с контейнерами, очередями и данными в службе хранилища Azure. Маркер доступа предоставляется для сеанса и позволяет авторизовать вызов операций. Разрешения зависят от роли, назначенной удостоверению Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/20/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 6354d89ff5a23ccb51b85737b3a842c08534683e
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223616"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Использование удостоверения Azure AD для доступа к службе хранилища Azure с помощью интерфейса командной строки или PowerShell (предварительная версия)

Служба хранилища Azure предоставляет предварительные версии расширений для Azure CLI и PowerShell, позволяющие войти в систему и выполнять команды сценариев с удостоверением Azure Active Directory (Azure AD). Можно использовать удостоверение Azure AD пользователя, группы или субъекта-службы приложения, а также [удостоверение управляемой службы](../../active-directory/managed-identities-azure-resources/overview.md). Назначить удостоверению Azure AD разрешения для доступа к ресурсам хранилища можно с помощью управления доступом на основе ролей (RBAC). Дополнительные сведения о ролях RBAC в службе хранилища Azure см. в разделе [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).

При входе в Azure CLI или PowerShell с использованием удостоверения Azure AD возвращается маркер доступа, необходимый для получения доступа к службе хранилища Azure с этим удостоверением. Этот маркер затем автоматически используется интерфейсом командной строки или PowerShell для авторизации операций в службе хранилища Azure. Для поддерживаемых операций больше не требуется передавать ключ учетной записи или маркер SAS с помощью команды.

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="supported-operations"></a>Поддерживаемые операции

Предварительные версии расширений поддерживаются для операций с контейнерами и очередями. Операции, которые можно вызывать, зависят от разрешений, предоставленных удостоверению Azure AD, с которым вы вошли в Azure CLI или PowerShell. Разрешения для контейнеров и очередей службы хранилища Azure назначаются с помощью управления доступом на основе ролей (RBAC). Например, если удостоверению назначена роль для чтения данных, то с его помощью можно выполнять команды сценария, которые считывают данных из очереди или контейнера. Если удостоверению назначена роль участника для данных, то с его помощью можно выполнять команды сценария, которые считывают, записывают или удаляют данные в контейнере или очереди. 

Дополнительные сведения о разрешениях, необходимых для каждой операции службы хранилища Azure с контейнером или очередью, представлены в разделе [Permissions for calling REST operations](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations) (Разрешения для вызова операций REST).  

## <a name="call-cli-commands-with-an-azure-ad-identity"></a>Вызов команд интерфейса командной строки с помощью удостоверения Azure AD

Чтобы установить предварительную версию расширения Azure CLI, сделайте следующее.

1. Убедитесь, что у вас установлен Azure CLI 2.0.32 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию.
2. Для установки предварительной версии расширения выполните следующую команду. 

    ```azurecli
    az extension add -n storage-preview
    ```

Это расширение добавляет параметр `--auth-mode` в поддерживаемые команды.

- Задайте для параметра `--auth-mode` значение `login`, чтобы выполнить вход с удостоверением Azure AD.
- Задайте для параметра `--auth-mode` устаревшее значение `key`, чтобы выполнить попытку запроса ключа учетной записи, если не указаны параметры аутентификации для учетной записи. 

Например, чтобы скачать большой двоичный объект в Azure CLI с помощью удостоверения Azure AD, сначала выполните `az login`, а затем вызовите команду, задав для параметра `--auth-mode` значение `login`.

```azurecli
az login
az storage blob download --account-name storagesamples --container sample-container --name myblob.txt --file myfile.txt --auth-mode login 
```

С параметром `--auth-mode` связана переменная среды `AZURE_STORAGE_AUTH_MODE`.

## <a name="call-powershell-commands-with-an-azure-ad-identity"></a>Вызов команд PowerShell с помощью удостоверения Azure AD

Azure PowerShell поддерживает вход с использованием удостоверения Azure AD только с одним из следующих модулей предварительной версии: 

- 4.4.0-preview; 
- 4.4.1-preview. 

Чтобы использовать Azure PowerShell для входа с удостоверением Azure AD, сделайте следующее.

1. Удалите все ранее установленные версии Azure PowerShell.

    - Удалите все предыдущие установки Azure PowerShell из Windows с помощью параметра **Apps & features** (Приложения и компоненты) в разделе **Параметры**.
    - Удалите все модули **Azure*** из `%Program Files%\WindowsPowerShell\Modules`.

1. Убедитесь, что у вас установлена последняя версия PowerShellGet. Откройте окно Windows PowerShell и выполните следующую команду, чтобы установить последнюю версию:
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```
1. После установки PowerShellGet закройте и снова откройте окно PowerShell. 

1. Установите Azure PowerShell последней версии:

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

1. Установите один из модулей службы хранилища Azure, используемых в режиме предварительной версии, которые поддерживают Azure AD:

    ```powershell
    Install-Module Azure.Storage –Repository PSGallery -RequiredVersion 4.4.1-preview  –AllowPrerelease –AllowClobber –Force 
    ```
1. Закройте и снова откройте окно PowerShell.
1. Вызовите командлет [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) для создания контекста, добавив параметр `-UseConnectedAccount`. 
1. Чтобы вызвать командлет с помощью удостоверения Azure AD, передайте созданный контекст в этот командлет.

В приведенном ниже примере показано, как перечислить большие двоичные объекты в контейнер из Azure PowerShell с помощью удостоверения Azure AD. Обязательно замените заполнители имен учетной записи и контейнера собственными значениями: 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName storagesamples -UseConnectedAccount 
Get-AzureStorageBlob -Container sample-container -Context $ctx 
```

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о ролях RBAC для службы хранилища Azure, ознакомившись с разделом [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure со службой хранилища Azure см. в статье [Аутентификация в Azure AD с помощью функции "Управляемое удостоверение службы Azure" (предварительная версия)](storage-auth-aad-msi.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
- Дополнительные сведения об интеграции Azure AD для очередей и больших двоичных объектов Azure доступны в записи блога команды разработчиков службы хранилища Azure [Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/) (Объявление о выпуске предварительной версии аутентификации Azure AD для службы хранилища Azure).
