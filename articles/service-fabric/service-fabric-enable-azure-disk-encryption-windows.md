---
title: Включение шифрования дисков в кластерах Service Fabric на платформе Windows | Документация Майкрософт
description: В этой статье описывается, как включить шифрование дисков в узлах кластера Service Fabric в Azure с помощью Azure Resource Manager и Azure Key Vault.
services: service-fabric
documentationcenter: .net
author: v-viban
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/23/2018
ms.author: v-viban
ms.openlocfilehash: 0b84d270cc50888822b8463df2b95aedaa34ee9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660251"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Включение шифрования дисков в узлах кластера Service Fabric на платформе Windows 
> [!div class="op_single_selector"]
> * [Шифрование дисков для Windows](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Шифрование дисков для Linux](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

Выполните следующие действия, чтобы включить шифрование дисков в узлах кластера Service Fabric для Windows. Вам нужно будет сделать это для каждого типа узлов или масштабируемых наборов виртуальных машин. Для шифрования узлов мы будем использовать возможности шифрования дисков Azure для масштабируемых наборов виртуальных машин.

В этом руководстве описываются следующие процедуры.

* Основные понятия, которые необходимо учитывать, чтобы включить шифрование дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Windows.
* Предварительные требования, которые необходимо выполнить перед включением шифрования дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Windows.
* Шаги, которые необходимо выполнить, чтобы включить шифрование дисков в масштабируемом наборе виртуальных машин кластера Service Fabric на платформе Windows.


## <a name="prerequisites"></a>предварительным требованиям
1. **Автоматическая регистрация**. Для использования предварительной версии службы шифрования дисков масштабируемого набора виртуальных машин требуется автоматическая регистрация.
2. Вы можете самостоятельно зарегистрировать свою подписку, выполнив следующие шаги: 
```Powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
3. Подождите около 10 минут, пока состояние не изменится на "Зарегистрировано". Вы можете проверить состояние, выполнив следующую команду: 
```Powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```
4. **Azure Key Vault**. Создайте новое хранилище ключей в тех же подписке и регионе, что и масштабируемый набор, и настройте политику доступа EnabledForDiskEncryption в хранилище ключей с помощью командлета PowerShell. Вы также можете установить политику, используя пользовательский интерфейс Key Vault на портале Azure: 
```Powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
5. Установите последнюю версию [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest), в которой есть новые команды шифрования.
6. Установите последнюю версию [пакета Azure SDK из выпуска Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Ниже приведены командлеты ADE масштабируемого набора виртуальных машин для включения ([Set](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmssdiskencryptionextension?view=azurermps-4.4.1)) шифрования, получения ([Get](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/get-azurermvmssvmdiskencryption?view=azurermps-4.4.1)) состояния шифрования и удаления ([disable](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/disable-azurermvmssdiskencryption?view=azurermps-4.4.1)) шифрования в экземпляре масштабируемого набора.

| Get-Help | Version (версия) |  Источник  |
| ------------- |-------------| ------------|
| Get-AzureRmVmssDiskEncryptionStatus   | 3.4.0 или выше | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryptionStatus   | 3.4.0 или выше | AzureRM.Compute |
| Disable-AzureRmVmssDiskEncryption   | 3.4.0 или выше | AzureRM.Compute |
| Get-AzureRmVmssDiskEncryption   | 3.4.0 или выше | AzureRM.Compute |
| Get-AzureRmVmssVMDiskEncryption   | 3.4.0 или выше | AzureRM.Compute |
| Set-AzureRmVmssDiskEncryptionExtension   | 3.4.0 или выше | AzureRM.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Поддерживаемые сценарии для шифрования дисков
* Шифрование масштабируемого набора виртуальных машин поддерживается только для масштабируемых наборов, созданных с помощью управляемых дисков, и не поддерживается для масштабируемых наборов на основе собственных (или неуправляемых) дисков.
* Шифрование масштабируемого набора виртуальных машин поддерживается для томов операционной системы и данных в масштабируемых наборах Windows. Отключение шифрования поддерживается для томов операционной системы и данных в масштабируемых наборах Windows.
* В текущей предварительной версии не поддерживаются операции пересоздания образа и обновления виртуальных машин в масштабируемом наборе.


### <a name="create-new-cluster-and-enable-disk-encryption"></a>Создание нового кластера и включение шифрования дисков

Используйте следующие команды для создания кластера и включения шифрования дисков с использованием шаблона Azure Resource Manager и самозаверяющего сертификата.

### <a name="log-in-to-azure"></a>Вход в Azure 

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>Использование собственного пользовательского шаблона 

Если необходимо создать пользовательский шаблон в соответствии с потребностями, настоятельно рекомендуется начать с одного из шаблонов, доступных в [примерах шаблонов Azure Service Fabric](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Настройте шаблон кластера [customize-your-cluster-template], следуя инструкциям и учитывая пояснения в [разделе ниже].

Если у вас уже есть пользовательский шаблон, дважды проверьте, что все три параметра, связанные с сертификатом, в шаблоне и файле параметров имеют указанные ниже имена, а значения равны нулю.

```Json
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


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```CLI

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

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Развертывание приложения в кластере Service Fabric на платформе Windows
Выполните шаги и рекомендации по [развертыванию приложения в кластере](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-deploy-remove-applications).


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>Включите шифрование диска для масштабируемого набора виртуальных машин кластера Service Fabric, созданного выше.
 
```Powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```CLI

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Проверьте, включено ли шифрование дисков для масштабируемых наборов виртуальных машин Windows.
Получите состояние всего масштабируемого набора виртуальных машин или любого экземпляра в масштабируемом наборе. Ознакомьтесь с командами ниже.
Кроме того, пользователь может войти на виртуальную машину в масштабируемом наборе и убедиться, что диски зашифрованы.

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```CLI

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Отключение шифрования дисков для масштабируемого набора виртуальных машин кластера Service Fabric 
Отключение шифрования дисков распространяется на масштабируемый набор, а не на экземпляр. 

```Powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Дополнительная информация
Теперь у вас есть безопасный кластер, в котором можно включить или отключить шифрование дисков для масштабируемого набора виртуальных машин кластера Service Fabric. [Enable Disk encryption for service fabric Linux cluster nodes](service-fabric-enable-azure-disk-encryption-linux.md) (Включение шифрования дисков в кластерах Service Fabric на платформе Linux) 

