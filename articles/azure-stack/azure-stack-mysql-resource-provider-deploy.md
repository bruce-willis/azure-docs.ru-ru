---
title: Использование баз данных MySQL в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть базы данных MySQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов MySQL Server.
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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296014"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Использование баз данных MySQL в Microsoft Azure Stack
Используйте поставщик ресурсов MySQL Server, чтобы предоставлять доступ к базам данных MySQL посредством службы Azure Stack. Служба поставщика ресурсов MySQL запущена на виртуальной машине поставщика ресурсов MySQL. Это виртуальная машина Windows Server Core.

## <a name="deploy-the-resource-provider"></a>Развертывание поставщика ресурсов 
1. Если вы не сделали этого ранее, зарегистрируйте пакет SDK и скачайте образ основных компонентов Windows Server 2016 Datacenter, который доступен в разделе "Marketplace Management" (Управление Marketplace). Использовать можно только образ основных компонентов Windows Server 2016. Вы можете применить сценарий, чтобы создать [образ Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Но обязательно выберите образ основных компонентов). 

2. Войдите на узел, на котором доступна виртуальная машина для привилегированной конечной точки:
    - Если установлен пакет SDK для Azure, войдите на физический узел.  
    - В интегрированных системах нужно использовать узел, который предоставляет доступ к привилегированной конечной точке. 
    
    >[!NOTE] 
    > Сценарий *должен* выполняться в системе Windows 10 или Windows Server 2016 с установленной последней версией среды выполнения .NET. В противном случае произойдет сбой установки. Узел с пакетом ASDK для Azure Stack соответствуют этому критерию. 
    
3. Скачайте двоичный файл адаптера поставщика ресурсов MySQL. Затем запустите самоизвлечение содержимого во временный каталог. 
    >[!NOTE]  
    > У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack. Обязательно скачайте соответствующий двоичный файл для выполняемой версии Azure Stack.

    | Версия Azure Stack | Версия MySQL RP| 
    | --- | --- | 
    | Версия 1804 (1.0.180513.1)|[MySQL RP версии 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) | 
    | Версия 1802 (1.0.180302.1) | [MySQL RP версии 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | Версия 1712 (1.0.180102.3 или 1.0.180106.1 (интегрированные системы)) | [MySQL RP версии 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  Для ASDK в рамках этого процесса создается самозаверяющий сертификат. Для интегрированных систем вам нужно предоставить подходящий сертификат. Если нужно предоставить собственный сертификат, укажите в параметре **DependencyFilesLocalPath** PFX-файл, соответствующий следующим критериям. 
    - Групповой сертификат для \*.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\> или сертификат для одного сайта с общим именем mysqladapter.dbadapter.\<регион\>.\<внешнее_полное_доменное_имя\> 
    - Это должен быть доверенный сертификат. То есть должна существовать цепочка доверия без промежуточных сертификатов. 
    - В DependencyFilesLocalPath имеется только один файл сертификата. 
    - Имя файла не должно содержать специальные знаки или пробелы. 

5. Откройте **новую** консоль PowerShell с повышенными правами (администратора). Затем перейдите в каталог, в который были извлечены файлы. Откройте новое окно, чтобы избежать проблем с неправильными модулями PowerShell, уже загруженными в систему. 

6. Выполните скрипт **DeployMySqlProvider.ps1**. Этот скрипт выполняет следующие действия: 
    - Скачивает двоичный файл соединителя MySQL (его можно указать на автономном источнике). 
    - Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack. 
    - Публикует пакеты коллекции, что позволяет развертывать базы данных SQL из коллекции. 
    - Публикует пакет коллекции для развертывания серверов размещения. 
    - Развертывает виртуальную машину с помощью образа Azure Stack для Windows Server 2016 из Marketplace и устанавливает поставщик ресурсов. 
    - Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов. 
    - Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager (для клиента и администратора). 

    Можно указать необходимые параметры в командной строке или выполнить скрипт без параметров, а затем ввести их при появлении запроса. 

    Ниже приведен пример, который можно запустить в командной строке PowerShell. (Не забудьте изменить данные учетной записи и пароли, если это требуется). 

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

    # Set the credentials for the new resource provider VM local administrator account 
    $vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 

    # And the cloudadmin credential required for privileged endpoint access. 
    $CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

    # Change the following as appropriate. 
    $PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

    # Run the installation script from the folder where you extracted the installation files. 
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>Параметры DeployMySqlProvider.ps1 
Эти параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые параметры. 

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию | 
| --- | --- | --- | 
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ | 
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ | 
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов MySQL. | _Обязательный_ | 
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ | 
| **DependencyFilesLocalPath** | Путь к локальному общему ресурсу, содержащему файл [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Если вы указали один из этих путей, в этот каталог нужно поместить и файл сертификата. | _Необязательно._ (_Обязательно_, если в системе несколько узлов). | 
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ | 
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 | 
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 | 
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  | 
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  | 
| **AcceptLicense** | Пропускает запрос на принятие условий лицензии GPL.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> Отображение номеров SKU на портале может занять до часа. Вы не сможете создать базу данных, пока не будет создан номер SKU. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack 
> [!NOTE] 
>  После выполнения сценария установки необходимо обновить страницу портала, чтобы появилась колонка администратора. 

1. Войдите на портал администрирования в качестве администратора служб. 
2. Проверьте, успешно ли выполнено развертывание. Последовательно выберите **Группы ресурсов**, затем выберите группу ресурсов **system.\<расположение\>.mysqladapter**. Убедитесь, что все четыре развертывания выполнены успешно:

      ![Проверка развертывания поставщика ресурсов MySQL](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>Дополнительная информация
[Добавление серверов размещения](azure-stack-mysql-resource-provider-hosting-servers.md)
