---
title: Проверка подлинности с помощью Azure Active Directory для доступа к данным больших двоичных объектов и очередей из приложений (предварительная версия) | Документация Майкрософт
description: Использование Azure Active Directory для проверки подлинности из приложения и последующей авторизации запросов к ресурсам службы хранилища Azure (предварительная версия).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/07/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 6a0b7139fd8d216397090154a4324c8e4305a939
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48816384"
---
# <a name="authenticate-with-azure-active-directory-from-an-azure-storage-application-preview"></a>Проверка подлинности с помощью Azure Active Directory из приложения службы хранилища (предварительная версия)

Ключевым преимуществом использования Azure Active Directory (Azure AD) со службой хранилища Azure является то, что учетные данные больше не нужно хранить в коде. Вместо этого можно запросить маркер доступа OAuth 2.0 из Azure AD. Azure AD осуществляет аутентификацию субъекта безопасности (пользователя, группы или субъекта-службы), запустившего приложение. Если аутентификация пройдена успешно, Azure AD возвращает маркер доступа приложению, и затем приложение может использовать его для авторизации запросов к службе хранилища Azure.

В этой статье показано, как настроить приложение для использования аутентификации на основе Azure AD. В примерах кода используется .NET, но в других языках применяется аналогичный подход.

Прежде чем субъект безопасности сможет проходить аутентификацию из приложения службы хранилища Azure, нужно настроить параметры управления доступом на основе ролей (RBAC) для этого субъекта безопасности. Служба хранилища Azure определяет роли RBAC, которые включают в себя разрешения для контейнеров и очередей. При назначении роли RBAC субъекту безопасности ему предоставляется доступ к соответствующему ресурсу. Дополнительные сведения см. в разделе [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).

Общие сведения о процессе предоставления кода OAuth 2.0 представлены в разделе [Авторизация доступа к веб-приложениям Azure Active Directory с помощью потока предоставления кода OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Регистрация приложения в клиенте Azure AD

При использовании Azure AD для предоставления доступа к ресурсам хранилища в первую очередь необходимо зарегистрировать клиентское приложение в клиенте Azure AD. Зарегистрировав приложение, вы сможете вызвать [библиотеку аутентификации Azure Active Directory ](../../active-directory/active-directory-authentication-libraries.md) (ADAL) из кода. Эта библиотека предоставляет API для аутентификации с помощью Azure AD в приложении. Регистрация приложения также дает возможность авторизовать вызовы из этого приложения к интерфейсам API службы хранилища Azure с помощью маркера доступа.

При регистрации приложения вы отправляете сведения о приложении в Azure AD. После этого служба Azure AD предоставляет идентификатор клиента (также называемый *идентификатором приложения*), позволяющий связать с ней приложение во время выполнения. Дополнительные сведения об идентификаторе клиента см. в статье [Объекты приложения и субъекта-службы в Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md).

Чтобы зарегистрировать приложение службы хранилища Azure, выполните инструкции, приведенные в разделе [Добавление приложения](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) статьи [Интеграция приложений с Azure Active Directory](../../active-directory/active-directory-integrating-applications.md). При регистрации приложения как собственного вы можете указать любой допустимый универсальный код ресурса (URI) в качестве **URI перенаправления**. Реальную конечную точку указывать необязательно.

![Снимок экрана регистрации приложения службы хранилища в Azure AD](./media/storage-auth-aad-app/app-registration.png)

После регистрации приложения в разделе **Параметры** отобразится идентификатор приложения (идентификатор клиента).

![Снимок экрана с идентификатором клиента](./media/storage-auth-aad-app/app-registration-client-id.png)

Дополнительные сведения о регистрации приложения в Azure AD см. в разделе [Интеграция приложений с Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). 

## <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Предоставление зарегистрированному приложению разрешений для службы хранилища Azure

Далее необходимо предоставить приложению разрешения для вызова интерфейсов API службы хранилища Azure. Это позволит вашему приложению авторизовать вызовы к службе хранилища Azure с помощью Azure AD.

1. В области навигации слева на портале Azure выберите **Все службы** и выполните поиск элемента **Регистрация приложений**.
2. Найдите имя зарегистрированного приложения, созданное на предыдущем шаге.
3. Выберите это приложение и щелкните **Параметры**. В разделе **Доступ через API** выберите **Необходимые разрешения**.
4. В колонке **Необходимые разрешения** нажмите кнопку **Добавить**.
5. В разделе **Select an API** (Выбор API) выполните поиск фразы "Служба хранилища Azure" и выберите элемент **Служба хранилища Azure** из списка результатов.

    ![Снимок экрана с разрешениями для службы хранилища](media/storage-auth-aad-app/registered-app-permissions-1.png)

6. В разделе **Выбор разрешений** установите флажок рядом с элементом **Access Azure Storage** (Доступ к службе хранилища Azure) и щелкните **Выбрать**.
7. Нажмите кнопку **Done**(Готово).

Теперь в окне **Необходимые разрешения** вы можете видеть, что приложение Azure AD имеет доступ к Azure Active Directory и службе хранилища Azure. Разрешения службе Azure AD предоставляются автоматически во время первой регистрации приложения в Azure AD.

![Снимок экрана с разрешениями зарегистрированного приложения](media/storage-auth-aad-app/registered-app-permissions-2.png)

## <a name="net-code-example-create-a-block-blob"></a>Пример кода .NET: создание блочного BLOB-объекта

В примере кода показано, как получить маркер доступа из Azure AD. Маркер доступа используется для аутентификации указанного пользователя и последующей авторизации запроса на создание блочного BLOB-объекта. Чтобы этот пример работал, сначала выполните инструкции в предыдущих разделах.

> [!NOTE]
> Как владельцу учетной записи хранения Azure вам автоматически не назначаются разрешения на доступ к данным. Для службы хранилища Azure вы должны назначить себе роль RBAC явным образом. Вы можете назначить ее на уровне подписки, группы ресурсов, учетной записи хранения, контейнера или очереди. 
>
> Например, чтобы запустить пример кода с учетной записью хранения, владельцем которой вы являетесь, и вашим удостоверением пользователя, необходимо назначить себе роль участника данных для большого двоичного объекта. В противном случае вызов для создания большого двоичного объекта завершится ошибкой с кодом состояния HTTP 403 (запрещено). Дополнительные сведения см. в разделе [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).

### <a name="well-known-values-for-authentication-with-azure-ad"></a>Известные значения для аутентификации с помощью Azure AD

Чтобы выполнить аутентификацию субъекта безопасности с помощью Azure AD, необходимо добавить в код некоторые известные значения.

#### <a name="azure-ad-oauth-endpoint"></a>Конечная точка Azure AD для OAuth

Ниже представлена базовая конечная точка центра Azure AD для OAuth 2.0, где *tenant-id* — это идентификатор клиента Active Directory (или идентификатор каталога).

`https://login.microsoftonline.com/<tenant-id>/oauth2/token`

Идентификатор клиента определяет клиент Azure AD для аутентификации. Чтобы получить идентификатор клиента, выполните указания, описанные в разделе **Получение идентификатора клиента для Azure Active Directory**.

#### <a name="storage-resource-id"></a>Идентификатор ресурса хранилища

Идентификатор ресурса службы хранилища Azure позволяет получить маркер для аутентификации запросов к службе хранилища Azure.

`https://storage.azure.com/`

### <a name="get-the-tenant-id-for-your-azure-active-directory"></a>Получение идентификатора клиента для Azure Active Directory

Чтобы получить идентификатор клиента, сделайте следующее:

1. На портале Azure выберите Active Directory.
2. Щелкните **Свойства**.
3. Скопируйте значение GUID, указанное для параметра **Идентификатор каталога**. Это значение также называется идентификатором клиента.

![Снимок экрана, отображающий копирование идентификатора клиента](./media/storage-auth-aad-app/aad-tenant-id.png)

### <a name="add-references-and-using-statements"></a>Добавление ссылок и инструкций using  

В Visual Studio установите предварительную версию клиентской библиотеки службы хранилища Azure. В меню **Сервис** выберите **Диспетчер пакетов NuGet**, а затем — **Консоль диспетчера пакетов**. Введите следующую команду в консоли, чтобы установить последнюю версию клиентской библиотеки для .NET:

```
Install-Package WindowsAzure.Storage
```

Также установите последнюю версию ADAL:

```
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```

Далее добавьте приведенные ниже инструкции using в код.

```dotnet
using System.Globalization;
using Microsoft.IdentityModel.Clients.ActiveDirectory; //ADAL client library for getting the access token
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
```

### <a name="get-an-oauth-token-from-azure-ad"></a>Получение токена OAuth из Azure AD

Добавьте метод, который запрашивает токен из Azure AD. Чтобы запросить токен, вызовите метод [AuthenticationContext.AcquireTokenAsync](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory.authenticationcontext.acquiretokenasync). Убедитесь, что вы получили следующие значения на выполненных ранее шагах:

- идентификатор клиента (каталога);
- идентификатор клиента (приложения);
- URI перенаправления клиента.

```dotnet
static string GetUserOAuthToken()
{
    const string ResourceId = "https://storage.azure.com/";
    const string AuthEndpoint = "https://login.microsoftonline.com/{0}/oauth2/token";
    const string TenantId = "<tenant-id>"; // Tenant or directory ID

    // Construct the authority string from the Azure AD OAuth endpoint and the tenant ID. 
    string authority = string.Format(CultureInfo.InvariantCulture, AuthEndpoint, TenantId);
    AuthenticationContext authContext = new AuthenticationContext(authority);

    // Acquire an access token from Azure AD. 
    AuthenticationResult result = authContext.AcquireTokenAsync(ResourceId, 
                                                                "<client-id>", 
                                                                new Uri(@"<client-redirect-uri>"), 
                                                                new PlatformParameters(PromptBehavior.Auto)).Result;

    return result.AccessToken;
}
```

### <a name="create-the-block-blob"></a>Создание блочного BLOB-объекта

Наконец, используйте маркер доступа для создания учетных данных службы хранилища и с их помощью создайте большой двоичный объект.

```dotnet
// Get the access token.
string accessToken = GetUserOAuthToken();

// Use the access token to create the storage credentials.
TokenCredential tokenCredential = new TokenCredential(accessToken);
StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

// Create a block blob using those credentials
CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://storagesamples.blob.core.windows.net/sample-container/Blob1.txt"), storageCredentials);
```

> [!NOTE]
> Интеграция Azure AD со службой хранилища Azure требует использования протокола HTTPS для операций службы хранилища Azure.

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о ролях RBAC для службы хранилища Azure, ознакомившись с разделом [Manage access rights to Azure Storage data with RBAC (Preview)](storage-auth-aad-rbac.md) (Управление правами доступа к данным в службе хранилища Azure с помощью RBAC (предварительная версия)).
- Дополнительные сведения об использовании управляемых удостоверений для ресурсов Azure со службой хранилища Azure см. в статье [Аутентификация в Azure AD с помощью функции "Управляемое удостоверение службы Azure" (предварительная версия)](storage-auth-aad-msi.md).
- Чтобы узнать о входе в Azure CLI и PowerShell с использованием идентификатора Azure AD, ознакомьтесь с разделом [Использование удостоверения Azure AD для доступа к службе хранилища Azure с помощью интерфейса командной строки или PowerShell (предварительная версия)](storage-auth-aad-script.md).
- Дополнительные сведения об интеграции Azure AD для очередей и больших двоичных объектов Azure доступны в записи блога команды разработчиков службы хранилища Azure [Announcing the Preview of Azure AD Authentication for Azure Storage](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/) (Объявление о выпуске предварительной версии аутентификации Azure AD для службы хранилища Azure).



