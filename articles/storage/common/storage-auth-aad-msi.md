---
title: Аутентификация в Azure AD с помощью функции "Управляемое удостоверение службы Azure" (предварительная версия) | Документация Майкрософт
description: Аутентификация в Azure AD с помощью функции "Управляемое удостоверение службы Azure" (предварительная версия).
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 05/18/2018
ms.author: tamram
ms.openlocfilehash: 080cb3ee536227e5ddce3fac856de79b2b061dcf
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970774"
---
# <a name="authenticate-with-azure-ad-from-an-azure-managed-service-identity-preview"></a>Аутентификация в Azure AD с помощью функции "Управляемое удостоверение службы Azure" (предварительная версия)

Служба хранилища Azure поддерживает аутентификацию Azure Active Directory (Azure AD) с использованием функции [Управляемое удостоверение службы](../../active-directory/managed-service-identity/overview.md). Функция "Управляемое удостоверение службы" (MSI) предоставляет автоматически управляемые удостоверения в Azure Active Directory (Azure AD). MSI можно использовать для аутентификации из приложений, запущенных на виртуальных машинах Azure, приложений-функций, масштабируемых наборов виртуальных машин и других ресурсов. Используя MSI и мощные возможности аутентификации Azure AD, можно избежать хранения учетных данных в приложениях, выполняемых в облаке.  

Чтобы предоставить разрешения удостоверению управляемой службы для контейнеров или очередей службы хранилища, следует назначить MSI роль RBAC, охватывающую разрешения службы хранилища. Дополнительные сведения о ролях RBAC в службе хранилища см. в разделе [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)). 

> [!IMPORTANT]
> Эта предварительная версия не предназначена для использования в рабочей среде. Соглашения об уровне обслуживания (SLA) для рабочих сред не будут доступны до выпуска общедоступной версии интеграции с Azure AD для службы хранилища Azure. Если интеграция с Azure AD пока не поддерживается для вашего сценария, продолжайте использовать в приложениях авторизацию по общему ключу или маркеры SAS. Дополнительные сведения о предварительной версии см. в статье об [аутентификации доступа к службе хранилища Azure с помощью Azure Active Directory (предварительная версия)](storage-auth-aad.md).
>
> На этапе предварительной версии распространение назначенных ролей RBAC может занимать до пяти минут.

В этой статье описана аутентификация в службе хранилища Azure с помощью MSI из виртуальной машины Azure.  

## <a name="enable-msi-on-the-vm"></a>Включение MSI на виртуальной машине

Прежде чем использовать MSI для аутентификации в службе хранилища Azure из виртуальной машины, необходимо включить MSI на этой виртуальной машине. Чтобы узнать, как включить MSI, ознакомьтесь с одной из следующих статей:

- [портал Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-service-identity/qs-configure-powershell-windows-vm.md)
- [интерфейс командной строки Azure](../../active-directory/managed-service-identity/qs-configure-cli-windows-vm.md)
- [Шаблон Azure Resource Manager](../../active-directory/managed-service-identity/qs-configure-template-windows-vm.md)
- [Пакеты SDK для Azure](../../active-directory/managed-service-identity/qs-configure-sdk-windows-vm.md)

## <a name="get-an-msi-access-token"></a>Получение маркера доступа MSI

Для выполнения аутентификации с помощью MSI приложению или сценарию необходимо получить маркер доступа MSI. Чтобы узнать, как получить маркер доступа, ознакомьтесь с разделом [Использование управляемого удостоверения службы (MSI) виртуальной машины Azure для получения маркера](../../active-directory/managed-service-identity/how-to-use-vm-token.md).

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET: создание блочного BLOB-объекта

В этом примере кода предполагается наличие маркера доступа MSI. Маркер доступа используется для авторизации удостоверения управляемой службы для создания блочного BLOB-объекта.

### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

В Visual Studio установите предварительную версию клиентской библиотеки службы хранилища Azure. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. Введите приведенную ниже команду в консоли.

```
Install-Package https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0  
```

Добавьте приведенные ниже инструкции using в код.

```dotnet
using Microsoft.WindowsAzure.Storage.Auth;
```

### <a name="create-credentials-from-the-msi-access-token"></a>Создание учетных данных на основе маркера доступа MSI

Чтобы создать блочный BLOB-объект, используйте класс **TokenCredentials**, предоставляемый предварительной версией пакета. Создайте новый экземпляр **TokenCredentials**, передав полученный ранее маркер доступа MSI.

```dotnet
// Create storage credentials from your MSI access token.
TokenCredential tokenCredential = new TokenCredential(msiAccessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using the credentials.
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
``` 

> [!NOTE]
> Интеграция Azure AD со службой хранилища Azure требует использования протокола HTTPS для операций службы хранилища Azure.

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о ролях RBAC для службы хранилища Azure, ознакомившись с разделом [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).
- Чтобы узнать об авторизации доступа к контейнерам и очередям из приложений службы хранилища, ознакомьтесь с разделом [Authenticate with Azure Active Directory from an Azure Storage application (Preview)](storage-auth-aad-app.md) (Аутентификация с помощью Azure Active Directory из приложения службы хранилища Azure (предварительная версия)).
- Чтобы узнать о входе в Azure CLI и PowerShell с использованием идентификатора Azure AD, ознакомьтесь с разделом [Использование удостоверения Azure AD для доступа к службе хранилища Azure с помощью интерфейса командной строки или PowerShell (предварительная версия)](storage-auth-aad-script.md).
- Дополнительные сведения об интеграции Azure AD для очередей и больших двоичных объектов Azure доступны в записи блога команды разработчиков службы хранилища Azure [Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/) (Объявление о выпуске предварительной версии аутентификации Azure AD для службы хранилища Azure).
