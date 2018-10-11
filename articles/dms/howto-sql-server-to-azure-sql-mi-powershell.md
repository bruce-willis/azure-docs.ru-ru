---
title: Использование модуля Azure Database Migration Service в Microsoft Azure PowerShell для переноса данных из локального экземпляра SQL Server в Управляемый экземпляр Базы данных SQL Azure | Документация Майкрософт
description: Сведения о переносе из локального экземпляра SQL Server в Управляемый экземпляр Базы данных SQL Azure с помощью Azure PowerShell.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 6760d2832b6c39759f9287797363fcd5a116ccfb
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883674"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>Перенос данных из локального экземпляра SQL Server в базу данных SQL Azure с помощью Azure PowerShell
В этой статье выполняется перенос базы данных **Adventureworks2012**, восстановленной на локальном экземпляре SQL Server 2005 (или более поздней версии), в базу данных SQL Azure с помощью Microsoft Azure PowerShell. Вы можете выполнить перенос баз данных из локального экземпляра SQL Server в базу данных SQL Azure с помощью модуля `AzureRM.DataMigration` в Microsoft Azure PowerShell.

В этой статье раскрываются следующие темы:
> [!div class="checklist"]
> * Создайте группу ресурсов.
> * создание экземпляра Azure Database Migration Service;
> * создание проекта миграции в экземпляре Azure Database Migration Service;
> * выполнение миграции.

## <a name="prerequisites"></a>Предварительные требования
Для выполнения этих действий вам потребуется следующее:

- [SQL Server 2016 или более поздней версии](https://www.microsoft.com/sql-server/sql-server-downloads) (любой выпуск).
- При установке SQL Server Express включите протокол TCP/IP, который отключен по умолчанию. Для этого выполните инструкции в разделе [Использование диспетчера конфигурации SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) статьи "Включение или отключение сетевого протокола сервера".
- [Настройте брандмауэр Windows для доступа к ядру СУБД](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Экземпляр базы данных SQL Azure. Экземпляр базы данных SQL Azure можно создать, ознакомившись со сведениями в статье [Создание базы данных SQL Azure на портале Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal).
- [Помощник по миграции данных](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 или более поздней версии.
- Создайте виртуальную сеть с помощью модели развертывания Azure Resource Manager, которая предоставляет Azure Database Migration Service возможность установки подключения типа "сеть — сеть" к локальным исходным серверам с помощью [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) или [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Выполните оценку миграции локальной базы данных и схемы с использованием Помощника по миграции данных, как описано в статье [Выполнение оценки миграции SQL Server с помощью Data Migration Assistant](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem).
- Скачайте и установите модуль AzureRM.DataMigration из коллекции PowerShell с помощью [командлета PowerShell Install-Module](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1).
- Убедитесь, что учетные данные, используемые для подключения к исходному экземпляру SQL Server, имеют разрешение [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Убедитесь, что учетные данные, используемые для подключения к целевому экземпляру базы данных SQL Azure, имеют разрешение на управление базами данных в целевой Базе данных SQL Azure.
- Подписка Azure. Если у вас еще нет подписки Azure, создайте [бесплатную учетную](https://azure.microsoft.com/free/) запись, прежде чем начать работу.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Вход в подписку Microsoft Azure
Чтобы войти в подписку Azure с помощью PowerShell, следуйте инструкциям в [этой статье](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1).

## <a name="create-a-resource-group"></a>Создание группы ресурсов
Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. Прежде чем создать виртуальную машину, создайте группу ресурсов.

Создайте группу ресурсов, выполнив команду [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1). 

В следующем примере создается группа ресурсов с именем *myResourceGroup* в регионе *EastUS*.

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>Создание экземпляра Azure Database Migration Service 
Вы можете создать экземпляр Azure Database Migration Service, выполнив командлет `New-AzureRmDataMigrationService`. Для этого командлета нужно передать следующие обязательные параметры:
- *ResourceGroupName.* Вы можете выполнить команду [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1), чтобы создать группу ресурсов Azure, как описано в предыдущем разделе, и указать ее имя в качестве параметра.
- *ServiceName.* Строка, соответствующая требуемому уникальному имени службы для Azure Database Migration Service. 
- *Расположение.* Указывает расположение службы. Укажите расположение центра обработки данных Azure, например западная часть США или Юго-Восточная Азия.
- *Sku.* Этот параметр соответствует имени SKU DMS. Сейчас поддерживаются такие имена SKU: *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
- *VirtualSubnetId.* Вы можете создать подсеть, выполнив командлет [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1). 

В указанном ниже примере создается служба с именем *MyDMS* в группе ресурсов *MyDMSResourceGroup*, которая расположена в регионе *Восточная часть США*, с использованием виртуальной сети с именем *MyVNET* и подсети с именем *MySubnet*.

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Создание проекта миграции
Создав экземпляр Azure Database Migration Service, создайте проект миграции. Для проекта Azure Database Migration Service необходимо иметь сведения о подключении для исходных и целевых экземпляров, а также список баз данных, которые требуется перенести как часть проекта.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Создание объекта сведений о подключении к базе данных для исходных и целевых подключений
Объект сведений о подключении к базе данных можно создать, выполнив командлет `New-AzureRmDmsConnInfo`. Для этого командлета требуются следующие параметры:
- *ServerType.* Тип запрошенного подключения к базе данных, например SQL, Oracle или MySQL. Используйте SQL для SQL Server и SQL Azure.
- *DataSource.* Имя или IP-адрес экземпляра SQL Server или Базы данных SQL Azure.
- *AuthType.* Тип проверки подлинности для подключения. Это может быть SqlAuthentication или WindowsAuthentication.
- Параметр *TrustServerCertificate* задает значение, указывающее, будет ли шифроваться канал при проходе по цепочке сертификатов для проверки доверия. Значение может быть true или false.

В приведенном ниже примере создается объект сведений о подключении для исходного SQL Server MySourceSQLServer с использованием проверки подлинности SQL: 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

В приведенном ниже примере создается объект сведений о подключении для сервера Управляемого экземпляра Базы данных SQL Azure с именем targetmanagedinstance.database.windows.net с использованием проверки подлинности SQL:

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Указание баз данных для проекта миграции
Создайте список объектов `AzureRmDataMigrationDatabaseInfo`, в котором указаны базы данных, переносимые в рамках проекта миграции баз данных Azure. Его можно предоставить в виде параметра для создания проекта. Объекты AzureRmDataMigrationDatabaseInfo можно создать с помощью командлета `New-AzureRmDataMigrationDatabaseInfo`. 

В следующем примере создается проект `AzureRmDataMigrationDatabaseInfo` для базы данных **AdventureWorks**, который добавляется в список, чтобы его можно было предоставить в виде параметра для создания проекта.

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Создание объекта проекта
Наконец, можно создать проект миграции баз данных Azure с именем *MyDMSProject*, который расположен в *восточной части США*, выполнив команду `New-AzureRmDataMigrationProject` и добавив раннее созданные исходные и целевые подключения и список баз данных для миграции.

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Создание и запуск задачи миграции
Наконец, создайте и запустите задачу миграции баз данных Azure. Помимо информации, предоставленной с проектом, созданным в качестве предварительного условия, для задачи миграции баз данных Azure необходимо иметь учетные данные для исходного и целевого подключения, а также список таблиц базы данных, которые необходимо перенести. 

### <a name="create-credential-parameters-for-source-and-target"></a>Создание параметров учетных данных для исходного и целевого подключения
Учетные данные безопасности подключения можно создать в качестве объекта [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0). 

В следующем примере показано создание объектов *PSCredential* для исходного и целевого подключения. Пароли представлены в качестве строковых переменных *$sourcePassword* и *$targetPassword*. 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>Создание объекта файлового ресурса для резервного копирования
Теперь с помощью командлета New-AzureRmDmsFileShare создайте объект FileShare, представляющий локальную сетевую папку SMB, в которую Azure Database Migration Service может поместить резервные копии базы данных-источника.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzureRmDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Создание объекта выбранной базы данных
Теперь необходимо выбрать исходную и целевую базы данных с помощью командлета New-AzureRmDmsSelectedDB, как показано в следующем примере:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>Универсальный код ресурса (URI) SAS для контейнера службы хранилища Azure
Создайте переменную, содержащую URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>Выберите имена для входа
Создайте список имен для входа, которые нужно перенести, как показано ниже. Примечание. В настоящее время DMS поддерживает перенос только имен для входа SQL. 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>Выбор заданий агентов
Создайте список заданий агентов, которые будут перенесены, как показано в следующем примере:

>[!NOTE]
>В настоящее время DMS поддерживает только задания подсистемы T-SQL.

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>Создание и запуск задачи миграции

Выполните командлет `New-AzureRmDataMigrationTask`, чтобы создать и запустить задачу миграции. Для этого командлета требуются следующие параметры:
- *TaskType.* Для создаваемой задачи миграции для типа миграции "из SQL Server в Управляемый экземпляр Базы данных SQL Azure" ожидается тип *MigrateSqlServerSqlDbMi*. 
- *ResourceGroupName.* Имя группы ресурсов Azure, в которой будет создана задача.
- *ServiceName.* Экземпляр Azure Database Migration Service, в котором будет создана задача.
- *ProjectName.* Имя проекта Azure Database Migration Service, в котором будет создана задача. 
- *TaskName.* Имя задачи, которая будет создана. 
- *SourceConnection*. Объект AzureRmDmsConnInfo, представляющий соединение с источником SQL Server.
- *TargetConnection.* Объект AzureRmDmsConnInfo, представляющий подключение конечного Управляемого экземпляра Базы данных SQL Azure.
- *SourceCred.* Объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) для подключения к исходному серверу.
- *TargetCred.* Объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) для подключения к целевому серверу.
- *SelectedDatabase.* Объект AzureRmDataMigrationSelectedDB, представляющий сопоставление исходной и целевой баз данных.
- *BackupFileShare.* Объект FileShare, представляющий локальную сетевую папку, в которую Azure Database Migration Service может поместить резервные копии базы данных-источника.
- *BackupBlobSasUri.* URI SAS, который позволяет Azure Database Migration Service обращаться к контейнеру учетной записи хранения для отправки файлов резервных копий. Узнайте, как получить URI SAS для контейнера больших двоичных объектов.
- *SelectedLogins.* Список выбранных для переноса имен для входа.
- *SelectedAgentJobs.* Список выбранных заданий агента для переноса.

В следующем примере создается и запускается задача миграции с именем myDMSTask:

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

## <a name="monitor-the-migration"></a>Мониторинг миграции
Вы можете отслеживать выполняемую задачу миграции, запросив свойство состояния задачи, как показано в следующем примере:

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>Дополнительная информация
- Просмотрите [руководство по миграции базы данных Майкрософт](https://datamigration.microsoft.com/).