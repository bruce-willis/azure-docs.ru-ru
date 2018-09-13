---
title: Создание кластера Azure Service Fabric | Документация Майкрософт
description: Сведения о настройке защищенного кластера Service Fabric в Azure с помощью Azure Resource Manager.  Вы можете создать кластер с помощью шаблона по умолчанию или собственного шаблона.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: aab985270cf17b94d6353536c96a3825b5e3b73f
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287348"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Создание кластера Service Fabric с помощью Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [портала Azure](service-fabric-cluster-creation-via-portal.md)
>
>

[Кластер Azure Service Fabric](service-fabric-deploy-anywhere.md) — это подключенный к сети набор виртуальных машин, в котором вы развертываете микрослужбы и управляете ими.  Кластер Service Fabric, работающий в Azure, — это ресурс Azure, развернутый с помощью Azure Resource Manager. В этой статье описывается развертывание защищенного кластера Service Fabric в Azure с помощью Resource Manager. Вы можете использовать шаблон кластера по умолчанию или собственный шаблон.  Если у вас еще нет своего шаблона, [узнайте, как его создать](service-fabric-cluster-creation-create-template.md).

Безопасность кластера настраивается при его начальной настройке. Позже ее невозможно будет изменить. Перед настройкой кластера ознакомьтесь со статьей [Сценарии защиты кластера Service Fabric][service-fabric-cluster-security]. В Azure для защиты кластера и его конечных точек, проверки подлинности клиентов и шифрования данных Service Fabric требует использования сертификата x509. Azure Active Directory также рекомендуется использовать для обеспечения безопасного доступа к конечным точкам управления. Перед созданием кластера необходимо создать клиенты и пользователей Azure AD.  Дополнительные сведения см.в статье [Set up Azure Active Directory for client authentication](service-fabric-cluster-creation-setup-aad.md) (Настройка Azure AD для проверки подлинности клиентов).

Перед созданием рабочего кластера для запуска производственных рабочих нагрузок мы рекомендуем сначала ознакомиться с [контрольным списком готовности рабочей среды](service-fabric-production-readiness-checklist.md).

## <a name="prerequisites"></a>Предварительные требования 
В этой статье для развертывания кластера используйте модули Azure CLI или Service Fabric RM для PowerShell:

* [Azure PowerShell версии 4.1 и выше][azure-powershell].
* [Azure CLI версии 2.0 и выше][azure-CLI].

Найти справочную документацию для модулей Service Fabric можно здесь:
* [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric)
* [az SF CLI module](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Вход в Azure

Перед выполнением любой из команд в этой статье нужно выполнить вход в Azure.

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Создание кластера с помощью генерируемого системой самозаверяющего сертификата

Выполните следующие команды, чтобы создать кластер, защищенный с помощью самозаверяющего сертификата, сгенерированного системой. Эта команда устанавливает основной сертификат кластера, который используется для обеспечения безопасности кластера и настройки доступа администратора для выполнения операций управления с использованием этого сертификата.  Самозаверяющие сертификаты эффективны для защиты тестовых кластеров.  Рабочие кластеры должны быть защищены с помощью сертификата, выданного центром сертификации (ЦС).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Использование шаблона кластера по умолчанию, который поставляется в модуле

Выполните следующую команду, чтобы быстро создать кластер с помощью шаблона по умолчанию, указав минимальные параметры.

Используемый шаблон доступен в [примерах шаблонов Azure Service Fabric для Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) и [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure).

Следующая команда позволяет создать кластеры Windows или Linux (нужно указать соответствующую ОС). Команды PowerShell или CLI также сохраняют сертификат в указанную папку *CertificateOutputFolder*, но сначала нужно убедиться, что папка для сертификатов уже создана. Команда принимает и другие параметры (например, номер SKU виртуальной машины).

> [!NOTE]
> Следующая команда PowerShell работает только с версией PowerShell для Azure Resource Manager выше 6.1. Чтобы узнать текущую версию PowerShell для Azure Resource Manager, выполните команду PowerShell "Get-Module AzureRM". Если нужно обновить версию PowerShell для Azure Resource Manager, щелкните [эту ссылку](/powershell/azure/install-azurerm-ps?view=azurermps-6.3.0). 
>
>

Развертывание кластера с помощью PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Развертывание кластера с помощью Azure CLI:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Использование собственного шаблона

Если необходимо создать пользовательский шаблон в соответствии с потребностями, советуем начать с одного из шаблонов, доступных в [примерах шаблонов Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Узнайте, как [настроить собственный шаблон кластера][customize-your-cluster-template].

Если у вас уже есть пользовательский шаблон, дважды проверьте, что все три параметра, связанные с сертификатом, в шаблоне и файле параметров имеют указанные ниже имена, а значения равны нулю.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Развертывание кластера с помощью PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Развертывание кластера с помощью Azure CLI:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Создание кластера с помощью своего сертификата X.509

Создайте кластер, используя следующую команду, если уже имеется сертификат, который нужно использовать для защиты кластера.

Если это подписанный ЦС сертификат, который также используется в других целях, рекомендуется предоставить отдельную группу ресурсов, специально для хранилища ключей. Рекомендуется поместить хранилище ключей в собственной группе ресурсов. Так вы сможете удалять группы ресурсов для вычислений и хранения, включая группу ресурсов, в которой размещен кластер Service Fabric, без потери ключей и секретов. **Группа ресурсов с хранилищем ключей *должна находиться в том же регионе*, что и кластер, который ее использует**.

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Использование шаблона по умолчанию с типом для одного или пяти узлов, который поставляется в модуле
Используемый шаблон доступен в [примерах Azure для Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) и [Ubuntu](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure).

Развертывание кластера с помощью PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Развертывание кластера с помощью Azure CLI:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Использование собственного шаблона кластера
Если необходимо создать пользовательский шаблон в соответствии с потребностями, советуем начать с одного из шаблонов, доступных в [примерах шаблонов Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Узнайте, как [настроить собственный шаблон кластера][customize-your-cluster-template].

Если у вас уже есть пользовательский шаблон, дважды проверьте, что все три параметра, связанные с сертификатом, в шаблоне и файле параметров имеют указанные ниже имена, а значения равны нулю.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Развертывание кластера с помощью PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Развертывание кластера с помощью Azure CLI:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Использование указателя на секрет, переданный в хранилище ключей

Чтобы использовать имеющееся хранилище ключей, [необходимо включить его для развернутой службы](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI), благодаря чему поставщик вычислительных ресурсов сможет получить из него сертификаты и установить его на узлах кластера.

Развертывание кластера с помощью PowerShell:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Развертывание кластера с помощью Azure CLI:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>Дополнительная информация
На этом этапе у вас есть защищенный кластер, работающий в Azure. Далее [подключитесь к этому кластеру](service-fabric-connect-to-secure-cluster.md) и узнайте, как [управлять секретами приложений](service-fabric-application-secret-management.md).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template
