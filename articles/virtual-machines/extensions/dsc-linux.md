---
title: Расширение Azure DSC для Linux | Документация Майкрософт
description: Установка пакетов OMI и DSC для обеспечения возможности настройки виртуальной машины Linux в Azure с помощью Desired State Configuration.
services: virtual-machines-linux
documentationcenter: ''
author: balukambala
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: roiyz
ms.openlocfilehash: 4a057b88d8d511965d694aaf4494f79bba88e47a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412653"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Расширение DSC для Linux (Microsoft.OSTCExtensions.DSCForLinux)

## <a name="overview"></a>Обзор

Desired State Configuration (DSC) — это платформа, которая позволяет управлять инфраструктурой ИТ и разработки, используя конфигурацию как код.

Расширение DSCForLinux публикуется и поддерживается корпорацией Майкрософт. Оно устанавливает агенты OMI и DSC на виртуальных машинах Azure. Расширение DSC также может выполнить следующие действия:


- регистрация виртуальной машины Linux в учетной записи службы автоматизации Azure для извлечения конфигураций из службы автоматизации Azure (Register ExtensionAction);
- отправка конфигураций MOF на виртуальную машину Linux (Push ExtensionAction);
- применение конфигурации Meta MOF на виртуальной машине Linux для настройки опрашивающего сервера с целью извлечения конфигурации узла (Pull ExtensionAction);
- установка пользовательских модулей DSC на виртуальную машину Linux (Install ExtensionAction);
- удаление пользовательских модулей DSC с виртуальной машины Linux (Remove ExtensionAction).

## <a name="prerequisites"></a>Предварительные требования

### <a name="operating-system"></a>Операционная система

Расширение DSC Linux поддерживает все [ОС, поддерживаемые расширением](https://azurewiki.cloudapp.netVMAgentExtension/extensionSupportedOSs) за исключением следующих.

| Дистрибутив | Version (версия) |
|---|---|
| CentOS Linux | 6.5 и более новая |
| Ubuntu| 12.04 LTS, 14.04 LTS, 16.04 LTS  |
| RHEL| 6.5 и более новая  |
| openSUSE| 13.1 и более новая  |
| SUSE Linux Enterprise Server| 11 SP3 и более новая  |

  
 
### <a name="internet-connectivity"></a>Подключение к Интернету

Для расширения DSCForLinux требуется, чтобы целевая виртуальная машина была подключена к Интернету. Например расширение Register требует подключения к службе автоматизации. Для других действий, например Pull, Push, Install требуется подключение к хранилищу Azure или GitHub. Это зависит от параметров, указанных клиентом.

## <a name="extension-schema"></a>Схема расширения

### <a name="11-public-configuration"></a>1.1 Открытая конфигурация

Ниже приведены все поддерживаемые параметры открытой конфигурации:

* `FileUri` (необязательный, строчный): код URI файла MOF, Meta MOF или ZIP с пользовательскими ресурсами.
* `ResourceName` (необязательный, строчный): имя пользовательского модуля ресурсов.
* `ExtensionAction` (необязательный, строчный): указывает, что делает расширения. Допустимые значения: Register, Push, Pull, Install, Remove. Если этот параметр не указан, по умолчанию он распознается как действие отправки.
* `NodeConfigurationName` (необязательный, строчный): имя применяемой конфигурации узла.
* `RefreshFrequencyMins` (необязательный, целочисленный): указывает частоту (в минутах), с которой DSC пытается получить конфигурацию от опрашивающего сервера. 
       Если конфигурация на опрашивающем сервере отличается от текущей на целевом узле, она копируется в ожидающее хранилище и применяется.
* `ConfigurationMode` (необязательный, строчный): указывает, каким образом DSC следует применить конфигурацию. Допустимые значения: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins` (необязательный, целочисленный): указывает интервал (в минутах), с которым DSC проверяет, что конфигурация находится в требуемом состоянии.

> [!NOTE]
> Если вы используете версию старее 2.3, параметр mode совпадает с ExtensionAction. Термин mode выполнял слишком много функций. Поэтому во избежание путаницы, начиная с версии 2.3, используется ExtensionAction. Для обеспечения обратной совместимости, расширение поддерживает как mode, так и ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1.2 Защищенная конфигурация

Ниже приведены все поддерживаемые параметры защищенной конфигурации:

* `StorageAccountName` (необязательный, строчный): имя учетной записи хранения содержащей файл.
* `StorageAccountKey` (необязательный, строчный): ключ учетной записи хранения содержащей файл.
* `RegistrationUrl` (необязательный, строчный): URL-адрес учетной записи службы автоматизации Azure.
* `RegistrationKey` (необязательный, строчный): ключ доступа учетной записи службы автоматизации Azure.


## <a name="scenarios"></a>Сценарии

### <a name="register-to-azure-automation-account"></a>Регистрация в учетной записи службы автоматизации Azure
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Формат PowerShell
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Применение MOF-файла конфигурации (в учетной записи хранения Azure) к виртуальной машине

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Формат PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Применение MOF-файла конфигурации (в общедоступном хранилище) к виртуальной машине

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Формат PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Применение файла конфигурации Meta MOF (в учетной записи хранения Azure) к виртуальной машине

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Формат PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Применение файла конфигурации Meta MOF (в общедоступном хранилище) к виртуальной машине
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Формат PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Установка пользовательского модуля ресурсов (ZIP-файл в учетной записи хранения Azure) на виртуальной машине
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Формат PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Установка пользовательского модуля ресурсов (ZIP-файл в общедоступном хранилище) на виртуальной машине
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Формат PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Удаление пользовательского модуля ресурсов из виртуальной машины
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Формат PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Шаблоны идеально подходят для развертывания одной или нескольких виртуальных машин, требующих настройки после развертывания, например подключения к службе автоматизации Azure. 

Пример шаблона Resource Manager — [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) и [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Дополнительную информацию о шаблонах Resource Manager см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

### <a name="21-using-azure-cliazure-cli"></a>2.1. Использование [**Azure CLI**] [azure-cli]
Прежде чем развертывать расширение DSCForLinux, необходимо настроить `public.json` и `protected.json` в соответствии с различными сценариями, описанными в разделе 3.

#### <a name="211-classic"></a>2.1.1. Классический
Классический режим еще называют режимом управления службами Azure. Его можно включить с помощью команды:
```
$ azure config mode asm
```

Расширение DSCForLinux развертывается командой:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Следующая команда позволяет узнать последнюю версию расширения:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Диспетчер ресурсов
Чтобы переключиться в режим Azure Resource Manager, выполните команду:
```
$ azure config mode arm
```

Расширение DSCForLinux развертывается командой:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> В режиме Azure Resource Manager `azure vm extension list` на данный момент недоступна.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Использование [**Azure PowerShell**] [azure-powershell]

#### <a name="221-classic"></a>2.2.1 Классический

Вы можете войти в учетную запись Azure (режим управления службами Azure), с помощью команды:

```powershell>
Add-AzureAccount
```

Затем следует развернуть расширение DSCForLinux с помощью команды:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

После этого необходимо изменить содержимое $privateConfig и $publicConfig в соответствии с различными сценариями, описанными в разделе выше. 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="222resource-manager"></a>2.2.2.Диспетчер ресурсов

Вы можете войти в учетную запись Azure (режим Azure Resource Manager) с помощью команды:

```powershell>
Login-AzureRmAccount
```

Дополнительную информацию об использовании Azure PowerShell с Azure Resource Manager см. [**здесь**](../../azure-resource-manager/powershell-azure-resource-manager.md).

Расширение DSCForLinux развертывается командой:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

После этого необходимо изменить содержимое $privateConfig и $publicConfig в соответствии с различными сценариями, описанными в разделе выше. 
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureRmVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертывания расширения можно получить на портале Azure, а также использовав Azure CLI. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Код ошибки 51 указывает на неподдерживаемый дистрибутив или неподдерживаемое действие расширения.
Иногда расширению DSC Linux не удается установить OMI, если на компьютере уже есть более новая версия OMI. [error response: (000003)Downgrade not allowed] (сообщение об ошибке: (000003) откат на более старую версию не разрешен)



### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/community/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о расширениях виртуальных машин см. в обзоре [расширений и компонентов виртуальной машины для Linux](features-linux.md).
