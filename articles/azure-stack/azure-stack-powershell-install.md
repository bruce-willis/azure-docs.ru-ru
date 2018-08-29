---
title: Установка PowerShell для Azure Stack | Документация Майкрософт
description: Узнайте, как установить PowerShell для Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 08/10/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: 2619f959dbefba84ea1a4d5aa974055998b78b5a
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41946420"
---
# <a name="install-powershell-for-azure-stack"></a>Установка PowerShell для Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

Для работы с облаком необходимо установить модули PowerShell, совместимые с Azure Stack. Совместимость обеспечивается с помощью функции *профилей API*.

Профили API позволяют управлять различиями между версиями Azure и Azure Stack. Профиль версии API — это набор модулей Azure Resource Manager PowerShell с определенными версиями API. Каждая облачная платформа имеет набор поддерживаемых профилей версий API. Например, Azure Stack поддерживает версию профиля за определенную дату, например **2017-03-09-profile**, а Azure поддерживает профиль API **последней** версии. При установке профиля устанавливается набор модулей Azure Resource Manager PowerShell, которые соответствуют выбранному профилю.

Вы можете установить совместимые модули PowerShell для Azure Stack в подключенном, частично подключенном к Интернету или в автономном режиме. В этой статье изложены подробные инструкции по установке PowerShell для Azure Stack в приведенных ниже случаях.

## <a name="1-verify-your-prerequisites"></a>1. Проверка необходимых компонентов

Прежде чем приступать к работе с Azure Stack и PowerShell, необходимо выполнить несколько требований.

- **Версия PowerShell 5.0**  
Чтобы проверить используемую версию, выполните $PSVersionTable.PSVersion и сравните **номер основной версии**. Если у вас нет PowerShell 5.0, перейдите по [ссылке](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell), чтобы обновить PowerShell до версии 5.0.

  > [!Note]  
  > Для PowerShell 5.0 требуется компьютер с ОС Windows.

- **Запуск командной строки PowerShell с повышенными привилегиями**  
  Необходимо запустить PowerShell с правами администратора.

- **Доступ к коллекции PowerShell**  
  Необходим доступ к [коллекции PowerShell](https://www.powershellgallery.com). Коллекция является центральным репозиторием содержимого PowerShell. Модуль **PowerShellGet** содержит командлеты для обнаружения, установки, обновления и публикации артефактов PowerShell, таких как модули, ресурсы DSC, возможности ролей и скрипты из коллекции PowerShell и других частных репозиториев. Если PowerShell используется в автономном режиме, необходимо извлечь ресурсы с компьютера с подключением к Интернету и сохранить их в доступном на отключенном компьютере месте.


<!-- Nuget? -->

## <a name="2-validate-if-the-powershell-gallery-is-accessible"></a>2. Проверка доступа к коллекции PowerShell

Проверьте, зарегистрирована ли коллекция PSGallery в качестве репозитория.

> [!Note]  
> На этом шаге необходим доступ к Интернету. 

Откройте командную строку PowerShell с повышенными привилегиями и выполните следующие командлеты:

````PowerShell  
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
````

Если репозиторий не зарегистрирован, откройте сеанс Windows PowerShell с повышенными привилегиями и выполните в нем следующую команду.

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Удалите существующие версии модулей PowerShell для Azure Stack

Прежде чем устанавливать нужную версию, обязательно удалите все установленные ранее модули PowerShell AzureRM для Azure Stack. Удалить их можно с помощью следующих двух способов.

1. Чтобы удалить имеющиеся модули PowerShell AzureRM, закройте все активные сеансы PowerShell и выполните следующие командлеты:

  ````PowerShell  
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ````

2. Удалите все папки, имена которых начинаются с `Azure` из папок `C:\Program Files\WindowsPowerShell\Modules` и `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules`. При удалении этих папок удаляются все установленные модули PowerShell.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Установка PowerShell для Azure Stack с подключением к Интернету

Для использования Azure Stack требуется профиль версии API **2017-03-09-profile**, который можно получить при установке модуля **AzureRM.Bootstrapper**. В дополнение к модулям AzureRM нужно установить модули PowerShell, предназначенные для Azure Stack. 

Выполните следующий скрипт PowerShell, чтобы установить эти модули на рабочей станции разработки:

  - **Версия 1.4.0** (Azure Stack 1804 или выше)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.4.0 if Azure Stack is running 1804 at a minimum 
    Install-Module -Name AzureStack -RequiredVersion 1.4.0
    ```

- **Версия 1.2.11** (до версии 1804)

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper 

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

    # Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 
    ```

Подтвердите установку, выполнив следующую команду:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Если установка выполнена успешно, в выходных данных указываются модули AzureRM и AzureStack.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Установка PowerShell без подключения к Интернету

В автономном режиме вам следует сначала скачать модули PowerShell на компьютер с подключением к Интернету, а затем перенести их туда, где будет устанавливаться Пакет средств разработки Azure Stack.

Войдите на компьютер с подключением к Интернету и выполните следующие скрипты для скачивания пакетов Azure Resource Manager и AzureStack на локальный компьютер в зависимости от используемой версии Azure Stack.


  - **Версия 1.3.0** (Azure Stack 1804 или выше)
  
    > [!Note]  
    Чтобы выполнить обновление версии 1.2.11, прочитайте [руководство по миграции](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - **Версия 1.2.11** (до версии 1804)

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"

      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    ````

2. Скопируйте скачанные пакеты на USB-устройство.

3. Войдите на рабочую станцию и скопируйте пакеты с USB-устройства в нужное расположение на ней.

4. Затем зарегистрируйте это расположение в качестве репозитория по умолчанию и установите из этого репозитория модули AzureRM и AzureStack:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Настройка PowerShell для использования прокси-сервера

Если для доступа к Интернету требуется прокси-сервер, необходимо сначала настроить PowerShell для использования имеющегося прокси-сервера.

1. Откройте командную строку PowerShell с повышенными привилегиями.
2. Выполните следующие команды:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Дополнительная информация

 - [Download Azure Stack tools from GitHub](azure-stack-powershell-download.md) (Скачивание средств Azure Stack из GitHub).
 - [Configure the Azure Stack user's PowerShell environment](user/azure-stack-powershell-configure-user.md) (Настройка пользовательской среды PowerShell в Azure Stack).  
 - [Configure the Azure Stack operator's PowerShell environment](azure-stack-powershell-configure-admin.md) (Настройка среды PowerShell для оператора Azure Stack). 
 - [Manage API version profiles in Azure Stack](user/azure-stack-version-profiles.md) (Управление профилями версий API в Azure Stack).  
