---
title: Управляемые удостоверения в Службе приложений и Функциях Azure | Документация Майкрософт
description: Справочник принципов использования и руководство по настройке управляемых удостоверений в Службе приложений Azure и Функциях Azure.
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/25/2018
ms.author: mahender
ms.openlocfilehash: fb9b50ecb16bd37d005403a14ea11c6d89f50dfe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983653"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Использование управляемых удостоверений в Службе приложений и Функциях Azure

> [!NOTE] 
> Служба приложений в Linux и функция "Веб-приложение для контейнеров" еще не поддерживают управляемые удостоверения.

> [!Important] 
> При переносе приложения между подписками или клиентами управляемые удостоверения для Службы приложений и Функций Azure не будут работать в надлежащем режиме. Для приложения нужно получить новое удостоверение, отключив службу и включив ее снова. См. раздел [Удаление удостоверения](#remove) ниже. Также нужно обновить политики доступа во всех зависимых ресурсах, чтобы они использовали новое удостоверение.

В этой статье показано, как создавать управляемое удостоверение для приложений Службы приложений и Функций Azure, а также как их использовать для получения доступа к другим ресурсам. Управляемое удостоверение от Azure Active Directory позволяет приложению без проблем получать доступ к другим ресурсам, защищенным AAD, таким как Azure Key Vault. Удостоверения управляются платформой Azure, и для них не нужно подготавливать или изменять секреты. Дополнительные сведения об управляемых удостоверениях в Azure Active Directory см. в статье [Что такое управляемые удостоверения для ресурсов Azure?](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Создание приложения с удостоверением

Чтобы создать приложение с удостоверением, необходимо задать дополнительные свойства в приложении.

### <a name="using-the-azure-portal"></a>Использование портала Azure

Чтобы настроить управляемое удостоверение на портале, сначала необходимо создать обычное приложение, а затем активировать соответствующую функцию.

1. Создайте приложение на портале обычным образом. Перейдите к нему на портале.

2. Если используется приложение-функция, перейдите к **функциям платформы**. Для других типов приложений прокрутите вниз до группы **параметров** в левой области навигации.

3. Выберите **Управляемое удостоверение**.

4. Установите для параметра **Register with Azure Active Directory** (Регистрация с помощью Azure Active Directory) значение **Вкл.** Выберите команду **Сохранить**.

![Управляемое удостоверение в Службе приложений](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Использование Azure CLI

Чтобы настроить управляемое удостоверение с помощью Azure CLI, используйте команду `az webapp identity assign` для имеющегося приложения. Примеры из этого раздела можно выполнять тремя способами:

- использовать [Azure Cloud Shell](../cloud-shell/overview.md) с портала Azure;
- использовать внедренный компонент Azure Cloud Shell с помощью кнопки "Попробовать", расположенной в правом верхнем углу каждого блока кода ниже.
- [установить последнюю версию Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 или выше), если вы предпочитаете использовать локальную консоль CLI. 

Ниже описаны действия по созданию веб-приложения и присвоению удостоверения ему с помощью CLI:

1. Если вы используете Azure CLI в локальной консоли, сначала выполните вход в Azure с помощью команды [az login](/cli/azure/reference-index#az-login). Используйте учетную запись, связанную с подпиской Azure, с помощью которой нужно развернуть приложение.

    ```azurecli-interactive
    az login
    ```
2. Создайте веб-приложение с помощью CLI. Дополнительные примеры использования CLI со службой приложений см. в статье [Примеры Azure CLI](../app-service/app-service-cli-samples.md).

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Выполните команду `identity assign`, чтобы создать удостоверение для этого приложения.

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Использование Azure PowerShell

Ниже описаны действия по созданию веб-приложения и присвоению удостоверения ему с помощью Azure PowerShell:

1. При необходимости установите Azure PowerShell с помощью инструкции, приведенной в [руководстве Azure PowerShell](/powershell/azure/overview), а затем выполните команду `Login-AzureRmAccount`, чтобы создать подключение к Azure.

2. Создайте веб-приложение с помощью Azure PowerShell. Дополнительные примеры применения Azure PowerShell со службой приложений см. в статье [Примеры сценариев Azure PowerShell](../app-service/app-service-powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Выполните команду `identity assign`, чтобы создать удостоверение для этого приложения.

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Использование шаблона Azure Resource Manager

Шаблон Azure Resource Manager можно использовать для автоматизации развертывания ресурсов Azure. Дополнительные сведения о развертывании в службе приложений и Функциях см. в статьях [Предсказуемые подготовка и развертывание микрослужб в Azure](../app-service/app-service-deploy-complex-application-predictably.md) и [Автоматизация развертывания ресурсов приложения-функции для службы "Функции Azure"](../azure-functions/functions-infrastructure-as-code.md).

Любой ресурс типа `Microsoft.Web/sites` можно создать с помощью удостоверения, добавив следующее свойство в определение ресурса:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Таким образом, Azure создает удостоверения для приложения и управляет ими.

Например, веб-приложение может выглядеть следующим образом:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Созданный сайт будет иметь следующие дополнительные свойства:
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Где `<TENANTID>` и `<PRINCIPALID>` заменяются значениями GUID. Свойство tenantId определяет, к какому клиенту AAD относится это удостоверение. principalId — это уникальный идентификатор для нового удостоверения приложения. В AAD субъект-служба имеет то же имя, которое было присвоено экземпляру Службы приложений или Функций Azure.

## <a name="obtaining-tokens-for-azure-resources"></a>Получение маркеров для ресурсов Azure

Приложение может использовать удостоверения для получения маркеров для других ресурсов, защищенных AAD, например Azure Key Vault. Эти маркеры представляют приложение, получающее доступ к ресурсам, а не конкретного пользователя приложения. 

> [!IMPORTANT]
> Чтобы разрешить доступ из приложения, необходимо настроить целевой ресурс. Например, если вы запрашиваете маркер для Key Vault, необходимо добавить политику доступа, включая удостоверение приложения. В противном случае вызовы Key Vault будут отклонены, даже если они включают маркеры. Чтобы узнать больше о том, какие ресурсы поддерживают маркеры Azure Active Directory, см. сведения в разделе о [службах Azure, поддерживающих аутентификацию Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication).

Существует простой протокол REST для получения маркера в службе приложений и Функциях Azure. Для приложений .NET библиотека Microsoft.Azure.Services.AppAuthentication предоставляет абстракцию этого протокола и поддерживает локальную разработку.

### <a name="asal"></a>Использование библиотеки Microsoft.Azure.Services.AppAuthentication для .NET

Самый простой способ для приложений и функций .NET работать с управляемыми удостоверениями заключается в использовании пакета Microsoft.Azure.Services.AppAuthentication. Эта библиотека также позволяет локально тестировать код на компьютере разработки с использованием учетной записи пользователя из Visual Studio, [Azure CLI](/cli/azure) или встроенной проверки подлинности Active Directory. Дополнительные сведения о параметрах локальной разработки с помощью этой библиотеки см. в [Справочник Microsoft.Azure.Services.AppAuthentication]. В этом разделе показано, как начать работу с библиотекой в коде.

1. Добавьте ссылки на пакеты NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) и [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) в приложение.

2.  Добавьте в приложение следующий код:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Дополнительные сведения о пакете Microsoft.Azure.Services.AppAuthentication и операциях, которые он предоставляет, см. в [Справочник Microsoft.Azure.Services.AppAuthentication] и [примерах службы приложений и хранилищах ключей с управляемым удостоверением службы .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Использование протокола REST

Приложение с управляемым удостоверением содержит две заданные переменные среды:
- MSI_ENDPOINT;
- MSI_SECRET.

**MSI_ENDPOINT** — это локальный URL-адрес, из которого приложение может запрашивать маркеры. Чтобы получить маркер для ресурса, отправьте запрос HTTP GET к этой конечной точке, задав следующие параметры:

> [!div class="mx-tdBreakAll"]
> |Имя параметра|В|ОПИСАНИЕ|
> |-----|-----|-----|
> |resource|Запрос|Универсальный код ресурса (URI) AAD, для которого нужно получить маркер.|
> |api-version|Запрос|Версия API маркеров, которая будет использоваться. Сейчас поддерживается только одна версия: 2017-09-01.|
> |secret|Заголовок|Значение переменной среды MSI_SECRET.|


Успешный ответ 200 — OK включает текст JSON со следующими свойствами:

> [!div class="mx-tdBreakAll"]
> |Имя свойства|ОПИСАНИЕ|
> |-------------|----------|
> |access_token|Запрашиваемый маркер доступа. Вызывающая веб-служба может использовать этот маркер для проверки подлинности принимающей веб-службы.|
> |expires_on|Время истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC до истечения срока действия. Это значение используется для определения времени существования кэшированных маркеров.|
> |resource|URI идентификатора приложения принимающей вызов веб-службы.|
> |token_type|Указывает значение типа маркера. Единственный тип, поддерживаемый Azure AD — носитель. Дополнительные сведения о маркерах носителей см. в спецификации [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt) (OAuth2.0 Authorization Framework: использование маркера носителя (RFC 6750)).|


Этот ответ совпадает с [ответом для запроса маркера взаимного доступа между службами AAD](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE] 
> Переменные среды настраиваются при первом запуске процесса. Поэтому после включения управляемого удостоверения может потребоваться перезапустить приложение или повторно развернуть код, чтобы для него стали доступны `MSI_ENDPOINT` и `MSI_SECRET`.

### <a name="rest-protocol-examples"></a>Примеры протокола REST
Пример запроса может выглядеть следующим образом:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
Пример ответа может выглядеть следующим образом:
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Примеры кода
<a name="token-csharp"></a>Чтобы выполнить такой запрос на языке C#, используйте следующий код:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> Для языков .NET можно также использовать пакет [Microsoft.Azure.Services.AppAuthentication](#asal), вместо того чтобы создавать этот запрос самостоятельно.

<a name="token-js"></a>Чтобы выполнить такой запрос на языке Node.js, используйте следующий код:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>Чтобы выполнить такой запрос на языке PowerShell, используйте следующий код:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Удаление удостоверения

Чтобы удалить удостоверение, нужно отключить соответствующую функцию с помощью портала, PowerShell или интерфейса командной строки точно так же, как и при его создании. В протоколе шаблонов REST/ARM для этого нужно выбрать тип None (Отсутствует), вот так:

```json
"identity": {
    "type": "None"
}    
```

Такое удаление удостоверения приводит к удалению субъекта из AAD. Присвоенные системой идентификаторы удаляются из AAD автоматически, когда удаляется ресурс приложения.

> [!NOTE] 
> Также вы можете установить параметр приложения WEBSITE_DISABLE_MSI, который отключает локальную службу маркеров. Но при этом само удостоверение сохранится, а управляемое удостоверение будет отображаться как "включенное". По этой причине мы рекомендуем не использовать такой параметр.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Безопасное подключение Базы данных Azure SQL с использованием управляемого удостоверения службы](app-service-web-tutorial-connect-msi.md)

[Справочник Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
