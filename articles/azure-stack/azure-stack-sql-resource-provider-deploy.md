---
title: Использование баз данных SQL в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть базы данных SQL в качестве службы для Azure Stack, и изучите простую процедуру развертывания адаптера поставщика ресурсов SQL Server.
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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938338"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Развертывание поставщика ресурсов SQL Server в Azure Stack

Используйте поставщик ресурсов SQL Server Azure Stack, чтобы предоставлять доступ к базам данных SQL в качестве службы Azure Stack. Поставщик ресурсов SQL выполняется как служба в виртуальной машине ядра сервера Windows Server 2016.

## <a name="prerequisites"></a>предварительным требованиям

Существует несколько предварительных требований, которые должны быть выполнены перед развертыванием поставщика ресурсов SQL Azure Stack. Чтобы обеспечить соответствие этим требованиям, выполните следующие действия на компьютере, который имеет доступ к привилегированной конечной точке виртуальной машины:

- Если вы еще этого не сделали, [зарегистрируйте Azure Stack](.\azure-stack-registration.md) в Azure, чтобы можно было загружать элементы Azure Marketplace.
- Добавьте необходимую виртуальную машину ядра Windows Server в Azure Stack Marketplace, загрузив образ **Windows Server 2016 Datacenter — ядро сервера**. Вы можете применить сценарий, чтобы создать [образ Windows Server 2016](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Убедитесь, что выбрали параметр ядра при запуске скрипта.

  >[!NOTE]
  >Если необходимо установить обновление, один MSU-пакет можно поместить в локальный каталог зависимого элемента. Если обнаружено несколько MSU-файлов, при установке поставщика ресурсов SQL произойдет сбой.

- Загрузите двоичный файл поставщика ресурсов SQL и запустите файл для самостоятельного извлечения содержимого во временный каталог. У поставщика ресурсов есть минимальная соответствующая сборка Azure Stack. Обязательно скачайте соответствующий двоичный файл для используемой версии Azure Stack.

    |Версия Azure Stack|Версия SQL RP|
    |-----|-----|
    |Версия 1804 (1.0.180513.1)|[SQL RP версии 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |Версия 1802 (1.0.180302.1)|[SQL RP версии 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |Версия 1712 (1.0.180102.3, 1.0.180103.2 или 1.0.180106.1 (интегрированные системы))|[SQL RP версии 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>Сертификаты

Только для интегрированных систем. Укажите сертификат SQL PaaS PKI, описанный в разделе о необязательных сертификатах PaaS в статье [Требования к инфраструктуре открытых ключей (PKI) для развертывания Azure Stack](.\azure-stack-pki-certs.md#optional-paas-certificates). Поместите PFX-файл в каталог, указанный параметром **DependencyFilesLocalPath**.

## <a name="deploy-the-sql-resource-provider"></a>Развертывание поставщика ресурсов SQL

Когда вы установите все необходимые компоненты, запустите скрипт **DeploySqlProvider.ps1**, чтобы развернуть поставщик ресурсов SQL. Сценарий DeploySqlProvider.ps1 входит в состав двоичного файла поставщика ресурсов SQL, загруженного для вашей версии Azure Stack.

> [!IMPORTANT]
> Сценарий должен выполняться в системе Windows 10 или Windows Server 2016 с установленной последней версией среды выполнения .NET.

Чтобы развернуть поставщик ресурсов SQL, откройте **новую консоль** PowerShell с повышенными привилегиями и перейдите в каталог, в который ранее извлекли двоичные файлы поставщика ресурсов SQL. Рекомендуем открыть новое окно PowerShell, чтобы избежать потенциальных проблем, вызванных уже загруженными модулями PowerShell.

Запустите скрипт DeploySqlProvider.ps1, который выполняет следующие задачи:

- Передает сертификаты и другие артефакты в учетную запись хранения в Azure Stack.
- Публикует пакеты коллекции, что позволяет развертывать базы данных SQL с помощью коллекции.
- Публикует пакет коллекции для развертывания серверов размещения.
- Развертывает виртуальную машину с помощью загруженного образа ядра Windows Server 2016, а затем устанавливает поставщик ресурсов SQL.
- Регистрирует локальную запись DNS для сопоставления с виртуальной машиной поставщика ресурсов.
- Регистрирует поставщик ресурсов в локальном экземпляре Azure Resource Manager для оператора и пользователя.
- Может установить одно обновление Windows Server во время установки поставщика ресурсов.

> [!NOTE]
> Когда вы запустите развертывание поставщика ресурсов SQL, будет создана группа ресурсов **system.local.sqladapter**. На выполнение четырех обязательных развертываний в эту группу ресурсов может потребоваться до 75 минут.

### <a name="deploysqlproviderps1-parameters"></a>Параметры DeploySqlProvider.ps1

Следующие параметры можно указать в командной строке. Если вы не зададите нужные параметры или их значения не пройдут проверку, вам будет предложено указать необходимые параметры.

| Имя параметра | ОПИСАНИЕ | Комментарий или значение по умолчанию |
| --- | --- | --- |
| **CloudAdminCredential** | Учетные данные администратора облака, необходимые для доступа к привилегированной конечной точке. | _Обязательный_ |
| **AzCredential** | Учетные данные администратора службы Azure Stack. Используйте те же учетные данные, которые вы указали при развертывании Azure Stack. | _Обязательный_ |
| **VMLocalCredential** | Учетные данные локального администратора на виртуальной машине поставщика ресурсов SQL. | _Обязательный_ |
| **PrivilegedEndpoint** | IP-адрес или DNS-имя привилегированной конечной точки. |  _Обязательный_ |
| **DependencyFilesLocalPath** | В этот каталог нужно поместить и PFX-файл сертификата. | _Необязательный_ (_обязательно_ для интегрированных систем) |
| **DefaultSSLCertificatePassword** | Пароль для PFX-файла сертификата. | _Обязательный_ |
| **MaxRetryCount** | Количество повторов каждой операции в случае сбоя.| 2 |
| **RetryDuration** | Время ожидания между повторными попытками в секундах. | 120 |
| **Удаление** | Удаляет поставщик ресурсов и все связанные с ним ресурсы (см. примечания ниже). | Нет  |
| **DebugMode** | Отключает автоматическую очистку в случае сбоя. | Нет  |

>[!NOTE]
> Отображение номеров SKU на портале может занять до часа. Вы не можете создать базу данных до развертывания и запуска SKU.

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Развертывание поставщика ресурсов SQL с помощью пользовательского сценария

Чтобы избежать конфигурации вручную при развертывании поставщика ресурсов, настройте следующий скрипт. Измените сведения и пароли учетной записи по умолчанию для развертывания Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

После выполнения скрипта установки поставщика ресурсов обновите браузер, чтобы отобразить последние изменения.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Проверка развертывания с использованием портала Azure Stack

Выполните следующие действия, чтобы проверить успешное развертывания поставщика ресурсов SQL.

1. Войдите на портал администрирования в качестве администратора служб.
2. Выберите **Группы ресурсов**.
3. Выберите группу ресурсов **system.\<расположение\>.sqladapter**.
4. Под пунктом **Развертывания** на следующем экране должно отображаться **4 успешно**.

      ![Проверка развертывания поставщика ресурсов SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Подробные сведения о развертывании поставщика ресурсов вы найдете в разделе **ПАРАМЕТРЫ**. Выберите **Развертывания**, чтобы узнать о состоянии, метке времени и продолжительности для каждого развертывания.

## <a name="next-steps"></a>Дополнительная информация

[Добавление серверов размещения](azure-stack-sql-resource-provider-hosting-servers.md)
