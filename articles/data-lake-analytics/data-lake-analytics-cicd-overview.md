---
title: Настройка конвейера CI/CD для Azure Data Lake Analytics | Документация Майкрософт
description: Узнайте, как настроить непрерывную интеграцию и непрерывное развертывание для Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: c069bc2a6147a021ea9bdf37e2926d5c8f33281c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145015"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Настройка конвейера CI/CD для Azure Data Lake Analytics

В этом документе описано, как настроить конвейер CI/CD для заданий и баз данных U-SQL.

## <a name="cicd-for-u-sql-job"></a>CI/CD для задания U-SQL

Средства Azure Data Lake для Visual Studio предоставляют тип проекта U-SQL, который позволяет организовывать скрипты U-SQL. Использование проектов U-SQL для управления кодом U-SQL упрощает выполнение сценариев CI/CD.

## <a name="build-u-sql-project"></a>Создание проекта U-SQL

Проект U-SQL можно создать с помощью MSBuild, передав соответствующие параметры. Чтобы настроить процесс сборки для проектов U-SQL, сделайте следующее.

### <a name="project-migration"></a>Миграция проекта

Прежде чем настраивать задачу сборки для проекта U-SQL, убедитесь, что вы используете последнюю версию проекта U-SQL. Откройте файл проекта U-SQL в редакторе и проверьте наличие следующих элементов импорта:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

В противном случае вы можете перенести проект двумя способами:

- Вариант 1: заменить старый элемент импорта приведенным выше.
- Вариант 2: открыть старый проект в Средствах Azure Data Lake для Visual Studio версии выше 2.3.3000.0. Старый шаблон проекта будет автоматически обновлен до последней версии. Проект, созданный в версии выше 2.3.3000.0, непосредственно использует новый шаблон.

### <a name="get-nuget-package"></a>Получение пакета NuGet

MSBuild не предоставляет встроенную поддержку типа проектов U-SQL. Чтобы реализовать эту возможность, включите ссылку на решение в [пакет Microsoft.Azure.DataLake.USQL.SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), который добавляет необходимую языковую службу.

Чтобы добавить ссылку на пакет NuGet, в обозревателе решений щелкните решение правой кнопкой мыши и выберите **Управление пакетами NuGet**. Или можно добавить в папку решения файл с именем `packages.config`, а в него включить следующее содержимое.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>Управление ссылками на базу данных U-SQL

Если скрипты U-SQL в проекте U-SQL содержат инструкции запросов к объектам базы данных U-SQL, включая запросы к таблице U-SQL или ссылки на сборку, перед созданием такого проекта необходимо создать ссылку на соответствующий проект базы данных U-SQL с определением этих объектов.

См. дополнительные сведения о [проектах базы данных U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>Проект базы данных U-SQL в настоящее время находится в общедоступной предварительной версии. При наличии в проекте оператора DROP сборка завершается ошибкой. Скоро будет разрешено выполнение оператора DROP.
>

### <a name="build-u-sql-project-with-msbuild-command-line"></a>Создание проекта U-SQL с помощью командной строки MSBuild

Перенеся проект и получив пакет NuGet, вы можете вызвать стандартную командную строку MSBuild со следующими дополнительными аргументами для создания проекта U-SQL:

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Определение аргументов и значения:

* USQLSDKPath=<пакет Nuget U-SQL>\build\runtime — этот параметр ссылается на путь установки пакета NuGet для языковой службы U-SQL.
* USQLTargetType=Merge или SyntaxCheck:
    * Merge — режим слияния компилирует файлы с выделенным кодом, например CS, PY, R, встраивая пользовательскую библиотеку кода (например, двоичный файл DLL, а также код Python или R) в скрипт U-SQL.
    * SyntaxCheck — режим SyntaxCheck сначала объединяет файлы с выделенным кодом в скрипте U-SQL, а затем компилирует скрипт U-SQL для проверки кода.
* DataRoot=<DataRoot path> — папка DataRoot требуется только в режиме SyntaxCheck. Создавая скрипт в режиме SyntaxCheck, MSBuild проверяет в скрипте ссылки на объекты базы данных. Перед выполнением сборки настройте соответствующую локальную среду с объектами из базы данных U-SQL в папке DataRoot на компьютере сборки, на которые указывает ссылка. Вы также можете управлять этими зависимостями базы данных, используя [ссылки на проект базы данных U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Обратите внимание: MSBuild проверяет только ссылки на объекты базы данных, а не файлы.
* EnableDeployment=true или false — этот параметр указывает, возможно ли развернуть указанные базы данных U-SQL во время процесса построения. Если вы ссылаетесь на проект базы данных U-SQL и используете объекты базы данных в скрипте U-SQL, установите для этого параметра значение true.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Непрерывная интеграция с Visual Studio Team Services

Кроме командной строки пользователи могут также использовать задачу MSBuild или Visual Studio Build Tools для создания проектов U-SQL в Visual Studio Team Services. Чтобы настроить задачу сборки, проверьте следующее:

1.  Добавьте задачу восстановления NuGet, чтобы получить решение со ссылкой на пакет NuGet, включая `Azure.DataLake.USQL.SDK`. Так MSBuild сможет найти целевые объекты языка U-SQL. Если вы хотите использовать пример аргументов MSBuild непосредственно на шаге 2, задайте параметр **Дополнительно > Каталог назначения** в качестве `$(Build.SourcesDirectory)/packages`.

    ![Использование CI/CD в Azure Data Lake: настройка задачи MSBuild для проекта U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Использование CI/CD в Azure Data Lake: настройка задачи Nuget для проекта U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Указав аргументы MSBuild, вы можете задать аргументы в следующей задаче MSBuild или Visual Studio Build Tools, а также определить переменные для этих аргументов в определении сборки VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

    ![Использование CI/CD в Azure Data Lake: настройка переменных MSBuild для проекта U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>Выходные данные сборки проекта U-SQL

После запуска сборки все создаваемые в проекте U-SQL скрипты выводятся в ZIP-файл с именем `USQLProjectName.usqlpack`. Структура папок в проекте будет храниться в выходных данных сборки в формате ZIP.

>[!NOTE]
>
>Файл с выделенным кодом для каждого скрипта U-SQL будет объединен как встроенный оператор для выходных данных скрипта сборки.
>

## <a name="test-u-sql-script"></a>Тестирование скрипта U-SQL

Azure Data Lake предоставляет тестовые проекты для скрипта U-SQL и кода C# (UDO/UDAG/UDF):
* См. дополнительные сведения о [добавлении тестовых случаев для скрипта U-SQL и расширенного кода C#](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* См. дополнительные сведения о [выполнении тестовых случаев в Visual Studio Team Service](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>Развертывание задания U-SQL

Проверив код в ходе сборки и тестирования, вы можете отправить задания U-SQL непосредственно из Visual Studio Team Service с помощью **задания Azure PowerShell**. Вы также можете развернуть скрипт в Azure Data Lake Storage или хранилище BLOB-объектов, а затем [запустить запланированные задания в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>Отправка заданий U-SQL в Visual Studio Team Service

Выходные данные сборки проекта U-SQL — это ZIP-файл с именем **USQLProjectName.usqlpack**, который включает все скрипты U-SQL в проекте. Вы можете использовать [задачу Azure PowserShell в Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) со следующим примером скрипта PowserShell, чтобы отправить задания U-SQL непосредственно из сборки или конвейера сборки или выпуска Visual Studio Team Service.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Планирование заданий U-SQL в Фабрике данных Azure

Кроме отправки заданий U-SQL непосредственно из Visual Studio Team Service, вы также можете отправить встроенные скрипты в Azure Data Lake Storage или хранилище BLOB-объектов, а затем [запустить запланированные задания в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Используйте [задачу Azure PowerShell в Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) со следующим примером скрипта PowerShell, чтобы отправить скрипты U-SQL в учетную запись Azure Data Lake Storage.

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-u-sql-database"></a>CI/CD для базы данных U-SQL

Средства Azure Data Lake для Visual Studio предоставляют шаблон проекта базы данных U-SQL, который позволяет разработчикам быстро и легко выполнять разработку, развертывание и администрирование баз данных U-SQL. См. дополнительные сведения о [проектах базы данных U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>Создание проекта базы данных U-SQL

### <a name="get-nuget-package"></a>Получение пакета NuGet

MSBuild не предоставляет встроенную поддержку типа проектов базы данных U-SQL. Чтобы реализовать эту возможность, включите ссылку на решение в [пакет Microsoft.Azure.DataLake.USQL.SDK Nuget](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), который добавляет необходимую языковую службу.

Чтобы добавить ссылку на пакет NuGet, в обозревателе решений щелкните решение правой кнопкой мыши и выберите **Управление пакетами NuGet** для решения, а затем найдите и установите пакет NuGet. Или можно добавить в папку решения файл с именем packages.config и включить в него следующее содержимое.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>Создание проекта базы данных U-SQL с помощью командной строки MSBuild

Вы можете вызвать стандартную командную строку MSBuild и передать ссылку на пакет U-SQL SDK для NuGet со следующими дополнительными аргументами для создания проекта базы данных U-SQL:

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Аргумент `USQLSDKPath=<U-SQL Nuget package>\build\runtime` ссылается на путь установки пакета NuGet для языковой службы U-SQL.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Непрерывная интеграция с Visual Studio Team Services

Кроме командной строки пользователи могут также использовать **задачу MSBuild** или **Visual Studio Build Tools** для создания проектов базы данных U-SQL в Visual Studio Team Services. Чтобы настроить задачу сборки, проверьте следующее:

1.  Добавьте задачу восстановления NuGet, чтобы получить решение со ссылкой на пакет NuGet, включая `Azure.DataLake.USQL.SDK`. Так MSBuild сможет найти целевые объекты языка U-SQL. Если вы хотите использовать пример аргументов MSBuild непосредственно на шаге 2, задайте параметр **Дополнительно > Каталог назначения** в качестве `$(Build.SourcesDirectory)/packages`.

    ![Использование CI/CD в Azure Data Lake: настройка задачи MSBuild для проекта U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Использование CI/CD в Azure Data Lake: настройка задачи Nuget для проекта U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Указав аргументы MSBuild, вы можете задать аргументы в следующей задаче MSBuild или Visual Studio Build Tools, а также определить переменные для этих аргументов в определении сборки VSTS.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```

    ![Использование CI/CD в Azure Data Lake: настройка переменных MSBuild для проекта базы данных U-SQL](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>Выходные данные сборки проекта базы данных U-SQL

Выходные данные сборки для проекта базы данных U-SQL — это пакет развертывания базы данных U-SQL с именем, которое содержит суффикс `.usqldbpack`. Пакет `.usqldbpack` — это ZIP-файл, который объединяет все инструкции DDL в единый скрипт U-SQL в папке DDL, а также все библиотеки DLL и дополнительные файлы для сборок в папке Temp.

## <a name="test-table-valued-function-and-stored-procedure"></a>Тестирование функции с табличным значением и хранимой процедуры

Сейчас добавление тестовых случаев для функций с табличным значением и хранимых процедур не поддерживается. Но вы можете создать проект U-SQL со скриптами U-SQL, которые вызывают эти функций, а затем написать тестовые случаи для них. Чтобы настроить тестовые случаи для функций с табличным значением и хранимых процедур, определенных в проекте базы данных U-SQL, сделайте следующее:

1.  Создайте проект U-SQL для тестирования и напишите скрипты U-SQL, которые вызывают функции с табличным значением и хранимые процедуры.
2.  Добавьте ссылку на базу данных в этот проект U-SQL. Чтобы получить определение хранимой процедуры и функций с табличным значением, нужно создать ссылку на проект базы данных, который содержит инструкцию DDL. См. дополнительные сведения о [создании ссылок на базы данных](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Добавьте тестовые случаи для скриптов U-SQL, которые вызывают функции с табличным значением и хранимые процедуры. См. дополнительные сведения о [добавлении тестовых случаев для скриптов U-SQL](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>Развертывание базы данных U-SQL в Visual Studio Team Service

`PackageDeploymentTool.exe` предоставляет программный интерфейс и интерфейс командной строки, которые позволяют развернуть пакет развертывания базы данных U-SQL (.usqldbpack). Пакет SDK включен в [пакет U-SQL SDK для NuGet](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), размещенный в файле build/runtime/PackageDeploymentTool.exe. С помощью `PackageDeploymentTool.exe` можно развертывать базы данных U-SQL в Azure Data Lake Analytics и локальную учетную запись.

>[!NOTE]
>
>Поддержка командной строки PowerShell и задачи выпуска Visual Studio Team Service для развертывания базы данных U-SQL будет реализована позже.
>

Чтобы настроить задачу развертывания базы данных в Visual Studio Team Service, сделайте следующее:

1. Добавьте задачу скрипта PowerShell в конвейер сборки или выпуска, а затем выполните следующий скрипт PowerShell. Эта задача позволяет получить зависимости пакета Azure SDK для `PackageDeploymentTool.exe` и `PackageDeploymentTool.exe`. Можно задать параметры -AzureSDK и -DBDeploymentTool для загрузки зависимостей и средства развертывания для некоторых отдельных папок. Передайте путь -AzureSDK в `PackageDeploymentTool.exe` как параметр -AzureSDKPath на шаге 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL dabatase deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Добавьте **задачу командной строки** в конвейер сборки или выпуска, а затем создайте скрипт, который вызывает `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` расположен в определенной папке $DBDeploymentTool. Пример скрипта: 

    * Локальное развертывание базы данных U-SQL

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Используйте режим интерактивной аутентификации, чтобы развернуть базу данных U-SQL в учетной записи Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Используйте аутентификацию на основе секрета, чтобы развернуть базу данных U-SQL в учетной записи Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Используйте аутентификацию на основе файла сертификата, чтобы развернуть базу данных U-SQL в учетной записи Azure Data Lake Analytics:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**Описание параметра PackageDeploymentTool.exe:**

**Распространенные параметры:**

|Параметр|ОПИСАНИЕ|По умолчанию|Обязательно|
|---------|-----------|-------------|--------|
|Package|Путь к пакету развертывания развертываемой базы данных U-SQL|null|Да|
|База данных|Имя развертываемой или создаваемой базы данных|master|false|
|LogFile|Путь к файлу для ведения журнала по умолчанию для стандартного вывода на консоль|null|false|
|LogLevel|Уровень ведения журнала: подробный, обычный, предупреждение, ошибка|LogLevel.Normal|false|

**Параметр локального развертывания:**

|Параметр|ОПИСАНИЕ|По умолчанию|Обязательно|
|---------|-----------|-------------|--------|
|DataRoot|Путь к корневой папке локальных данных|null|Да|

**Параметр развертывания Azure Data Lake Analytics:**

|Параметр|ОПИСАНИЕ|По умолчанию|Обязательно|
|---------|-----------|-------------|--------|
|Учетная запись.|Определяет учетную запись Azure Data Lake Analytics для развертывания по ее имени|null|Да|
|ResourceGroup|Имя группы ресурсов Azure для учетной записи Azure Data Lake Analytics|null|Да|
|SubscriptionId|Идентификатор подписки Azure для учетной записи Azure Data Lake Analytics|null|Да|
|Клиент|Имя клиента (имя домена каталога AAD, которое можно найти на странице управления подпиской на портале Azure)|null|Да|
|AzureSDKPath|Путь для поиска зависимых сборок в пакете Azure SDK|null|Да|
|Interactive|Использование интерактивного режима для аутентификации|false|false|
|ClientId|Идентификатор приложения AAD для неинтерактивной аутентификации|null|Требуется для неинтерактивной аутентификации|
|Секрет|Секрет и пароль для неинтерактивной аутентификации (используется только в доверенной или безопасной среде)|null|Требуется для неинтерактивной аутентификации (иначе используется SecreteFile)|
|SecreteFile|Файл с секретом и паролем для неинтерактивной аутентификации (доступ для чтения должен быть разрешен только текущему пользователю)|null|Требуется для неинтерактивной аутентификации (иначе используется Secrete)|
|CertFile|Файл в сертификатом X.509 для неинтерактивной аутентификации (по умолчанию используется аутентификация на основе секрета клиента)|null|false|
|JobPrefix|Префикс для развертывания задания DDL U-SQL развертывания базы данных|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>Дальнейшие действия

- [Тестирование кода Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
- [Запуск скриптов U-SQL на локальном компьютере](data-lake-analytics-data-lake-tools-local-run.md)
- [Использование проекта базы данных U-SQL для разработки базы данных U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
