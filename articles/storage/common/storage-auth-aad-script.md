---
title: Выполнение команд Azure CLI или PowerShell с удостоверением Azure AD для получения доступа к службе хранилища Azure (предварительная версия) | Документация Майкрософт
description: Azure CLI и PowerShell поддерживают вход с помощью удостоверения Azure AD для выполнения команд с контейнерами, очередями и данными в службе хранилища Azure. Маркер доступа предоставляется для сеанса и позволяет авторизовать вызов операций. Разрешения зависят от роли, назначенной удостоверению Azure AD.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/30/2018
ms.author: tamram
ms.openlocfilehash: 98af46707485d1ab49e7d8c6fb1729e6edc6b2ff
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235871"
---
# <a name="use-an-azure-ad-identity-to-access-azure-storage-with-cli-or-powershell-preview"></a>Использование удостоверения Azure AD для доступа к службе хранилища Azure с помощью интерфейса командной строки или PowerShell (предварительная версия)

Служба хранилища Azure предоставляет предварительные версии расширений для Azure CLI и PowerShell, позволяющие войти в систему и выполнять команды сценариев с удостоверением Azure Active Directory (Azure AD). Можно использовать удостоверение Azure AD пользователя, группы или субъекта-службы приложения, а также [удостоверение управляемой службы](../../active-directory/managed-service-identity/overview.md). Назначить удостоверению Azure AD разрешения для доступа к ресурсам хранилища можно с помощью управления доступом на основе ролей (RBAC). Дополнительные сведения о ролях RBAC в службе хранилища Azure см. в разделе [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).

При входе в Azure CLI или PowerShell с использованием удостоверения Azure AD возвращается маркер доступа, необходимый для получения доступа к службе хранилища Azure с этим удостоверением. Этот маркер затем автоматически используется интерфейсом командной строки или PowerShell для авторизации операций в службе хранилища Azure. Для поддерживаемых операций больше не требуется передавать ключ учетной записи или маркер SAS с помощью команды.

> [!IMPORTANT]
> Эта предварительная версия не предназначена для использования в рабочей среде. Соглашения об уровне обслуживания (SLA) для рабочих сред не будут доступны до выпуска общедоступной версии интеграции Azure AD для службы хранилища Azure. Если интеграция Azure AD пока не поддерживается для вашего сценария, продолжайте использовать в приложениях авторизацию по общему ключу или маркеры SAS. Дополнительные сведения о предварительной версии см. в статье [Аутентификация доступа к службе хранилища Azure с помощью Azure Active Directory (предварительная версия)](storage-auth-aad.md).
>
> На этапе предварительной версии распространение назначенных ролей RBAC может занимать до пяти минут.
>
> Интеграция Azure AD со службой хранилища Azure требует использования протокола HTTPS для операций службы хранилища Azure.

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

Чтобы использовать Azure PowerShell для входа с удостоверением Azure AD, сделайте следующее.

1. Убедитесь, что у вас установлена последняя версия PowerShellGet. Выполните следующую команду, чтобы установить последнюю версию.
 
    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

2. Удалите все предыдущие установленные версии Azure PowerShell.
3. Установите AzureRM.

    ```powershell
    Install-Module AzureRM –Repository PSGallery –AllowClobber
    ```

4. Установите предварительную версию модуля.

    ```powershell
    Install-Module -Name Azure.Storage -AllowPrerelease –AllowClobber 
    ```

5. Вызовите командлет [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontext) для создания контекста, добавив параметр `-UseConnectedAccount`. 
6. Чтобы вызвать командлет с использованием удостоверения Azure AD, передайте созданный контекст в этот командлет.

В приведенном ниже примере показано, как перечислить большие двоичные объекты в контейнере из Azure PowerShell с помощью удостоверения Azure AD. 

```powershell
$ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -UseConnectedAccount 
Get-AzureStorageBlob -Container $sample-container -Context $ctx 
```

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о ролях RBAC для службы хранилища Azure, ознакомившись с разделом [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).
- Чтобы узнать об использовании функции "Управляемое удостоверение службы" в службе хранилища Azure, ознакомьтесь с разделом [Аутентификация в Azure AD с помощью функции "Управляемое удостоверение службы Azure" (предварительная версия)](storage-auth-aad-msi.md).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
- Дополнительные сведения об интеграции Azure AD для очередей и больших двоичных объектов Azure доступны в записи блога команды разработчиков службы хранилища Azure [Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/) (Объявление о выпуске предварительной версии аутентификации Azure AD для службы хранилища Azure).
