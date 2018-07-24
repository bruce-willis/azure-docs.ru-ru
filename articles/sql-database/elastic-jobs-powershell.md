---
title: Создание агента заданий обработки эластичных баз данных SQL Azure с помощью PowerShell | Документация Майкрософт
description: Дополнительные сведения о создании агента заданий обработки эластичных баз данных с помощью PowerShell.
services: sql-database
author: johnpaulkee
manager: craigg
ms.service: sql-database
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: joke
ms.openlocfilehash: 2cc8db0ce849e0f0d376824665aac7dbc2af29db
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39035223"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Создание агента заданий обработки эластичных баз данных с помощью PowerShell

[Задания обработки эластичных баз данных](elastic-jobs-overview.md) позволяют выполнять скрипты Transact-SQL (T-SQL) в нескольких базах данных одновременно.

В рамках этого руководства вы узнаете, что необходимо сделать для выполнения запроса в нескольких базах данных:

> [!div class="checklist"]
> * создание агента заданий обработки эластичных баз данных;
> * создание учетных данных заданий для выполнения скриптов на целевых объектах с помощью этих заданий;
> * определение целевых объектов (серверы, эластичные пулы, базы данных, карты сегментов), для которых следует выполнить задание;
> * создание учетных данных для базы данных в целевых базах данных для подключения агента и выполнения заданий;
> * создать задание;
> * добавление шагов задания;
> * запуск выполнения задания;
> * мониторинг задания.

## <a name="prerequisites"></a>Предварительные требования

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

Установите модуль **AzureRM.Sql** 4.8.1-preview, чтобы получать актуальные командлеты заданий обработки эластичных баз данных. Выполните приведенные ниже команды в PowerShell с правами администратора.

```powershell
# Installs the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -RequiredVersion 4.8.1-preview -Force

# Import the AzureRM.Sql 4.8.1 module
Import-Module AzureRM.Sql -RequiredVersion 4.8.1

# Confirm if module successfully imported - if the imported version is 4.8.1, then continue
Get-Module AzureRM.Sql
```

## <a name="create-required-resources"></a>Создание необходимых ресурсов

Для создания агента заданий обработки эластичных баз данных требуется база данных (S0 или более поздней версии). Она будет использоваться в качестве [базы данных заданий](elastic-jobs-overview.md#job-database). 

*Следующий скрипт позволяет создать группу ресурсов, сервер и базу данных, которые можно использовать в базе данных заданий. Следующий скрипт также создает второй сервер с двумя пустыми базами данных для выполнения заданий.*

Для задания обработки эластичных баз данных нет особых требований к именованию, поэтому можно использовать любые соглашения об именовании (при условии, что они соответствуют всем [требованиям Azure](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)).

```powershell
# Sign in to your Azure account
Connect-AzureRmAccount

# Create a resource group
Write-Output "Creating a resource group..."
$ResourceGroupName = Read-Host "Please enter a resource group name"
$Location = Read-Host "Please enter an Azure Region"
$Rg = New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
$Rg

# Create a server
Write-Output "Creating a server..."
$AgentServerName = Read-Host "Please enter an agent server name"
$AgentServerName = $AgentServerName + "-" + [guid]::NewGuid()
$AdminLogin = Read-Host "Please enter the server admin name"
$AdminPassword = Read-Host "Please enter the server admin password"
$AdminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$AdminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AdminLogin, $AdminPasswordSecure
$AgentServer = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $AgentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$AgentServer | New-AzureRmSqlServerFirewallRule -AllowAllAzureIPs
$AgentServer

# Create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$JobDatabaseName = "JobDatabase"
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $JobDatabaseName -RequestedServiceObjectiveName "S0"
$JobDatabase
```

```powershell
# Create a target server and some sample databases - uses the same admin credential as the agent server just for simplicity
Write-Output "Creating target server..."
$TargetServerName = Read-Host "Please enter a target server name"
$TargetServerName = $TargetServerName + "-" + [guid]::NewGuid()
$TargetServer = New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName -Location $Location -ServerName $TargetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($AdminCred)

# Set target server firewall rules to allow all Azure IPs
$TargetServer | New-AzureRmSqlServerFirewallRule -AllowAllAzureIPs
$TargetServer | New-AzureRmSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$TargetServer

# Create some sample databases to execute jobs against...
$Db1 = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb1"
$Db1
$Db2 = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $TargetServerName -DatabaseName "TargetDb2"
$Db2
```

## <a name="enable-the-elastic-jobs-preview-for-your-subscription"></a>Включение предварительной версии заданий обработки эластичных баз данных для подписки

Для использования заданий обработки эластичных баз данных следует зарегистрировать компонент в подписке Azure, выполнив следующую команду (ее необходимо запустить один раз в каждой подписке, в которой необходимо применять задания):

```powershell
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

## <a name="create-the-elastic-job-agent"></a>Создание агента заданий обработки эластичных баз данных

Агент заданий обработки эластичных баз данных является ресурсом Azure для создания, запуска заданий и управления ими. Агент выполняет запланированные или разовые задания.

Командлет **New-AzureRmSqlElasticJobAgent** требует наличия базы данных SQL Azure, поэтому параметры *ResourceGroupName*, *ServerName* и *DatabaseName* должны указывать на имеющиеся ресурсы.

```powershell
Write-Output "Creating job agent..."
$AgentName = Read-Host "Please enter a name for your new Elastic Job agent"
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $AgentName
$JobAgent
```

## <a name="create-job-credentials-so-that-jobs-can-execute-scripts-on-its-targets"></a>Создание учетных данных заданий для выполнения скриптов на целевых объектах с помощью этих заданий

Задания используют учетные данные для базы данных, чтобы подключаться к целевым базам данных, указанным целевой группой при выполнении. Эти учетные данные для баз данных также используются для подключения к базе данных master. Это позволяет перечислить все базы данных сервера или эластичного пула, когда они используются как тип элемента целевой группы.

Учетные данные для базы данных должны быть созданы в базе данных заданий.  
Для успешного завершения задания все целевые базы данных должны иметь имя для входа с достаточными полномочиями.

![Учетные данные заданий обработки эластичных баз данных](media/elastic-jobs-overview/job-credentials.png)

Помимо учетных данных в образе обратите внимание на добавление команд *GRANT* в следующем скрипте. Эти разрешения являются обязательными для скрипта, выбранного в этом примере задания. Так как в этом примере создается таблица в целевых базах данных, каждой из этих баз данных необходимо предоставить соответствующие разрешения для успешного выполнения.

Чтобы создать требуемые учетные данные задания (в базе данных заданий), выполните следующий скрипт:

```powershell
# In the master database (target server)
# - Create the master user login
# - Create the master user from master user login
# - Create the job user login
$Params = @{
  'Database' = 'master'
  'ServerInstance' =  $TargetServer.ServerName + '.database.windows.net'
  'Username' = $AdminLogin
  'Password' = $AdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @Params
$Params.Query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @Params
$Params.Query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @Params

# For each of the target databases
# - Create the jobuser from jobuser login
# - Make sure they have the right permissions for successful script execution
$TargetDatabases = @( $Db1.DatabaseName, $Db2.DatabaseName )
$CreateJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$GrantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$GrantCreateScript = "GRANT CREATE TABLE TO jobuser"

$TargetDatabases | % {
  $Params.Database = $_

  $Params.Query = $CreateJobUserScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantAlterSchemaScript
  Invoke-SqlCmd @Params

  $Params.Query = $GrantCreateScript
  Invoke-SqlCmd @Params
}

# Create job credential in Job database for master user
Write-Output "Creating job credentials..."
$LoginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$MasterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $LoginPasswordSecure
$MasterCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "masteruser" -Credential $MasterCred

$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $LoginPasswordSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "jobuser" -Credential $JobCred
```

## <a name="define-the-target-databases-you-want-to-run-the-job-against"></a>Определение целевых баз данных, для которых нужно запустить задание

[Целевая группа](elastic-jobs-overview.md#target-group) определяет набор из одной или нескольких баз данных, в которых будет выполняться шаг задания. 

Следующий фрагмент кода позволяет создать две целевые группы: *ServerGroup* и *ServerGroupExcludingDb2*. *ServerGroup* охватывает все базы данных, имеющиеся на сервере во время выполнения, а *ServerGroupExcludingDb2* охватывает все базы данных сервера, за исключением *TargetDb2*:

```powershell
Write-Output "Creating test target groups..."
# Create ServerGroup target group
$ServerGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name 'ServerGroup'
$ServerGroup | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName

# Create ServerGroup with an exclusion of Db2
$ServerGroupExcludingDb2 = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$ServerGroupExcludingDb2 | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -RefreshCredentialName $MasterCred.CredentialName
$ServerGroupExcludingDb2 | Add-AzureRmSqlElasticJobTarget -ServerName $TargetServerName -Database $Db2.DatabaseName -Exclude
```

## <a name="create-a-job"></a>создать задание;

```powershell
Write-Output "Creating a new job"
$JobName = "Job1"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job
```

## <a name="create-a-job-step"></a>Шаг создания задания

В этом примере определяется два шага для выполнения задания. Первый шаг задания (*step1*) позволяет создать таблицу (*Step1Table*) в каждой базе данных целевой группы *ServerGroup*. Второй шаг задания (*step2*) позволяет создать таблицу (*Step2Table*) в каждой базе данных, за исключением *TargetDb2*, так как целевая группа, [определенная ранее](#define-the-target-databases-you-want-to-run-the-job-against), предназначена для исключения этой базы данных.

```powershell
Write-Output "Creating job steps"
$SqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$SqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$Job | Add-AzureRmSqlElasticJobStep -Name "step1" -TargetGroupName $ServerGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText1
$Job | Add-AzureRmSqlElasticJobStep -Name "step2" -TargetGroupName $ServerGroupExcludingDb2.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText2
```


## <a name="run-the-job"></a>Выполнение задания

Для немедленного запуска задания выполните следующую команду:

```powershell
Write-Output "Start a new execution of the job..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
```

После успешного завершения команды появятся две новые таблицы в TargetDb1 и только одна новая таблица в TargetDb2:


   ![проверка новых таблиц в SSMS](media/elastic-jobs-overview/job-execution-verification.png)




## <a name="monitor-status-of-job-executions"></a>Мониторинг состояния выполнения задания

С помощью следующих фрагментов кода можно получить сведения о выполнении задания:

```powershell
# Get the latest 10 executions run
$JobAgent | Get-AzureRmSqlElasticJobExecution -Count 10

# Get the job step execution details
$JobExecution | Get-AzureRmSqlElasticJobStepExecution

# Get the job target execution details
$JobExecution | Get-AzureRmSqlElasticJobTargetExecution -Count 2
```

## <a name="schedule-the-job-to-run-later"></a>Планирование более позднего выполнения задания

Чтобы запланировать выполнение задания в определенное время, выполните следующую команду:

```powershell
# Run every hour starting from now
$Job | Set-AzureRmSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ресурсы Azure, созданные в рамках этого руководства, удалив группу ресурсов.

> [!TIP]
> Если вы планируете продолжить работу с этими событиями, не очищайте ресурсы, созданные при работе с этой статьей. Если вы не планируете продолжать работу, следуйте инструкциям ниже, чтобы удалить все созданные в рамках этой статьи ресурсы.
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $ResourceGroupName
```


## <a name="next-steps"></a>Дополнительная информация

В рамках этого руководства вы выполняли сценарий Transact-SQL для набора баз данных.  Вы узнали, как выполнять следующие задачи:

> [!div class="checklist"]
> * создание агента заданий обработки эластичных баз данных;
> * создание учетных данных заданий для выполнения скриптов на целевых объектах с помощью этих заданий;
> * определение целевых объектов (серверы, эластичные пулы, базы данных, карты сегментов), для которых следует выполнить задание;
> * создание учетных данных для базы данных в целевых базах данных для подключения агента и выполнения заданий;
> * создать задание;
> * Добавление шага задания.
> * Запуск выполнения задания.
> * Отслеживание задания

> [!div class="nextstepaction"]
>[Use Transact-SQL (T-SQL) to create and manage Elastic Database Jobs](elastic-jobs-tsql.md) (Создание заданий обработки эластичных баз данных и управление ими с помощью Transact-SQL (T-SQL))