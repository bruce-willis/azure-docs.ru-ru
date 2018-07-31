---
title: Руководство по использованию Azure Key Vault из веб-приложения | Документы Майкрософт
description: В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: barclayn
ms.openlocfilehash: ff59e39e54433aa673b093e2ee1fbe8c74010e54
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171329"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>Руководство по использованию Azure Key Vault из веб-приложения

В этом учебнике показано, как использовать хранилище ключей Azure из веб-приложения Azure. Здесь приводится процесс доступа к секрету из Azure Key Vault для использования в веб-приложении. Затем в этом руководстве вместо секрета клиента используется сертификат. Этот учебник поможет веб-разработчикам понять принципы создания веб-приложений в Azure.

Из этого руководства вы узнаете, как выполнять следующие задачи: 

> [!div class="checklist"]
> * Добавление параметров приложения в файл web.config
> * Добавление метода для получения маркера доступа
> * Получение маркера при запуске приложения
> * Проверка подлинности с помощью сертификата клиента

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения действий в этом руководстве требуется:

* URI для секрета кода в хранилище ключей Azure
* Идентификатор клиента и секрет клиента для веб-приложения, зарегистрированного в Azure Active Directory, которое имеет доступ к вашему хранилищу ключей
* Веб-приложение. В этом руководстве показаны действия для приложения ASP.NET MVC, развернутого в Azure в качестве веб-приложения.

Выполните действия, описанные в статье по [началу работы с Azure Key Vault](key-vault-get-started.md), чтобы получить URI секрета, идентификатор клиента, секрет клиента и зарегистрировать приложение. Веб-приложение будет обращаться для доступа к хранилищу и должно быть зарегистрировано в Azure Active Directory. Ему также требуются права доступа к хранилищу ключей. Если эти условия не выполнены, вернитесь к разделу "Регистрация приложения" в учебнике "Приступая к работе" и повторите перечисленные шаги. Дополнительные сведения о веб-приложениях Azure см. в статье [Обзор веб-приложений](../app-service/app-service-web-overview.md).

В этом примере показана подготовка удостоверений AAD вручную. Вместо этого следует использовать [Управляемое удостоверение службы (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview). MSI автоматически подготавливает удостоверения Azure AD. Для получения дополнительных сведений см. пример на сайте [GitHub](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) и связанное [руководство пр MSI со службой приложений и функциями](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). См. дополнительные сведения о связанном с Key Vault [MSI](tutorial-web-application-keyvault.md).


## <a id="packages"></a>Добавление пакетов NuGet

Существует два пакета, которые необходимо установить для веб-приложения.

* Библиотека проверки подлинности Active Directory содержит методы для взаимодействия с Azure Active Directory и управления удостоверениями пользователей.
* Библиотека Azure Key Vault содержит методы для взаимодействия с Azure Key Vault.

Оба пакета можно установить с помощью консоли диспетчера пакетов, используя команду Install-Package.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Изменение файла Web.Config

Существует три параметра приложения, которые необходимо добавить в файл web.config следующим образом. Фактические значения будут добавлены на портале Azure, чтобы получить дополнительный уровень безопасности.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>Добавление метода для получения маркера доступа

Для использования API хранилища ключей требуется маркер доступа. Клиент хранилища ключей обрабатывает вызовы API хранилища ключей, но вам необходимо предоставить ему функцию, которая возвращает маркер доступа. Ниже приведен код для получения маркера доступа из Azure Active Directory. Этот код можно расположить в любом месте приложения. Я добавлю его в класс Utils или EncryptionHelper.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>Получение секрета при запуске приложения

Теперь нам требуется, чтобы код вызвал API хранилища ключей и получил секрет. Следующий код можно поместить в любом месте при условии, что он вызывается перед тем, как его необходимо использовать. Код помещен в событие запуска приложения в файле Global.asax, чтобы он выполнялся один раз при запуске, а секрет становился доступным для приложения.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Добавление параметров приложения на портале Azure

Если у вас есть веб-приложение Azure, то вы можете добавить фактические значения параметров приложений на портале Azure. При выполнении этого шага фактические значения не будут размещены в файле web.config, а будут защищены с помощью портала, где предоставляются отдельные возможности управления доступом. Эти значения будут заменены на значения, введенные в файле web.config. Убедитесь, что имена совпадают.

![Параметры приложения на портале Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Проверка подлинности с помощью сертификата вместо секрета клиента

Теперь, когда вам известно о проверке подлинности приложения Azure AD с помощью идентификатора клиента и секрета клиента, воспользуйтесь идентификатором клиента и сертификатом. Ниже приведены действия по использованию сертификата в веб-приложении Azure.

1. Получение или создание сертификата
2. Связывание сертификата с приложением Azure AD
3. Добавление кода в веб-приложение, чтобы использовать сертификат
4. Добавление сертификата в веб-приложение

### <a name="get-or-create-a-certificate"></a>Получение или создание сертификата

 В этом руководстве создается тестовый сертификат. Далее приведен сценарий по созданию самозаверяющего сертификата. Измените каталог, в котором будут созданы файлы сертификата.  Для начальной и конечной даты сертификата используется текущая дата плюс один год.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = "c:\data\KVWebApp.pfx" 
$CerFilePath = "c:\data\KVWebApp.cer" 
$DNSName = "MyComputer.Contoso.com" 
$Password ="MyPassword" 
$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\LocalMachine\My" -NotBefore 05/15/2018 -NotAfter 05/15/2019 
Export-PfxCertificate -cert $cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $cert -FilePath $CerFilePath 
```

Запишите дату окончания и пароль для PFX-файла (в этом примере: 15 мая 2019 г. и test123). Они потребуются для приведенного ниже сценария. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Связывание сертификата с приложением Azure AD

Теперь, когда у вас есть сертификат, необходимо связать его с приложением Azure AD. Связь можно выполнить с помощью PowerShell. Чтобы связать сертификат с приложением Azure AD, выполните следующие команды:

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

После этого приложение можно будет увидеть в Azure AD. При поиске регистраций приложений в диалоговом окне поиска необходимо выбрать **Мои приложения**, а не "Все приложения". 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Добавление кода в веб-приложение, чтобы использовать сертификат

Теперь в веб-приложение будет добавлен код для доступа к сертификату и его использования для проверки подлинности. 

Во-первых, есть код для доступа к сертификату. Обратите внимание, что параметр StoreLocation имеет значение CurrentUser вместо LocalMachine и что мы указали false для метода Find, поскольку используем тестовый сертификат.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



Далее следует код, который использует CertificateHelper и создает ClientAssertionCertificate, необходимый для проверки подлинности.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Здесь представлен новый код для получения токена доступа. Этот код заменяет метод GetToken из предыдущего примера. Для удобства задано другое имя. Весь этот код помещен в класс Utils проекта веб-приложения для удобства использования.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



Последнее изменение кода находится в методе Application_Start. Сначала необходимо вызвать метод GetCert() для загрузки ClientAssertionCertificate. А затем мы изменяем метод обратного вызова, который использовался при создании нового KeyVaultClient. Этот код заменяет код из предыдущего примера.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Добавление сертификата в веб-приложение на портале Azure

Добавление сертификата в веб-приложение — это простой двухэтапный процесс. Сначала войдите на портал Azure и перейдите к своему веб-приложению. В колонке "Параметры" веб-приложения щелкните запись **Параметры SSL**. После открытия окна отправьте сертификат KVWebApp.pfx, созданный в предыдущем примере. Не забывайте пароль для PFX-файла.

![Добавление сертификата в веб-приложение на портале Azure][2]

Последнее, что требуется выполнить, — добавить в веб-приложение параметр с именем WEBSITE\_LOAD\_CERTIFICATES и значением "*". Это позволит гарантировать загрузку всех сертификатов. Если требуется загрузить только переданные сертификаты, можно ввести разделенный запятыми список их отпечатков.


## <a name="clean-up-resources"></a>Очистка ресурсов
Если служба приложений, хранилище ключей и приложение Azure AD, которые использовалась в этом руководстве, больше не нужны, удалите их.  


## <a id="next"></a>Дальнейшие действия
> [!div class="nextstepaction"]
>[Справочник по API для управления Azure Key Vault](/dotnet/api/overview/azure/keyvault/management)


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 