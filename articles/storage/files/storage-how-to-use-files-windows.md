---
title: Использование общей папки Azure в Windows | Документация Майкрософт
description: Узнайте, как использовать общую папку Azure в Windows и Windows Server.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: renash
ms.component: files
ms.openlocfilehash: 3e63a4c05fde570e598ba05c65fb99cec0427711
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226423"
---
# <a name="use-an-azure-file-share-with-windows"></a>Использование общей папки Azure в Windows
[Файлы Azure](storage-files-introduction.md) — это простая в использовании облачная файловая система от корпорации Майкрософт. Общие папки Azure можно легко использовать в Windows и Windows Server. В этой статье рассматриваются рекомендации по использованию общей папки Azure в Windows и Windows Server.

Чтобы использовать общую папку Azure за пределами региона Azure, в котором она размещается, например локально или в другом регионе Azure, операционная система должна поддерживать протокол SMB 3.0. 

Общие папки Azure можно использовать в ОС Windows, работающей на виртуальной машине Azure или в локальной среде. В следующей таблице показано, какие версии операционной системы поддерживают доступ к общим папкам и в какой среде:

| Версия Windows        | Версия SMB | Возможность подключения на виртуальной машине Azure | Возможность подключения в локальной среде |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019 (предварительная версия)<sup>1</sup> | SMB 3.0 | Yes | Yes |
| Windows 10<sup>2</sup> | SMB 3.0 | Yes | Yes |
| Semi-Annual Channel для Windows Server<sup>3</sup> | SMB 3.0 | Yes | Yes |
| Windows Server 2016    | SMB 3.0     | Yes                   | Yes                  |
| Windows 8.1            | SMB 3.0     | Yes                   | Yes                  |
| Windows Server 2012 R2 | SMB 3.0     | Yes                   | Yes                  |
| Windows Server 2012    | SMB 3.0     | Yes                   | Yes                  |
| Windows 7              | SMB 2.1     | Yes                   | Нет                    |
| Windows Server 2008 R2 | SMB 2.1     | Yes                   | Нет                    |

<sup>1</sup> Windows Server 2019 доступна в предварительной версии в рамках [программы предварительной оценки Windows Server](https://insider.windows.com/for-business-getting-started-server/). Хотя Windows Server 2019 еще не поддерживается для использования в рабочей среде, сообщите нам, если у вас есть какие-либо проблемы с подключением к общим папкам Azure, за исключением того, что описано в [руководстве по устранению неполадок для Windows](storage-troubleshoot-windows-file-connection-problems.md).  
<sup>2</sup> Windows 10 версии 1507, 1607, 1703, 1709 и 1803.  
<sup>3</sup> Windows Server версии 1709 и 1803.

> [!Note]  
> Мы всегда рекомендуем использовать последнюю версию KB для своей версии Windows.

## <a name="prerequisites"></a>Предварительные требования 
* **Имя учетной записи хранения**. Чтобы подключить общую папку Azure, вам потребуется имя учетной записи хранения.

* **Ключ учетной записи хранения**. Чтобы подключить общую папку Azure, вам потребуется первичный (или вторичный) ключ учетной записи хранения. В настоящее время ключи SAS для подключения не поддерживаются.

* **Убедитесь, что порт 445 открыт**: для протокола SMB требуется, чтобы TCP-порт 445 был открыт, в противном случае подключение будет невозможно. Вы можете проверить, блокирует ли ваш брандмауэр порт 445, с помощью командлета `Test-NetConnection`. Не забудьте заменить `your-storage-account-name` соответствующим именем для своей учетной записи хранения.

    ```PowerShell
    Test-NetConnection -ComputerName <your-storage-account-name>.core.windows.net -Port 445
    
    ```

    Если установка прошла успешно, отобразятся следующие выходные данные.

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > Вышеуказанная команда возвращает текущий IP-адрес учетной записи хранения. Не гарантируется, что IP-адрес будет постоянным. Он может измениться в любое время. Не следует жестко задавать этот IP-адрес в скриптах или в конфигурации брандмауэра. 

## <a name="using-an-azure-file-share-with-windows"></a>Использование общей папки Azure в Windows
Чтобы использовать общую папку Azure в Windows, вы должны либо подключить ее, то есть присвоить ей букву диска или путь точки подключения, либо получить доступ к ней через [UNC-путь](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx). 

В отличие от других общих папок SMB, с которыми вы могли взаимодействовать, например папок, размещенных на серверах Windows Server, Linux Samba или на устройстве NAS, в настоящее время аутентификация Kerberos с помощью идентификатора Active Directory (AD) или Azure Active Directory (AAD) для общих папок Azure не поддерживается, хотя мы [работаем](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles) над этой функцией. Вместо этого для доступа к вашей общей папке Azure нужно использовать ключ учетной записи хранения, содержащей эту папку. Это ключ администратора для учетной записи хранения, включая разрешения администратора для всех файлов и папок в общей папке, к которой вы получаете доступ, и для всех общих папок и других ресурсов хранения (большие двоичные объекты, очереди, таблицы и т. д.), которые размещаются в этой учетной записи. Если этого недостаточно для вашей рабочей нагрузки, служба [Синхронизация файлов Azure](storage-files-planning.md#data-access-method) может помочь урегулировать отсутствие проверки подлинности Kerberos и поддержки ACL до тех пор, пока не будет доступна общедоступная поддержка ACL и аутентификация Kerberos на основе AAD.

Общим вариантом для переноса бизнес-приложений, которые ожидают наличия подключенной к Azure общей папки SMB, является использование общей папки Azure в качестве альтернативы для запуска выделенного файлового сервера Windows на виртуальной машине Azure. Одна из важных рекомендаций для успешного перевода бизнес-приложения на использование общей папки Azure заключается в том, что многие бизнес-приложения работают в контексте выделенной учетной записи службы с ограниченными системными правами, а не административной учетной записью виртуальной машины. Поэтому нужно обязательно подключить или сохранить учетные данные для общей папки Azure из контекста учетной записи службы, а не вашей учетной записи администратора.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Сохранение учетных данных общей папки Azure в Windows  
Программа [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) позволяет сохранить учетные данные учетной записи хранения в Windows. Это означает, что когда вы пытаетесь получить доступ к общей папке Azure через UNC-путь или подключаете общую папку Azure, вам не нужно указывать учетные данные. Чтобы сохранить данные учетной записи хранения, выполните следующие команды PowerShell, заменив `<your-storage-account-name>` и `<your-resoure-group-name>` там, где это необходимо.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
# already logged in.
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command "cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)"
```

Вы можете проверить, сохранила ли программа cmdkey данные учетной записи хранения, используя параметр list:

```PowerShell
cmdkey /list
```

Если учетные данные общей папки Azure сохранены успешно, вы увидите следующие выходные данные (в списке могут быть дополнительные ключи):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Теперь вы можете подключить общую папку или получить к ней доступ без необходимости предоставления дополнительных учетных данных.

#### <a name="advanced-cmdkey-scenarios"></a>Расширенные сценарии cmdkey
Есть два дополнительных сценария использования cmdkey: сохранение учетных данных другого пользователя на компьютере, например данных учетной записи службы, и сохранение учетных данных на удаленном компьютере с помощью удаленного взаимодействия PowerShell.

Сохранить учетные данные для другого пользователя на компьютере очень просто: войдя в учетную запись, выполните следующую команду PowerShell:

```PowerShell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

Откроется новое окно PowerShell в контексте пользователя вашей учетной записи службы (или учетной записи пользователя). Затем вы можете использовать программу cmdkey, как описано [выше](#persisting-azure-file-share-credentials-in-windows).

Однако сохранение учетных данных на удаленном компьютере с использованием удаленного взаимодействия PowerShell невозможно, так как cmdkey не разрешает доступ даже для дополнений к хранилищу учетных данных, когда пользователь выполняет удаленный вход с помощью PowerShell. Мы рекомендуем войти в систему с помощью [удаленного рабочего стола](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows).

### <a name="mount-the-azure-file-share-with-powershell"></a>Подключение общей папки Azure с помощью PowerShell
Выполните следующие команды в обычном (то есть без повышенных прав) сеансе PowerShell для подключения общей папки Azure. Не забудьте заменить `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` и `<desired-drive-letter>` правильными данными.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
# already logged in.
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> Использование параметра `-Persist` в командлете `New-PSDrive` позволит повторно подключить общую папку при загрузке, только если учетные данные сохранены. Вы можете сохранить учетные данные, используя cmdkey, как [описано ранее](#persisting-azure-file-share-credentials-in-windows). 

При желании вы можете отключить общую папку Azure с помощью следующего командлета PowerShell.

```PowerShell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Подключение общей папки Azure с помощью проводника
> [!Note]  
> Имейте в виду, что следующие инструкции приведены для Windows 10 и могут немного отличаться для более поздних версий. 

1. Откройте проводник. Это можно сделать, открыв меню "Пуск" или нажав клавиши WIN+E.

2. Перейдите к элементу **Этот компьютер** в левой области окна. Меню, доступные на ленте, изменятся. В меню "Компьютер" выберите раздел **Подключение сетевого диска**.
    
    ![Снимок экрана раскрывающегося меню "Подключение сетевого диска"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Скопируйте UNC-путь из области **Подключение** на портале Azure. 

    ![UNC-путь из области подключения службы файлов Azure](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Выберите букву диска и введите UNC-путь. 
    
    ![Снимок экрана диалогового окна "Подключение сетевого диска"](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Используйте имя учетной записи хранения с префиксом `AZURE\` как имя пользователя и ключ учетной записи хранения в качестве пароля.
    
    ![Снимок экрана диалогового окна сетевых учетных данных](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Выберите общую папку Azure.
    
    ![Общая папка Azure теперь подключена](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Когда вы будете готовы отключить общую папку Azure, это можно сделать, щелкнув правой кнопкой мыши запись для общей папки в разделе проводника **Сетевые расположения** и выбрав параметр **Отключить**.

### <a name="accessing-share-snapshots-from-windows"></a>Доступ к моментальным снимкам общих файловых ресурсов из Windows
Если вы уже создали моментальные снимки общего файлового ресурса вручную или автоматически с помощью скрипта или такой службы, как Azure Backup, вы можете просмотреть предыдущие версии общего ресурса, каталога или определенного файла из подключенного общего файлового ресурса в Windows. Вы можете создать его с помощью [портала Azure](storage-how-to-use-files-portal.md), [Azure PowerShell](storage-how-to-use-files-powershell.md) или [Azure CLI](storage-how-to-use-files-cli.md).

#### <a name="list-previous-versions"></a>Вывод списка предыдущих версий
Перейдите к элементу или родительскому элементу, который требуется восстановить. Дважды щелкните для перехода к нужному каталогу. Щелкните правой кнопкой мыши и выберите в меню пункт **Свойства**.

![Контекстное меню для выбранного каталога](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Выберите пункт **Предыдущие версии**, чтобы просмотреть список моментальных снимков общих ресурсов для этого каталога. Вывод списка может занять несколько секунд в зависимости от скорости сети и количества моментальных снимков в каталоге.

![Вкладка "Предыдущие версии"](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Чтобы открыть тот или иной моментальный снимок, нажмите кнопку **Открыть**. 

![Открытый моментальный снимок](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Восстановление из предыдущей версии
Нажмите кнопку **Восстановить**, чтобы рекурсивно скопировать содержимое всего каталога на момент создания моментального снимка общего ресурса в исходном расположении.
 ![Кнопка "Восстановить" в предупреждении](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Обеспечение безопасности Windows или Windows Server
Чтобы подключить общую папку Azure в Windows, порт 445 должен быть доступен. Многие организации блокируют порт 445 из-за угроз безопасности, присущих SMB 1. SMB 1, также известный как CIFS (Common Internet File System), является устаревшим протоколом файловой системы, включенным в Windows и Windows Server. SMB 1 — это устаревший, неэффективный и, самое главное, небезопасный протокол. Хорошей новостью является то, что компонент "Файлы Azure" не поддерживает SMB 1, а все поддерживаемые версии Windows и Windows Server позволяют удалить или отключить этот протокол. Мы всегда [настоятельно рекомендуем](https://aka.ms/stopusingsmb1) удалять или отключать клиент и сервер SMB 1 в Windows перед использованием общей папки Azure в рабочей среде.

В следующей таблице представлена ​​подробная информация о состоянии SMB 1 для каждой версии Windows:

| Версия Windows                           | Состояние SMB 1 по умолчанию | Метод удаления или отключения       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019 (предварительная версия)             | Отключено             | Удаление с помощью функции Windows |
| Windows Server версии 1709 или выше            | Отключено             | Удаление с помощью функции Windows |
| Windows 10 версии 1709 или выше                | Отключено             | Удаление с помощью функции Windows |
| Windows Server 2016                       | Включено              | Удаление с помощью функции Windows |
| Windows 10 версии 1507, 1607 и 1703 | Включено              | Удаление с помощью функции Windows |
| Windows Server 2012 R2                    | Включено              | Удаление с помощью функции Windows | 
| Windows 8.1                               | Включено              | Удаление с помощью функции Windows | 
| Windows Server 2012                       | Включено              | Отключение с помощью реестра       | 
| Windows Server 2008 R2                    | Включено              | Отключение с помощью реестра       |
| Windows 7                                 | Включено              | Отключение с помощью реестра       | 

### <a name="auditing-smb-1-usage"></a>Аудит использования SMB 1
> Применяется к Windows Server 2019 (предварительная версия), Semi-Annual Channel для Windows Server (версии 1709 и 1803), Windows Server 2016, Windows 10 (версии 1507, 1607, 1703, 1709 и 1803), Windows Server 2012 R2 и Windows 8.1.

Прежде чем удалять SMB 1 в вашей среде, можете провести аудит использования SMB 1, чтобы узнать, повлияет ли это изменение на работу каких-либо клиентов. Если какие-либо запросы к папкам SMB выполняются с помощью SMB 1, событие аудита будет регистрироваться в журнале событий в разделе `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Чтобы включить поддержку аудита в Windows Server 2012 R2 и Windows 8.1, требуется пакет обновлений не ниже указанного в [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Чтобы включить аудит, выполните следующий командлет в сеансе PowerShell с повышенными правами:

```PowerShell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Удаление SMB 1 из Windows Server
> Применяется к Windows Server 2019 (предварительная версия), Semi-Annual Channel для Windows Server (версии 1709 и 1803), Windows Server 2016 и Windows Server 2012 R2.

Чтобы удалить SMB 1 из экземпляра Windows Server, выполните следующий командлет в сеансе PowerShell с повышенными правами:

```PowerShell
Remove-WindowsFeature -Name FS-SMB1
```

Чтобы завершить процесс удаления, перезагрузите сервер. 

> [!Note]  
> Начиная с Windows 10 и Windows Server версии 1709, SMB 1 не установлен по умолчанию и имеет отдельные функции Windows для клиента SMB 1 и сервера SMB 1. Мы всегда рекомендуем удалять сервер SMB 1 (`FS-SMB1-SERVER`) и клиент SMB 1 (`FS-SMB1-CLIENT`).

### <a name="removing-smb-1-from-windows-client"></a>Удаление SMB 1 из клиента Windows
> Применяется к Windows 10 (версии 1507, 1607, 1703, 1709 и 1803) и Windows 8.1.

Чтобы удалить SMB 1 из клиента Windows, выполните следующий командлет в сеансе PowerShell с повышенными правами:

```PowerShell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Чтобы завершить процесс удаления, перезагрузите компьютер.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Отключение SMB 1 в устаревших версиях Windows и Windows Server
> Применяется к Windows Server 2012, Windows Server 2008 R2 и Windows 7.

SMB 1 нельзя полностью удалить в устаревших версиях Windows и Windows Server, но его можно отключить через реестр. Чтобы отключить SMB 1, создайте новый ключ реестра `SMB1` типа `DWORD` со значением `0` в `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Вы можете легко выполнить это с помощью следующего командлета PowerShell:

```PowerShell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

После создания этого раздела реестра необходимо перезагрузить сервер, чтобы отключить SMB 1.

### <a name="smb-resources"></a>Ресурсы по SMB
- [Запись блога о прекращении использования SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/)
- [Центр координации продуктов с SMB 1](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/)
- [Запись блога об обнаружении SMB 1 в своей среде с помощью DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/)
- [Запись блога об отключении SMB 1 в групповой политике](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/)

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о службе файлов Azure см. по следующим ссылкам.
- [Планирование развертывания службы файлов Azure](storage-files-planning.md)
* [Часто задаваемые вопросы](../storage-files-faq.md)
* [Troubleshoot Azure File storage problems in Windows](storage-troubleshoot-windows-file-connection-problems.md) (Устранение неполадок хранилища файлов Azure в Windows)      
