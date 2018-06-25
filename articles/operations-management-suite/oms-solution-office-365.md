---
title: Решение по управлению Office 365 в Azure | Документация Майкрософт
description: В этой статье представлены сведения о настройке и использовании решения Office 365 в Azure.  Она содержит подробное описание записей Office 365, создаваемых в службе Log Analytics.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: e93860328ed6a31b7a06cc3420fb50ab4af9a00c
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236109"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Решение по управлению Office 365 в Azure (предварительная версия)

![Логотип Office 365](media/oms-solution-office-365/icon.png)

Решение по управлению Office 365 позволяет выполнять мониторинг среды Office 365 в Log Analytics.

- Отслеживать действия пользователей в учетных записях Office 365 для анализа шаблонов использования, а также определять поведенческие тенденции. Например, можно извлечь конкретные сценарии использования, такие как файлы, к которым предоставлен общий доступ за пределами организации, или самые популярные сайты SharePoint.
- Отслеживать действия администратора, чтобы контролировать изменения конфигурации или операции с высоким уровнем привилегий.
- Выявлять и анализировать нежелательное поведение пользователей, которое можно настраивать, исходя из потребностей организации.
- Выполнять аудит и проверку соответствия. Например, можно отслеживать операции доступа к файлам для конфиденциальных файлов, что поможет в процессе аудита и проверки соответствия.
- Оперативно устраняйте неполадки с помощью [поисковых запросов](../log-analytics/log-analytics-log-search.md) на основе данных о действиях Office 365 в вашей организации.

## <a name="prerequisites"></a>предварительным требованиям
Прежде чем устанавливать и настраивать это решение, необходимо иметь в наличии следующие компоненты.

- Подписка Office 365 организации.
- Учетные данные для учетной записи пользователя с правами глобального администратора.
- Для получения данных аудита необходимо [настроить аудит](https://support.office.com/en-us/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=en-US&rs=en-US&ad=US#PickTab=Before_you_begin) в подписке Office 365.  Обратите внимание, что [аудит почтовых ящиков](https://technet.microsoft.com/library/dn879651.aspx) настраивается отдельно.  Если аудит не настроен, вы все равно можете установить решение и собирать другие данные.
 

## <a name="management-packs"></a>Пакеты управления
В этом решении не предусматривается установка пакетов управления в [подключенных группах управления](../log-analytics/log-analytics-om-agents.md).
  
## <a name="install-and-configure"></a>Установка и настройка
Начните с добавления [решения Office 365 в подписку](/monitoring/monitoring-solutions.md#install-a-management-solution). Когда оно будет добавлено, вам нужно выполнить шаги по настройке, описанные в этом разделе, чтобы предоставить решению доступ к подписке Office 365.

### <a name="required-information"></a>Необходимые сведения
Прежде чем приступить к выполнению этой процедуры, соберите сведения ниже.

Из рабочей области Log Analytics:

- имя рабочей области: рабочая область, где будут собираться данные Office 365;
- имя группы ресурсов: группа ресурсов, которая содержит рабочую область;
- идентификатор подписки Azure: подписка, которая содержит рабочую область.

Из подписки Office 365:

- имя пользователя: адрес электронной почты учетной записи администратора;
- идентификатор клиента: уникальный идентификатор подписки Office 365;
- идентификатор клиента 2: 16-значная строка, представляющая клиента Office 365;
- секрет клиента: зашифрованная строка, необходимая для аутентификации.

### <a name="create-an-office-365-application-in-azure-active-directory"></a>Создание приложения Office 365 в Azure Active Directory
Первым шагом является создание приложения в Azure Active Directory, которое будет использоваться решением по управлению для доступа к решению Office 365.

1. Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com/).
1. Выберите **Azure Active Directory**, а затем — **Регистрация приложений**.
1. Щелкните **Регистрация нового приложения**.

    ![Добавление регистрации приложений](media/oms-solution-office-365/add-app-registration.png)
1. Введите **имя** приложения и **URL-адрес входа**.  Имя должно быть описательным.  Используйте _http://localhost_ для URL-адреса и оставьте в поле **Тип приложения** значение _Веб-приложение или API_.
    
    ![Создание приложения](media/oms-solution-office-365/create-application.png)
1. Щелкните **Создать** и проверьте сведения о приложении.

    ![Зарегистрированное приложение](media/oms-solution-office-365/registered-app.png)

### <a name="configure-application-for-office-365"></a>Настройка приложения для Office 365

1. Щелкните **Параметры**, чтобы открыть меню **настроек**.
1. Выберите **Свойства**. Задайте для параметра **Мультитенантный** значение _Да_.

    ![Настройка мультитенантного приложения](media/oms-solution-office-365/settings-multitenant.png)

1. В меню **Параметры** выберите **Требуемые разрешения**, а затем щелкните **Добавить**.
1. Щелкните **Select an API** (Выбор API), а затем — **Office 365 Management APIs** (Интерфейсы API управления Office 365). Выберите **Office 365 Management APIs** (Интерфейсы API управления Office 365). Нажмите кнопку **Выбрать**.

    ![Выбор API](media/oms-solution-office-365/select-api.png)

1. В разделе **Выберите разрешения** выберите следующие параметры в разделах **Разрешения приложений** и **Делегированные разрешения**:
    - чтение сведений о работоспособности служб в вашей организации;
    - чтение данных о действиях в организации;
    - чтение отчетов о действиях для вашей организации.

    ![Выбор API](media/oms-solution-office-365/select-permissions.png)

1. Нажмите кнопку **Выбрать**, а затем — **Готово**.
1. Щелкните **Предоставить разрешения**, а затем — **Да** при появлении запроса на проверку.

    ![Предоставление разрешений](media/oms-solution-office-365/grant-permissions.png)

### <a name="add-a-key-for-the-application"></a>Добавление ключа для приложения

1. В меню **Параметры** выберите **Ключи**.
1. Для нового ключа введите **описание** и **срок действия**.
1. Нажмите кнопку **Сохранить**, а затем скопируйте созданное **значение**.

    ![ключей](media/oms-solution-office-365/keys.png)

### <a name="add-admin-consent"></a>Добавление согласия администратора
Чтобы в первый раз включить учетную запись администратора, нужно предоставить согласие администратора для приложения. Это можно сделать с помощью сценария PowerShell. 

1. Сохраните следующий сценарий как *office365_consent.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,     
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId
    )
    
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
        
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $WorkspaceLocation= $Workspace.Location
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocation
    
    Function AdminConsent{
    
    $domain='login.microsoftonline.com'
    $mmsDomain = 'login.mms.microsoft.com'
    $WorkspaceLocationShort= $Workspace.PortalUrl.Split("/",$option)[1].Split(".",$option)[0]
    $WorkspaceLocationShort
    $WorkspaceLocation
    switch ($WorkspaceLocation.Replace(" ","").ToLower()) {
           "eastus"   {$OfficeAppClientId="d7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westeurope"   {$OfficeAppClientId="c9005da2-023d-40f1-a17a-2b7d91af4ede"; break}
           "southeastasia"   {$OfficeAppClientId="09c5b521-648d-4e29-81ff-7f3a71b27270"; break}
           "australiasoutheast"  {$OfficeAppClientId="f553e464-612b-480f-adb9-14fd8b6cbff8"; break}   
           "westcentralus"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break}
           "japaneast"   {$OfficeAppClientId="b07d97d3-731b-4247-93d1-755b5dae91cb"; break}
           "uksouth"   {$OfficeAppClientId="f232cf9b-e7a9-4ebb-a143-be00850cd22a"; break}
           "centralindia"   {$OfficeAppClientId="ffbd6cf4-cba8-4bea-8b08-4fb5ee2a60bd"; break}
           "canadacentral"  {$OfficeAppClientId="c2d686db-f759-43c9-ade5-9d7aeec19455"; break}
           "eastus2"  {$OfficeAppClientId="7eb65b0-8167-4b5d-b371-719a2e5e30cc"; break}
           "westus2"  {$OfficeAppClientId="98a2a546-84b4-49c0-88b8-11b011dc8c4e"; break} #Need to check
           "usgovvirginia" {$OfficeAppClientId="c8b41a87-f8c5-4d10-98a4-f8c11c3933fe"; 
                             $domain='login.microsoftonline.us';
                             $mmsDomain = 'usbn1.login.oms.microsoft.us'; break} # US Gov Virginia
           default {$OfficeAppClientId="55b65fb5-b825-43b5-8972-c8b6875867c1";
                    $domain='login.windows-ppe.net'; break} #Int
        }
    
        $OfficeAppRedirectUrl="https://$($WorkspaceLocationShort).$($mmsDomain)/Office365/Authorize"
        $OfficeAppRedirectUrl
        $domain
        Start-Process -FilePath  "https://$($domain)/common/adminconsent?client_id=$($OfficeAppClientId)&state=12345&redirect_uri=$($OfficeAppRedirectUrl)"
    }
    
    AdminConsent -ErrorAction Stop
    ```

2. Выполните следующую команду, чтобы запустить сценарий.
    ```
    .\office365_consent.ps1 -WorkspaceName <Workspace name> -ResourceGroupName <Resource group name> -SubscriptionId <Subscription ID>
    ```
    Пример:

    ```
    .\office365_consent.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631- yyyyyyyyyyyy'
    ```

1. Появится окно, аналогичное приведенному ниже. Нажмите кнопку **Принимаю**.
    
    ![Согласие администратора](media/oms-solution-office-365/admin-consent.png)

### <a name="subscribe-to-log-analytics-workspace"></a>Подписка на рабочую область Log Analytics
Последним шагом является подписка приложения на рабочую область Log Analytics. Этот шаг можно выполнить с помощью сценария PowerShell.

1. Сохраните следующий сценарий как *office365_subscription.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeUsername,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId,
        [Parameter(Mandatory=$True)][string]$OfficeClientId,
        [Parameter(Mandatory=$True)][string]$OfficeClientSecret
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    $OfficeClientSecret =[uri]::EscapeDataString($OfficeClientSecret)
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant = $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Failure {
    $line
    $formatstring = "{0} : {1}`n{2}`n" +
                    "    + CategoryInfo          : {3}`n" +
                    "    + FullyQualifiedErrorId : {4}`n"
    $fields = $_.InvocationInfo.MyCommand.Name,
              $_.ErrorDetails.Message,
              $_.InvocationInfo.PositionMessage,
              $_.CategoryInfo.ToString(),
              $_.FullyQualifiedErrorId
    
    $formatstring -f $fields
    $_.Exception.Response
    
    $line
    break
    }
    
    Function Connection-API
    {
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    
    $line
    $connectionAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/connections/office365connection_' + $SubscriptionId + $OfficeTennantId + '?api-version=2017-04-26-preview'
    $connectionAPIUrl
    $line
    
    $xms_client_tenant_Id ='1da8f770-27f4-4351-8cb3-43ee54f14759'
    
    $BodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'clientId': '" + $OfficeClientId + "',
                                    'clientSecret': '" + $OfficeClientSecret + "',
                                    'Username': '" + $OfficeUsername   + "',
                                    'Url': 'https://$($domain)/" + $OfficeTennantId + "/oauth2/token',
                                  },
                    'etag': '*',
                    'kind': 'Connection',
                    'solution': 'Connection',
                   }"
    
    $params = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id #Prod-'1da8f770-27f4-4351-8cb3-43ee54f14759'
        'Content-Type' = 'application/json'
        }
        Body = $BodyString
        Method = 'Put'
        URI = $connectionAPIUrl
    }
    $response = Invoke-WebRequest @params 
    $response
    $line
    
    }
    
    Function Office-Subscribe-Call{
    try{
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $SubscriptionId   =  $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_' + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $OfficeBodyString = "{
                    'properties': {
                                    'AuthProvider':'Office365',
                                    'office365TenantID': '" + $OfficeTennantId + "',
                                    'connectionID': 'office365connection_" + $SubscriptionId + $OfficeTennantId + "',
                                    'office365AdminUsername': '" + $OfficeUsername + "',
                                    'contentTypes':'Audit.Exchange,Audit.AzureActiveDirectory'
                                  },
                    'etag': '*',
                    'kind': 'Office365',
                    'solution': 'Office365',
                   }"
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Body = $OfficeBodyString
        Method = 'Put'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    }
    catch{ Failure }
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Connection-API -ErrorAction Stop
    Office-Subscribe-Call -ErrorAction Stop
    ```

2. Выполните следующую команду, чтобы запустить сценарий.
    ```
    .\office365_subscription.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeUsername <OfficeUsername> -OfficeTennantID <Tenant ID> -OfficeClientId <Client ID> -OfficeClientSecret <Client secret>
    ```
    Пример:

    ```
    .\office365_subscription.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeUsername 'admin@contoso.com' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx' -OfficeClientId 'f8f14c50-5438-4c51-8956-zzzzzzzzzzzz' -OfficeClientSecret 'y5Lrwthu6n5QgLOWlqhvKqtVUZXX0exrA2KRHmtHgQb='
    ```

### <a name="troublshooting"></a>Устранение неполадок

При попытке создать подписку, когда она уже имеется, может появиться следующая ошибка:

```
Invoke-WebRequest : {"Message":"An error has occurred."}
At C:\Users\v-tanmah\Desktop\ps scripts\office365_subscription.ps1:161 char:19
+ $officeresponse = Invoke-WebRequest @Officeparams
+                   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (System.Net.HttpWebRequest:HttpWebRequest) [Invoke-WebRequest], WebException
    + FullyQualifiedErrorId : WebCmdletWebResponseException,Microsoft.PowerShell.Commands.InvokeWebRequestCommand 
```

Если указаны недопустимые значения параметров, может появиться следующая ошибка:

```
Select-AzureRmSubscription : Please provide a valid tenant or a valid subscription.
At line:12 char:18
+ ... cription = (Select-AzureRmSubscription -SubscriptionId $($Subscriptio ...
+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureRmContext], ArgumentException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Profile.SetAzureRMContextCommand

```

## <a name="uninstall"></a>Удаление
Вы можете удалить решение по управлению Office 365 с помощью процесса, описанного в этом [разделе](../monitoring/monitoring-solutions.md#remove-a-management-solution). Однако это не остановит сбор данных в Office 365 и последующую их отправку в Log Analytics. Выполните процедуру ниже, чтобы отменить подписку на Office 365 и прекратить сбор данных.

1. Сохраните следующий сценарий как *office365_unsubscribe.ps1*.

    ```
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzureRmAccount -ErrorAction Stop}
    $Subscription = (Select-AzureRmSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzureRMOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    # Load ADAL Azure AD Authentication Library Assemblies
    $adal = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Services\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    $null = [System.Reflection.Assembly]::LoadFrom($adal)
    $null = [System.Reflection.Assembly]::LoadFrom($adalforms)
     
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $global:authResultARM = $authContext.AcquireToken($resourceAppIdURIARM, $clientId, $redirectUri, "Auto")
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Выполните следующую команду, чтобы запустить сценарий.

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Пример:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

## <a name="data-collection"></a>Сбор данных
### <a name="supported-agents"></a>Поддерживаемые агенты
Решение Office 365 не извлекает данные из каких-либо [агентов OMS](../log-analytics/log-analytics-data-sources.md).  Оно извлекает данные непосредственно из Office 365.

### <a name="collection-frequency"></a>Частота сбора
Для сбора данных может потребоваться несколько часов. После запуска сбора при создании каждой записи Office 365 отправляет в службу Log Analytics [уведомление веб-перехватчика](https://msdn.microsoft.com/office-365/office-365-management-activity-api-reference#receiving-notifications) с подробными данными. Эта запись становится доступной в Log Analytics в течение нескольких минут после ее получения.

## <a name="using-the-solution"></a>Использование решения
Когда вы добавите решение Office 365 в рабочую область Log Analytics, плитка **Office 365** будет добавлена на вашу панель инструментов. На этой плитке отображается количество и графическое представление количества компьютеров в вашей среде и соответствие обновлений.<br><br>
![Плитка сводки Office 365](media/oms-solution-office-365/tile.png)  

Щелкните элемент **Office 365**, чтобы открыть панель мониторинга **Office 365**.

![Панель мониторинга Office 365](media/oms-solution-office-365/dashboard.png)  

Панель мониторинга содержит столбцы, перечисленные в приведенной ниже таблице. Каждый столбец содержит десять ведущих оповещений с числом, показывающим соответствие оповещения критериям данного столбца, таким как область действия и диапазон времени. Можно выполнить поиск журналов, выводящий весь список, щелкнув элемент "Просмотреть все" внизу столбца или заголовок этого столбца.

| столбец | ОПИСАНИЕ |
|:--|:--|
| Операции | Предоставляет сведения об активных пользователях из всех отслеживаемых подписок Office 365. Также можно просмотреть количество действий, выполненных за определенный период времени.
| Exchange | Отображает разбивку по действиям Exchange Server, таким как Add-MailboxPermission (Добавление разрешения для почтового ящика) или Set-Mailbox (Настройка почтового ящика). |
| SharePoint | Отображает основные действия, выполняемые пользователями в отношении документов SharePoint. Если выполнить детализацию из этого элемента, то на странице поиска отобразятся сведения об этих действиях, такие как целевой документ и расположение действия. Например, для события доступа к файлу отобразится сам документ, к которому осуществляется доступ, связанное с ним имя учетной записи и IP-адрес. |
| Azure Active Directory | Включает в себя основные действия пользователей, такие как сброс пароля пользователя и попытка входа в систему. Если выполнить детализацию, то можно просмотреть подробные сведения об этих действиях, такие как состояние результата. Это полезно, в основном, для отслеживания подозрительных действий в Azure Active Directory. |




## <a name="log-analytics-records"></a>Записи Log Analytics

У всех записей, создаваемых решением Office 365 в рабочей области Log Analytics, свойство **Type** (Тип) имеет значение **OfficeActivity**.  Свойство **OfficeWorkload** определяет, к какой службе Office 365 относится запись — Exchange, Azure Active Directory, SharePoint или OneDrive.  Свойство **RecordType** указывает тип операции.  Эти свойства будут отличаться для каждого типа операции. Их описание приводится в таблице ниже.

### <a name="common-properties"></a>Общие свойства
Следующие свойства являются общими для всех записей Office 365.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| type | *OfficeActivity* |
| ClientIP | IP-адрес устройства, которое использовалось при записи действия в журнал. IP-адрес отображается в формате IPv4- или IPv6-адреса. |
| OfficeWorkload | Служба Office 365, к которой относится запись.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Операция | Имя действия пользователя или администратора.  |
| OrganizationId | Идентификатор GUID для клиента Office 365 организации. Это значение для вашей организации всегда будет одинаковым независимо от службы Office 365, в которой оно встречается. |
| RecordType | Тип выполняемой операции. |
| ResultStatus | Указывает, было ли успешным действие (указанное в свойстве Operation). Возможные значения: Succeeded (Успешно), PartiallySucceded (Выполнено частично) и Failed (Сбой). Для действий администратора Exchange возможные значения — True (Истина) или False (Ложь). |
| UserId | Имя участника-пользователя (UPN) для пользователя, который выполнил действие, приведшее к регистрации в журнале данной записи. Например, my_name@my_domain_name. Обратите внимание, что сюда также включаются записи для действий, выполняемых системными учетными записями (такими как SHAREPOINT\system или NTAUTHORITY\SYSTEM). | 
| UserKey | Альтернативный идентификатор пользователя, определенного в свойстве UserId.  Например, значение этого свойства может заполняться уникальным идентификатором Passport (PUID) для событий, выполняемых пользователями в SharePoint, OneDrive для бизнеса и Exchange. Это свойство также может указывать то же значение, что и свойство UserID событий, происходящих в других службах, и событий, выполняемых системными учетными записями.|
| UserType | Тип пользователя, выполнившего операцию.<br><br>Администратор<br>Приложение<br>DcAdmin<br>Обычный<br>Reserved<br>ServicePrincipal<br>системный; |


### <a name="azure-active-directory-base"></a>Основа Azure Active Directory
Следующие свойства являются общими для всех записей Azure Active Directory.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | Тип события Azure AD. |
| ExtendedProperties | Расширенные свойства события Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Вход в учетную запись Azure Active Directory
Эти записи создаются при попытке пользователя Active Directory войти в систему.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectoryAccountLogon |
| Приложение | Приложение, которое активирует событие входа в учетную запись, например Office 15. |
| Клиент | Подробные сведения о клиентском устройстве, операционной системе устройства и браузере устройства, который использовался при событии входа в учетную запись. |
| LoginStatus | Это свойство получается непосредственно из параметра OrgIdLogon.LoginStatus. Сопоставление различных неудачных попыток входа в систему можно выполнить с помощью алгоритмов оповещения. |
| UserDomain | Сведения об идентификации клиента (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory
Эти записи создаются при внесении изменений или дополнений в объекты Azure Active Directory.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | Пользователь, в отношении которого было выполнено действие (указанное в свойстве Operation). |
| Субъект | Пользователь или субъект-служба, которые выполнили действие. |
| ActorContextId | Идентификатор GUID организации, к которой принадлежит субъект. |
| ActorIpAddress | IP-адрес субъекта в формате IPv4- или IPv6-адреса. |
| InterSystemsId | Идентификатор GUID, который отслеживает действия по компонентам в службе Office 365. |
| IntraSystemId |   Идентификатор GUID, который создается Azure Active Directory для отслеживания действия. |
| SupportTicketId | Идентификатор запроса пользователя в службу поддержки для действия в ситуациях "действовать от имени". |
| TargetContextId | Идентификатор GUID организации, к которой принадлежит целевой пользователь. |


### <a name="data-center-security"></a>Безопасность центра обработки данных
Эти записи создаются на основе данных аудита безопасности центра обработки данных.  

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| EffectiveOrganization | Имя клиента, в отношении которого выполнялось повышение прав или командлет. |
| ElevationApprovedTime | Метка времени, когда был утвержден запрос на повышение прав. |
| ElevationApprover | Имя представителя корпорации Майкрософт. |
| ElevationDuration | Период, в течение которого действовало повышение прав. |
| ElevationRequestId |  Уникальный идентификатор запроса на повышение прав. |
| ElevationRole | Роль, для которой было запрошено повышение прав. |
| ElevationTime | Время начала повышения прав. |
| Start_Time | Время начала выполнения командлета. |


### <a name="exchange-admin"></a>Администратор Exchange
Эти записи создаются при внесении изменений в конфигурацию Exchange.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |  Указывает, кем выполнялся командлет (пользователем организации, персоналом центра обработки данных корпорации Майкрософт, учетной записью службы центра обработки данных или делегированным администратором). Значение False указывает на то, что командлет выполнялся пользователем вашей организации. Значение True означает, что командлет выполнялся персоналом центра обработки данных, учетной записью службы центра обработки данных или делегированным администратором. |
| ModifiedObjectResolvedName |  Это понятное имя объекта, который был изменен с помощью командлета. Эта запись вносится в журнал только в том случае, если командлет изменяет объект. |
| OrganizationName | Имя клиента. |
| OriginatingServer | Имя сервера, из которого выполнялся командлет. |
| Параметры | Имя и значение для всех параметров, которые использовались с командлетом, указанным в свойстве Operations. |


### <a name="exchange-mailbox"></a>Почтовый ящик Exchange
Эти записи создаются при внесении изменений или дополнений в почтовые ящики Exchange.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Сведения о почтовом клиенте, который использовался для выполнения операции, такие как версия браузера, версия Outlook или сведения о мобильном устройстве. |
| Client_IPAddress | IP-адрес устройства, которое использовалось при записи операции в журнал. IP-адрес отображается в формате IPv4- или IPv6-адреса. |
| ClientMachineName | Имя компьютера, на котором размещен клиент Outlook. |
| ClientProcessName | Клиент электронной почты, который использовался для доступа к почтовому ящику. |
| ClientVersion | Версия клиента электронной почты. |
| InternalLogonType | Зарезервировано для внутреннего использования. |
| Logon_Type | Указывает тип пользователя, который осуществил доступ к почтовому ящику и выполнил операцию, записанную в журнал. |
| LogonUserDisplayName |    Понятное имя пользователя, который выполнил операцию. |
| LogonUserSid | ИД безопасности пользователя, который выполнил операцию. |
| MailboxGuid | Идентификатор GUID Exchange почтового ящика, к которому осуществлялся доступ. |
| MailboxOwnerMasterAccountSid | ИД безопасности главной учетной записи для учетной записи владельца почтового ящика. |
| MailboxOwnerSid | ИД безопасности владельца почтового ящика. |
| MailboxOwnerUPN | Адрес электронной почты пользователя, владеющего почтовым ящиком, к которому осуществлялся доступ. |


### <a name="exchange-mailbox-audit"></a>Аудит почтового ящика Exchange
Эти записи создаются при создании записей аудита почтового ящика.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Элемент | Представляет элемент, в отношении которого выполнялась операция. | 
| SendAsUserMailboxGuid | Идентификатор GUID Exchange почтового ящика, к которому осуществлялся доступ для отправки почты с помощью команды "Отправить как". |
| SendAsUserSmtp | SMTP-адрес пользователя, олицетворяемого при отправке. |
| SendonBehalfOfUserMailboxGuid | Идентификатор GUID Exchange почтового ящика, к которому осуществлялся доступ для отправки почты с помощью команды "Отправить от имени". |
| SendOnBehalfOfUserSmtp | SMTP-адрес пользователя, от имени которого отправлялась почта. |


### <a name="exchange-mailbox-audit-group"></a>Группа аудита почтового ящика Exchange
Эти записи создаются при внесении изменений или дополнений в группы Exchange.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Сведения о каждом элементе в группе. |
| CrossMailboxOperations | Указывает, задействовано ли в операции более одного почтового ящика. |
| DestMailboxId | Используется только в том случае, если параметр CrossMailboxOperations имеет значение True. Указывает GUID целевого почтового ящика. |
| DestMailboxOwnerMasterAccountSid | Используется только в том случае, если параметр CrossMailboxOperations имеет значение True. Указывает ИД безопасности главной учетной записи владельца целевого почтового ящика. |
| DestMailboxOwnerSid | Используется только в том случае, если параметр CrossMailboxOperations имеет значение True. Указывает ИД безопасности целевого почтового ящика. |
| DestMailboxOwnerUPN | Используется только в том случае, если параметр CrossMailboxOperations имеет значение True. Указывает имя участника-пользователя (UPN) владельца целевого почтового ящика. |
| DestFolder | Папка назначения для операций, таких как перемещение. |
| Папка | Папка, где находится группа элементов. |
| Папки |     Сведения об исходных папках, задействованных в операции. Например, если папки выбираются, а затем удаляются. |


### <a name="sharepoint-base"></a>Основа SharePoint
Эти свойства являются общими для всех записей SharePoint.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Указывает, что событие произошло в SharePoint. Возможные значения: SharePoint или ObjectModel. |
| ItemType | Тип объекта, к которому осуществлялся доступ или который был изменен. Подробные сведения о типах объектов см. в таблице ItemType. |
| MachineDomainInfo | Сведения об операциях синхронизации устройств. Эти сведения сообщаются только в том случае, если они присутствуют в запросе. |
| MachineId |   Сведения об операциях синхронизации устройств. Эти сведения сообщаются только в том случае, если они присутствуют в запросе. |
| Site_ | Идентификатор GUID сайта, где расположен файл или папка, к которым осуществляется доступ пользователя. |
| Source_Name | Сущность, которая активировала отслеживаемую операцию. Возможные значения: SharePoint или ObjectModel. |
| UserAgent | Сведения о клиенте или браузере пользователя. Эти сведения предоставляются клиентом или браузером. |


### <a name="sharepoint-schema"></a>Схема SharePoint
Эти записи создаются при внесении изменений в конфигурацию SharePoint.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Необязательная строка для пользовательских событий. |
| Event_Data |  Необязательные полезные данные для пользовательских событий. |
| ModifiedProperties | Свойство включено для событий администрирования, таких как добавление пользователя в качестве участника сайта или группы администраторов семейства веб-сайтов. Это свойство включает в себя имя свойства, которое было изменено (например, группа администраторов сайта), новое значение измененного свойства (такое как пользователь, добавленный в качестве администратора сайта) и предыдущее значение измененного объекта. |


### <a name="sharepoint-file-operations"></a>Операции с файлами SharePoint
Эти записи создаются в ответ на операции с файлами в SharePoint.

| Свойство | ОПИСАНИЕ |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | Расширение файла, который копируется или перемещается. Это свойство отображается только для событий FileCopied и FileMoved. |
| DestinationFileName | Имя файла, который копируется или перемещается. Это свойство отображается только для событий FileCopied и FileMoved. |
| DestinationRelativeUrl | URL-адрес папки назначения, в которую копируется или перемещается файл. Сочетание значений параметров SiteURL, DestinationRelativeURL и DestinationFileName совпадает со значением свойства ObjectID, которое является полным именем пути для скопированного файла. Это свойство отображается только для событий FileCopied и FileMoved. |
| SharingType | Тип разрешений на совместное использование, назначенных пользователю, которому был предоставлен доступ к ресурсу. Пользователь идентифицируется с помощью параметра UserSharedWith. |
| Site_Url | URL-адрес сайта, где расположен файл или папка, к которым осуществляется доступ пользователя. |
| SourceFileExtension | Расширение файла, к которому осуществлялся доступ пользователя. Это свойство остается пустым, если объект, к которому осуществлялся доступ, является папкой. |
| SourceFileName |  Имя файла или папки, к которым осуществляется доступ пользователя. |
| SourceRelativeUrl | URL-адрес папки, содержащей файл, к которому осуществляется доступ пользователя. Сочетание значений параметров SiteURL, SourceRelativeURL и SourceFileName совпадает со значением свойства ObjectID. Это свойство является полным именем пути для файла, к которому осуществляется доступ пользователя. |
| UserSharedWith |  Пользователь, которому был предоставлен доступ к ресурсу. |




## <a name="sample-log-searches"></a>Пример поисков журналов
Следующая таблица содержит примеры запросов поиска по журналу для получения записей обновлений, собранных этим решением.

| Запрос | ОПИСАНИЕ |
| --- | --- |
|Количество всех операций для подписки Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Использование сайтов SharePoint|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl | sort by Count asc|
|Операции доступа к файлам по типу пользователя|search in (OfficeActivity) OfficeWorkload =~ "azureactivedirectory" and "MyTest"|
|Поиск по определенному ключевому слову|Type=OfficeActivity OfficeWorkload=azureactivedirectory "MyTest"|
|Мониторинг внешних действий в отношении Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Дополнительная информация
* Используйте поиск по журналам в [Log Analytics](../log-analytics/log-analytics-log-searches.md), чтобы просматривать подробные данные об обновлениях.
* [Создайте собственные панели мониторинга](../log-analytics/log-analytics-dashboards.md) для отображения избранных поисковых запросов Office 365.
* [Создайте оповещения](../log-analytics/log-analytics-alerts.md), чтобы заранее получать уведомления о важных действиях в Office 365.  
