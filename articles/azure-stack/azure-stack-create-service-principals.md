---
title: Создание субъекта-службы для Azure Stack | Документация Майкрософт
description: Описывается создание субъекта-службы, который можно использовать в Azure Resource Manager в сочетании с контролем доступа на основе ролей для управления доступом к ресурсам.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2018
ms.author: sethm
ms.openlocfilehash: f7233d6a27b9ec3d58f33f7032bbec7a646d24f7
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42366125"
---
# <a name="provide-applications-access-to-azure-stack"></a>Предоставление приложениям доступа к Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Если приложению нужен доступ для развертывания или настройки ресурсов Azure Stack через Azure Resource Manager, вам следует создать субъект-службу, который будет использоваться как учетные данные для этого приложения.  Этому субъекту-службе вы сможете делегировать только минимально необходимые разрешения.  

Например, можно создать средство управления конфигурацией, которое использует Azure Resource Manager для создания списка ресурсов Azure.  Чтобы реализовать этот сценарий, вам нужно создать субъект-службу, назначить ему роль читателя и предоставить средству управления конфигурацией доступ только для чтения. 

Субъекты-службы предпочтительнее использовать для запуска приложения с вашими учетными данными по следующим причинам:

* Для субъекта-службы можно назначить разрешения, которые отличаются от ваших разрешений учетной записи. Как правило, приложение получает именно те разрешения, которые требуются для его работы.
* Не требуется изменять учетные данные приложения в случае изменения ваших обязанностей.
* Можно использовать сертификат, чтобы автоматизировать аутентификацию при выполнении автоматического сценария.  

## <a name="getting-started"></a>Приступая к работе

Прежде всего нужно создать субъект-службу. Процесс будет разным в зависимости от способа развертывания Azure Stack.  В этом документе описывается создание субъекта-службы для [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) и [службы федерации Active Directory (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Создав субъект-службу, вы [делегируете разрешения](azure-stack-create-service-principals.md#assign-role-to-service-principal) для этой роли, используя единый процесс для AD FS и Azure AD.     

## <a name="create-service-principal-for-azure-ad"></a>Создание субъекта-службы для Azure AD

Если Azure Stack развернут с использованием Azure AD в качестве хранилища идентификаторов, создание субъекта-службы выполняется точно так же, как для Azure.  В этом разделе описан процесс с использованием портала.  Прежде чем начать, проверьте [необходимые разрешения Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).

### <a name="create-service-principal"></a>Создание субъекта-службы
В этом разделе вы создадите в Azure AD приложение (субъект-службу), которое будет представлять ваше приложение.

1. Войдите в учетную запись Azure через [портал Azure](https://portal.azure.com).
2. Выберите **Azure Active Directory** > **Регистрация приложений** > **Добавить**.   
3. Укажите имя и URL-адрес для приложения. Выберите тип создаваемого приложения: **веб-приложение или API** или **собственное приложение**. Выбрав нужные значения, нажмите кнопку **Создать**.

Субъект-служба для приложения создан.

### <a name="get-credentials"></a>Получение учетных данных
Если вход выполняется программными средствами, вам потребуются идентификатор приложения и ключ аутентификации для веб-приложения или API. Получить эти значения можно следующим образом.

1. В Active Directory в разделе **регистрации приложений** выберите нужное приложение.

2. Скопируйте **идентификатор приложения** и сохраните его в коде приложения. Приложения в разделе [примеров приложений](#sample-applications) используют это значение как идентификатор клиента.

     ![Идентификатор клиента](./media/azure-stack-create-service-principal/image12.png)
3. Чтобы создать ключ аутентификации для веб-приложения или API, выберите **Параметры** > **Ключи**. 

4. Введите описание и срок действия ключа. Затем нажмите кнопку **Сохранить**.

После этого отобразится значение ключа. Это значение нельзя будет получить позже, поэтому скопируйте его сразу. Значение ключа необходимо предоставить вместе с идентификатором приложения для входа от имени приложения. Сохраните значение ключа, чтобы приложение могло получить к нему доступ.

![сохраненный ключ](./media/azure-stack-create-service-principal/image15.png)


После этого [назначьте приложению роль](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Создание субъекта-службы для AD FS
Когда вы развернете Azure Stack с использованием AD FS, для создания субъекта-службы, назначения роли для доступа и входа с этим идентификатором можно использовать PowerShell.

Этот скрипт выполняется из привилегированной конечной точки на виртуальной машине ERCS.


Требования:
- Требуется сертификат.

**Параметры**

Необходимо указать следующие сведения в качестве входных для параметров службы автоматизации:


|Параметр|ОПИСАНИЕ|Пример|
|---------|---------|---------|
|ИМЯ|Имя учетной записи имени субъекта-службы|MyAPP|
|ClientCertificates|Массив объектов сертификата|Сертификат X509|
|ClientRedirectUris<br>(необязательный параметр)|URI перенаправления приложения|         |

**Пример**

1. Откройте сеанс Windows PowerShell с повышенными правами и выполните следующие команды.

   > [!NOTE]
   > Этот пример создает самозаверяющий сертификат. При выполнении этих команд в рабочей среде используйте команду Get-Certificate, чтобы получить объект сертификата для сертификата, который вы хотите использовать.

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # This produces a self signed cert for testing purposes.  It is prefered to use a managed certificate for this.
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. We will read this from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```

2. Когда работа службы автоматизации завершится, будут отображены необходимые сведения для использования имени субъекта-службы. 

   Например: 

   ```
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="assign-a-role"></a>Назначение роли
Созданному субъекту-службе необходимо [назначить роль](azure-stack-create-service-principals.md#assign-role-to-service-principal).

### <a name="sign-in-through-powershell"></a>Вход с помощью PowerShell
Назначив нужную роль, вы сможете войти в Azure Stack с помощью следующей команды, используя созданный субъект-службу:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ClientId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Назначение роли субъекту-службе
Чтобы обеспечить доступ к ресурсам в подписке, необходимо назначить приложению роль. Укажите, какая роль предоставит приложению необходимые разрешения. Дополнительные сведения о доступных ролях см. в статье [RBAC: встроенные роли](../role-based-access-control/built-in-roles.md).

Вы можете задать область действия на уровне подписки, группы ресурсов или ресурса. Разрешения наследуют более низкие уровни области действия. Например, добавление приложения в роль читателя для группы ресурсов означает, что оно может считывать группу ресурсов и все содержащиеся в ней ресурсы.

1. На портале Azure Stack перейдите на уровень области действия, в которой вы хотите разместить приложение. Например, чтобы назначить роль в области действия подписки выберите **Подписки**. Или же вы можете выбрать группу ресурсов либо отдельный ресурс.

2. Выберите определенную подписку, группу ресурсов или ресурс, которым будет назначено приложение.

     ![выбрать подписку для назначения](./media/azure-stack-create-service-principal/image16.png)

3. Выберите **Управление доступом (IAM)**.

     ![выбрать доступ](./media/azure-stack-create-service-principal/image17.png)

4. Выберите **Добавить**.

5. Выберите роль, которая будет назначена приложению.

6. Найдите приложение и выберите его.

7. Нажмите кнопку **ОК**, чтобы завершить назначение роли. Вы увидите свое приложение в списке пользователей, назначенных выбранной роли для выбранной области действия.

Итак, вы создали субъект-службу и назначили ему роль. Теперь вы можете использовать его в приложении для доступа к ресурсам Azure Stack.  

## <a name="next-steps"></a>Дополнительная информация

См. сведения о [добавлении пользователей для служб федерации Active Directory](azure-stack-add-users-adfs.md) и 
[управлении разрешениями пользователей](azure-stack-manage-permissions.md).
