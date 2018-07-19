---
title: Развертывание службы синхронизации файлов Azure (предварительная версия) | Документация Майкрософт
description: Сведения о развертывании службы синхронизации файлов Azure от начала до конца.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: wgries
ms.openlocfilehash: 808bc3908790c8d6dedf1d9f00a4c70b42c7c490
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37867075"
---
# <a name="deploy-azure-file-sync-preview"></a>Развертывание службы синхронизации файлов Azure (предварительная версия)
С помощью службы синхронизации файлов Azure (предварительная версия) вы можете централизованно хранить файловые ресурсы организации в службе файлов Azure, обеспечивая гибкость, производительность и совместимость локального файлового сервера. Это достигается путем преобразования Windows Server в быстрый кэш общего файлового ресурса Azure. Для локального доступа к данным вы можете использовать любой протокол, доступный в Windows Server, в том числе SMB, NFS и FTPS. Кроме того, вы можете создать любое количество кэшей в любом регионе.

Перед выполнением шагов, описанных в этом руководстве, настоятельно рекомендуем ознакомиться со статьями [Планирование развертывания службы файлов Azure](storage-files-planning.md) и [Планирование развертывания службы синхронизации файлов Azure (предварительная версия)](storage-sync-files-planning.md).

## <a name="prerequisites"></a>предварительным требованиям
* Учетная запись хранения и файловые ресурсы Azure в том регионе, в котором нужно развернуть службу синхронизации файлов Azure. Дополнительные сведения можно найти в разделе 
    - [Доступность по регионам](storage-sync-files-planning.md#region-availability). Здесь представлены сведения о регионах службы синхронизации файлов Azure.
    - Пошаговые инструкции по созданию учетной записи хранения см. в разделе о [создании учетной записи хранения](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
    - [Создание общей папки в хранилище файлов Azure](storage-how-to-create-file-share.md). Здесь содержатся пошаговые инструкции по созданию общего файлового ресурса.
* По крайней мере один поддерживаемый экземпляр Windows Server или кластер Windows Server для синхронизации со службой синхронизации файлов Azure. Дополнительные сведения о поддерживаемых версиях Windows Server см. в разделе [Взаимодействие службы синхронизации файлов Azure](storage-sync-files-planning.md#azure-file-sync-interoperability).
* На сервере Windows Server установите PowerShell версии 5.1. Если используется Windows Server 2012 R2, у вас должна быть установлена среда PowerShell версии не менее 5.1.\*. Эту проверку на наличие Windows Server 2016 можно спокойно пропустить, поскольку PowerShell 5.1 является стандартной версией по умолчанию. В Windows Server 2012 R2 можно проверить, установлена ли среда PowerShell 5.1.\*, просмотрев значение свойства **PSVersion** объекта **$PSVersionTable**.

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Если значение PSVersion меньше 5.1.\*, как это будет в случае с большинством последних установок Windows Server 2012 R2, версию можно легко обновить, скачав и установив [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Соответствующий пакет скачивания и установки для Windows Server 2012 R2 — это **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > На Windows Server 2012 R2 или Windows Server 2016 синхронизация файлов Azure еще не поддерживает PowerShell 6.
* Модуль AzureRM PowerShell на серверах, которые вы хотели бы использовать с функцией "Синхронизация файлов Azure". Дополнительные сведения об установке модулей AzureRM PowerShell см. в статье [Install Azure PowerShell with PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) (Установка Azure PowerShell с помощью PowerShellGet). Мы рекомендуем всегда использовать последнюю версию модулей Azure PowerShell. 

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Подготовка сервера Windows к работе со службой синхронизации файлов Azure
Для каждого сервера, который вы собираетесь использовать с функцией "Синхронизация файлов Azure", включая каждый узел сервера в отказоустойчивом кластере, отключите **конфигурацию усиленной безопасности Internet Explorer**. Это необходимо только при начальной регистрации на сервере. Конфигурацию можно включить повторно после регистрации сервера.

# <a name="portaltabportal"></a>[Портал](#tab/portal)
1. Откройте диспетчер сервера.
2. Щелкните **Локальный сервер**:  
    ![Элемент "Локальный сервер" в левой части пользовательского интерфейса диспетчера сервера](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Перейдите по ссылке **Конфигурация усиленной безопасности Internet Explorer** на вложенной панели **Свойства**.  
    ![Панель "Конфигурация усиленной безопасности Internet Explorer" в пользовательском интерфейсе диспетчера сервера](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. В диалоговом окне **Конфигурация усиленной безопасности Internet Explorer** установите переключатель **Выкл.** в разделе **Администраторы** и **Пользователи**.  
    ![Всплывающее окно "Конфигурация усиленной безопасности Internet Explorer" с установленным переключателем "Выкл."](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Чтобы отключить конфигурацию усиленной безопасности Internet Explorer, выполните следующую команду PowerShell с повышенными правами:

```PowerShell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="install-the-azure-file-sync-agent"></a>Установка агента службы синхронизации файлов Azure
Агент службы синхронизации файлов Azure — это скачиваемый пакет, позволяющий синхронизировать Windows Server с общим файловым ресурсом Azure. 

# <a name="portaltabportal"></a>[Портал](#tab/portal)
Его можно скачать в [Центре загрузки Майкрософт](https://go.microsoft.com/fwlink/?linkid=858257). После скачивания дважды щелкните пакет MSI, чтобы начать установку агента службы синхронизации файлов Azure.

> [!Important]  
> Если службу синхронизации файлов Azure нужно использовать с отказоустойчивым кластером, агент службы синхронизации файлов Azure должен быть установлен на каждом узле в кластере.

Таким образом, мы рекомендуем сделать следующее:
- Оставить путь установки по умолчанию (C:\Program Files\Azure\StorageSyncAgent), чтобы упростить устранение неполадок и обслуживание сервера.
- Включить Центр обновления Майкрософт, чтобы служба синхронизации файлов Azure поддерживалась в актуальном состоянии. Все обновления для агента службы синхронизации файлов Azure, включая обновления компонентов и исправления, будут выполняться из Центра обновления Майкрософт. Мы рекомендуем установить последнее обновление для службы синхронизации файлов Azure. Дополнительные сведения см. в разделе [Политика обновления агента службы синхронизации файлов Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

По завершении установки агента службы синхронизации файлов Azure пользовательский интерфейс регистрации сервера запустится автоматически. Перед регистрацией установите службу синхронизации хранилища. В разделе ниже ознакомьтесь со сведениями о создании службы синхронизации хранилища.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Выполните следующий код PowerShell, чтобы загрузить соответствующую версию агента Синхронизации файлов Azure для своей ОС и установите ее в своей системе.

```PowerShell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875004 `
        -OutFile "StorageSyncAgent.exe" 
}
elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875002 `
        -OutFile "StorageSyncAgent.exe" 
}
else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2 and Windows Server 2016")
}

# Extract the MSI from the install package
$tempFolder = New-Item -Path "afstemp" -ItemType Directory
Start-Process -FilePath ".\StorageSyncAgent.exe" -ArgumentList "/C /T:$tempFolder" -Wait

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "$($tempFolder.FullName)\StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.exe", ".\afstemp" -Recurse -Force
```

---

## <a name="deploy-the-storage-sync-service"></a>Развертывание службы синхронизации хранилища 
Развертывание функции "Синхронизация файлов Azure" начинается с размещения ресурса **службы синхронизации хранилища** в группе ресурсов выбранной вами подписки. Мы рекомендуем выполнять подготовку по мере необходимости. Вы создадите отношение доверия между серверами и этим ресурсом, и сервер можно будет зарегистрировать только в одной службе синхронизации хранилища. В результате рекомендуется развертывать столько служб синхронизации хранилища, сколько необходимо для разделения групп серверов. Имейте в виду, что серверы из разных служб синхронизации хранилища не могут синхронизироваться друг с другом.

> [!Note]
> Служба синхронизации хранилища унаследовала разрешения на доступ от подписки и группы ресурсов, в которую она была развернута. Рекомендуется тщательно проверить, кому предоставлен доступ. Сущности с доступом на запись могут начать синхронизацию новых наборов файлов с серверов, зарегистрированных в этой службе синхронизации хранилища, и вызвать отправку потока данных в доступное для них хранилище Azure.

# <a name="portaltabportal"></a>[Портал](#tab/portal)
Чтобы развернуть службу синхронизации хранилища, перейдите на [портал Azure](https://portal.azure.com/), нажмите *Создать* и найдите службу "Синхронизация файлов Azure". В результатах поиска выберите **Служба синхронизации файлов Azure (предварительная версия)**, а затем щелкните **Создать**, чтобы открыть вкладку **Развертывание службы синхронизации хранилища**.

В открывшейся области введите следующие сведения:

- **Имя.** Уникальное имя (в подписке) для службы синхронизации хранилища.
- **Подписка.** Подписка, в которой будет создана служба синхронизации хранилища. В зависимости от стратегии конфигурации организации можно получить доступ к одной или нескольким подпискам. Подписка Azure — это базовый контейнер при выставлении счетов для каждой облачной службы (такой как "Файлы Azure").
- **Группа ресурсов.** Это логическая группа ресурсов Azure, таких как учетная запись хранения или служба синхронизации хранилища. Для службы синхронизации файлов Azure можно создать группу ресурсов или использовать имеющуюся. (Мы рекомендуем использовать группы ресурсов в качестве контейнеров, чтобы логически выделить ресурсы для вашей организации, таких как группировка ресурсов отдела кадров или ресурсов для конкретного проекта.)
- **Расположение.** Регион, в котором будет развернута служба синхронизации файлов Azure. В этом списке доступны только поддерживаемые регионы.

По завершении выберите **Создать**, чтобы развернуть службу синхронизации хранилища.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Перед взаимодействием с командлетами управления функции "Синхронизация файлов Azure" вам необходимо будет импортировать библиотеку DLL и создать контекст управления этой функции. Это необходимо, так как командлеты управления функции "Синхронизация файлов Azure" еще не входят в модуль AzureRM PowerShell.

> [!Note]  
> Пакет StorageSync.Management.PowerShell.Cmdlets.dll, содержащий командлеты управления функции "Синхронизация файлов Azure" (намеренно), содержит командлет с неутвержденной командой (`Login`). Имя `Login-AzureRmStorageSync` было выбрано в соответствии с псевдонимом командлета `Login-AzureRmAccount` в модуле AzureRM PowerShell. Это сообщение об ошибке (и командлет) будут удалены, а агент Синхронизации файлов Azure будет добавлен в модуль AzureRM PowerShell.

```PowerShell
$acctInfo = Login-AzureRmAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzureRMAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzureRmLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzureRmResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzureRmResourceGroup -Name $resourceGroup -Location $region
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

После создания контекста Синхронизации файлов Azure с командлетом `Login-AzureRmStorageSync` можно создать службу синхронизации хранилища. Обязательно замените `<my-storage-sync-service>` нужным именем службы синхронизации хранилища.

```PowerShell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Регистрация Windows Server в службе синхронизации хранилища
При регистрации Windows Server в службе синхронизации хранилища между сервером (или кластером) и службой синхронизации хранилища устанавливаются отношения доверия. Сервер можно зарегистрировать только в одной службе синхронизации хранилища, и он может синхронизироваться с другими серверами и файловыми ресурсами Azure, связанными с той же службой синхронизации хранилища.

> [!Note]
> При регистрации сервера используются ваши учетные данные Azure для создания отношения доверия между службой синхронизации хранилища и сервером Windows Server, однако впоследствии сервер создает и использует собственное удостоверение, действительное до тех пор, пока он остается зарегистрированным и пока действительным является текущий маркер SAS (SAS хранилища). Новый маркер SAS нельзя выдать серверу после сбоя его регистрации. Таким образом, исключается способность сервера получать доступ к файловым ресурсам Azure и любая синхронизация становится невозможной.

# <a name="portaltabportal"></a>[Портал](#tab/portal)
После установки агента службы синхронизации файлов Azure пользовательский интерфейс регистрации сервера запустится автоматически. Если он не запустился, его можно открыть вручную, перейдя в расположение файла: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. После открытия пользовательского интерфейса регистрации сервера выберите **Вход**, чтобы приступить к работе.

После входа в систему вам будет предложено ввести следующие сведения:

![Снимок экрана пользовательского интерфейса регистрации сервера](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Подписка Azure.** Подписка, содержащая службу синхронизации хранилища (дополнительные сведения см. в разделе [Развертывание службы синхронизации хранилища](#deploy-the-storage-sync-service)). 
- **Группа ресурсов.** Группа ресурсов, содержащая службу синхронизации хранилища.
- **Служба синхронизации хранилища.** Имя службы синхронизации хранилища, которую необходимо зарегистрировать.

Выбрав соответствующую информацию, выберите **Зарегистрировать**, чтобы завершить регистрацию сервера. В рамках процесса регистрации будет запрошен дополнительный вход.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Создание группы синхронизации и конечной точки облака
Группа синхронизации определяет топологию синхронизации для набора файлов. Конечные точки в группе синхронизации синхронизируются. Группа синхронизации должна содержать по крайней мере одну облачную конечную точку, которая представляет собой общий файловый ресурс Azure, и одну или более конечных точек сервера. Конечная точка сервера представляет собой путь в зарегистрированном сервере. У сервера конечные точки могут быть в нескольких группах синхронизации. Вы можете создать необходимое число групп синхронизации, чтобы соответствующим образом описать желаемую топологию синхронизации.

Конечная точка облака является указателем файлового ресурса Azure. Все конечные точки сервера будут синхронизироваться с облачной конечной точкой, превращая ее в центр. Учетная запись хранения файлового ресурса Azure должна располагаться в том же регионе, что и служба синхронизации хранилища. Общий файловый ресурс Azure будет синхронизирован с одним исключением: будет подготовлена специальная папка, сопоставимая со скрытой папкой "System Volume Information" на томе NTFS. Этот каталог называется ".SystemShareInformation". Он содержит важные метаданные синхронизации, которые не синхронизируются с другими конечными точками. Не используйте и не удаляйте его!

> [!Important]  
> В группе синхронизации можно внести изменения в любую облачную конечную точку или конечную точку сервера в группе синхронизации и синхронизировать файлы в другие конечные точки. Если вы вносите изменения непосредственно в облачную конечную точку (общий файловый ресурс Azure), эти изменения сначала должны быть определены заданием обнаружения изменений службы "Синхронизация файлов Azure". Задание обнаружения изменений инициируется для облачной конечной точки каждые 24 часа. Дополнительные сведения см. в статье [Часто задаваемые вопросы о службе файлов Azure](storage-files-faq.md#afs-change-detection).

# <a name="portaltabportal"></a>[Портал](#tab/portal)
Чтобы создать группу синхронизации, на [портале Azure](https://portal.azure.com/) перейдите к службе синхронизации хранилища и выберите **+Группа синхронизации**:

![Создание группы синхронизации на портале Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

В открывшейся области введите следующую информацию, чтобы создать группу синхронизации с облачной конечной точкой:

- **Имя группы синхронизации.** Имя создаваемой группы синхронизации. Это имя должно быть уникальным в службе синхронизации хранилища. Вы можете выбрать любое подходящее имя.
- **Подписка.** Подписка, в которой развернута служба синхронизации хранилища из шага [Развертывание службы синхронизации хранилища](#deploy-the-storage-sync-service).
- **Учетная запись хранения.** Если щелкнуть **Выберите учетную запись хранения**, отобразится другая область. В ней можно выбрать учетную запись хранения с файловым ресурсом Azure, с которым нужно синхронизироваться.
- **Общая папка Azure.** Имя синхронизируемого файлового ресурса Azure.

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Чтобы создать группу синхронизации, выполните следующую команду PowerShell. Замените `<my-sync-group>` нужным именем группы синхронизации.

```PowerShell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

После успешного создания группы синхронизации можно создать облачную конечную точку. Замените `<my-storage-account>` и `<my-file-share>` фактическими значениями.

```PowerShell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzureRmStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzureStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Создание конечной точки сервера
Конечная точка сервера представляет собой определенное расположение на зарегистрированном сервере, например папку в томе сервера. Конечная точка сервера должна представлять собой путь на зарегистрированном сервере (а не на установленном ресурсе), а для распределения по уровням облака путь должен располагаться в несистемном томе. Запоминающее устройство, подключаемое к сети (NAS), не поддерживается.

# <a name="portaltabportal"></a>[Портал](#tab/portal)
Чтобы добавить конечную точку сервера, перейдите к созданной группе синхронизации и выберите **Добавить конечную точку сервера**.

![Добавление новой конечной точки сервера в области группы синхронизации](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

В открывшейся области **Добавление конечной точки сервера** введите следующие сведения, чтобы создать конечную точку сервера:

- **Зарегистрированный сервер.** Имя сервера или кластера для создания конечной точки сервера.
- **Путь.** Расположение в Windows Server, синхронизируемое в рамках группы синхронизации.
- **Уровни в облаке.** Параметр, позволяющий включать и отключать распределение по уровням облака. С помощью этого параметра редко используемые файлы можно распределить по уровням компонента "Файлы Azure".
- **Свободное место тома.** Объем свободного места, резервируемого на томе, где расположена конечная точка сервера. Например, если объем свободного места тома с единственной конечной точкой сервера равен 50 %, примерно половина объема данных будет распределена по уровням компонента "Файлы Azure". Независимо от того, включено ли распределение по уровням в облаке, общий файловый ресурс Azure всегда содержит полную копию данных в группе синхронизации.

Выберите **Создать**, чтобы добавить конечную точку сервера. Теперь ваши файлы будут синхронизироваться по всем общим файловым ресурсам Azure и Windows Server. 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Выполните следующие команды PowerShell для создания конечной точки сервера и замените `<your-server-endpoint-path>` и `<your-volume-free-space>` нужными значениями.

```PowerShell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering $true `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
}
else {
    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Подключение с помощью службы синхронизации файлов Azure
Ниже приведены рекомендуемые действия для первого подключения с помощью службы синхронизации файлов Azure с нулевым временем простоя и сохранением соответствия файлов и списка управления доступом (ACL):
 
1. Разверните службу синхронизации хранилища.
2. Создайте группу синхронизации.
3. Установите агент службы синхронизации файлов Azure на сервере с полным набором данных.
4. Зарегистрируйте этот сервер и создайте конечную точку сервера на общем ресурсе. 
5. Дождитесь, пока служба синхронизация выполнит полную отправку данных на общий файловый ресурс Azure (в облачную конечную точку).  
6. После завершения первоначальной отправки установите агент синхронизации файлов Azure на всех оставшихся серверах.
7. Создайте новые общие файловые ресурсы на всех оставшихся серверах.
8. Создайте серверные конечные точки на новых файловых ресурсах с политикой распределения по уровням облака, если необходимо. (Для этого требуется дополнительное хранилище, доступное для первоначальной настройки.)
9. Дождитесь, пока агент синхронизации файлов Azure выполнит быстрое восстановление полного пространства имен без передачи фактических данных. После полной синхронизации пространства имен служба синхронизации заполнит место на локальном диске на основе политики распределения по уровням облака для серверной конечной точки. 
10. Дождитесь завершения синхронизации и протестируйте топологию нужным образом. 
11. Перенаправьте пользователей и приложения в новую общую папку.
12. При необходимости можно удалить все повторяющиеся общие папки на серверах.
 
Если дополнительное место для первоначального подключения отсутствует, но требуется присоединиться к существующим общим папкам, можно предварительно заполнить данными общие файловые ресурсы Azure. Такой подход рекомендуется, только если вы допускаете время простоя и можете гарантировать отсутствие изменений данных на общих ресурсах сервера в процессе исходного подключения. 
 
1. Убедитесь, что данные на любом сервере не будут изменяться в ходе подключения.
2. Предварительно заполните файловые ресурсы Azure серверными данными с помощью любого средства передачи данных по протоколу SMB, например Robocopy, прямого копирования SMB. AzCopy не передает данных по протоколу SMB, поэтому средство не удастся использовать для предварительной отправки данных.
3. Создайте топологию службы синхронизации файлов Azure с нужными серверными конечными точками, указывающими на существующие общие папки.
4. Дождитесь, пока процесс синхронизации завершит сверку на всех конечных точках. 
5. После этого можно открыть общие ресурсы для внесения изменений.
 
Учтите, что сейчас подход с предварительным заполнением имеет несколько ограничений: 
- Полная точность для файлов не сохраняется. Например, файлы теряют списки управления доступом и метки времени.
- Изменения данных на сервере до полного запуска топологии синхронизации могут вызывать конфликты на серверных конечных точках.  
- После создания облачной конечной точки и перед выполнением начальной синхронизации служба синхронизации файлов Azure запускает процесс обнаружения файлов в облаке. Время, необходимое для завершения этого процесса, зависит от разных факторов, включая скорость передачи данных по сети, доступную пропускную способность, а также число файлов и папок. По грубой оценке в рамках предварительной версии процесс обнаружения выполняется со скоростью приблизительно 10 файлов в секунду. Таким образом, даже если предварительное заполнение завершится быстро, полный запуск системы может выполняться значительно дольше, когда данные предварительно заполняются в облаке.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Перенос развертывания репликации DFS (DFS-R) в службу синхронизации файлов Azure
Чтобы перенести развертывание DFS-R в службу "Синхронизация файлов Azure", сделайте следующее:

1. Создайте группу синхронизации для представления топологии DFS-R, которую требуется заменить.
2. Начните с сервера с полным набором данных в топологии DFS-R, которую следует перенести. Установите службу "Синхронизация файлов" Azure на этом сервере.
3. Зарегистрируйте этот сервер и создайте серверную конечную точку для переноса первого сервера. Не включайте распределение по уровням облака.
4. Выполните синхронизацию всех данных с файловым ресурсом Azure (облачной конечной точкой).
5. Установите и зарегистрируйте агент синхронизации файлов Azure на каждом из оставшихся серверов DFS-R.
6. Отключите DFS-R. 
7. Создайте серверную конечную точку на каждом из серверов DFS-R. Не включайте распределение по уровням облака.
8. Дождитесь завершения синхронизации и протестируйте топологию нужным образом.
9. Снимите DFS-R с учета.
10. Теперь можно включить распределение по уровням облака на любой серверной конечной точке.

Дополнительные сведения см. в статье [Azure File Sync interop with Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs) (Взаимодействие службы "Синхронизация файлов Azure" с распределенной файловой системой (DFS)).

## <a name="next-steps"></a>Дополнительная информация
- [Добавление и удаление конечных точек сервера службы синхронизации файлов Azure](storage-sync-files-server-endpoint.md)
- [Регистрация и отмена регистрации сервера в службе синхронизации файлов Azure (предварительная версия)](storage-sync-files-server-registration.md)
