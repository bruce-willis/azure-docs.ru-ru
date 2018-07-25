---
title: Обновление поставщика ресурсов MySQL в Azure Stack | Документы Майкрософт
description: Сведения о том, как обновить поставщика ресурсов MySQL в Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 4e894eaee6bb151b480204905d0a98324f5c353b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049601"
---
# <a name="update-the-mysql-resource-provider"></a>Обновление поставщика ресурсов MySQL 

*Область применения: интегрированные системы Azure Stack.*

При обновлении сборки Azure Stack может быть выпущен новый адаптер поставщика ресурсов SQL. Имеющийся адаптер может продолжать работу. Тем не менее мы рекомендуем как можно скорее обновить его до последней сборки. 

>[!IMPORTANT]
>Обновления необходимо устанавливать в порядке их выпуска. Пропускать версии нельзя. Список версий см. в разделе с [предварительными требованиями для развертывания поставщика ресурсов](.\azure-stack-mysql-resource-provider-deploy.md#prerequisites).

## <a name="update-the-mysql-resource-provider-adapter-integrated-systems-only"></a>Обновление адаптера поставщика ресурсов MySQL (только интегрированные системы)
При обновлении сборки Azure Stack может быть выпущен новый адаптер поставщика ресурсов SQL. Имеющийся адаптер может продолжать работу. Тем не менее мы рекомендуем как можно скорее обновить его до последней сборки.  
 
Чтобы обновить поставщик ресурсов, воспользуйтесь сценарием **UpdateMySQLProvider.ps1**. Этот процесс очень похож на процесс установки поставщика ресурсов, который описан в этой статье в разделе [Развертывание поставщика ресурсов](#deploy-the-resource-provider). Сценарий входит в состав скачиваемых ресурсов поставщика ресурсов. 

Сценарий **UpdateMySQLProvider.ps1** создает виртуальную машину с кодом последней версии поставщика ресурсов и переносит параметры из старой виртуальной машины на новую. Переносятся данные о базе данных и сервере размещения, а также необходимая запись DNS. 

>[!NOTE]
>Мы рекомендуем загрузить последний образ основных компонентов Windows Server 2016 из служб управления Marketplace. Если необходимо установить обновление, **один** MSU-пакет можно поместить в локальный каталог зависимого элемента. Если поместить несколько MSU-файлов в этом расположении, выполнение скрипта завершится ошибкой.

Этот сценарий необходимо использовать с аргументами, описанными для сценария DeployMySqlProvider.ps1. Также укажите сертификат.  

Ниже приведен пример сценария *UpdateMySQLProvider.ps1*, который можно запустить в командной строке PowerShell. (Не забудьте изменить данные учетной записи и пароли, если это требуется).  

> [!NOTE] 
> Этот процесс обновления применим только к интегрированным системам. 

```powershell 
# Install the AzureRM.Bootstrapper module and set the profile. 
Install-Module -Name AzureRm.BootStrapper -Force 
Use-AzureRmProfile -Profile 2017-03-09-profile 

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM. 
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access. 
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate. 
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
 
# Change directory to the folder where you extracted the installation files. 
# Then adjust the endpoints. 
$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
``` 
 
### <a name="updatemysqlproviderps1-parameters"></a>Параметры UpdateMySQLProvider.ps1 
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые параметры. 

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию | 
| --- | --- | --- | 
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ | 
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ | 
| **VMLocalCredential** |Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ | 
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ | 
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _Необязательно._ (_Обязательно_, если в системе несколько узлов). | 
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ | 
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 | 
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 | 
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  | 
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  | 
| **AcceptLicense** | Пропускает запрос на принятие условий лицензии GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 
 

## <a name="next-steps"></a>Дополнительная информация
[Обслуживание поставщика ресурсов MySQL](azure-stack-mysql-resource-provider-maintain.md)
