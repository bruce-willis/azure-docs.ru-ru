---
title: Подключение к Azure Stack | Документация Майкрософт
description: Узнайте, как подключиться к Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: 1cdf013325afe4b217f5f56043e06f60a4933419
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585634"
---
# <a name="connect-to-azure-stack-development-kit"></a>Подключение к пакету средств разработки Azure Stack

*Область применения: Пакет средств разработки Azure Stack*

Для управления ресурсами сначала необходимо подключиться к Пакету средств разработки Azure Stack. В этой статье описаны действия, выполнив которые вы сможете подключиться к пакету средств разработки. Вы можете использовать один из следующих вариантов подключения:

* [Подключение к удаленному рабочему столу.](#connect-with-remote-desktop) При подключении с помощью удаленного рабочего стола один пользователь может быстро выполнить подключение к пакету средств разработки.
* [Виртуальная частная сеть (VPN).](#connect-with-vpn) При подключении с помощью VPN несколько пользователей могут подключаться одновременно с использованием клиентов за пределами инфраструктуры Azure Stack. Для VPN-подключения требуются некоторые настройки.

<a name="connect-to-azure-stack-with-remote-desktop"></a>
##  <a name="connect-to-azure-stack-by-using-remote-desktop-connection"></a>Подключение к Azure Stack с помощью удаленного рабочего стола

С помощью подключения удаленного рабочего стола один пользователь может управлять ресурсами на портале оператора или пользовательском портале.

1. Откройте подключение к удаленному рабочему столу и подключитесь к пакету средств разработки. В качестве имени пользователя введите **AzureStack\AzureStackAdmin**. Используйте пароль оператора, указанный при настройке Azure Stack.  

2. Откройте диспетчер сервера на компьютере с пакетом средств разработки. Выберите **Локальный сервер**, снимите флажок **Internet Explorer Enhanced Security** (Усиленная безопасность Internet Explorer), а затем закройте диспетчер сервера.

3. Чтобы открыть [портал пользователя](azure-stack-key-features.md#portal), перейдите на сайт https://portal.local.azurestack.external/. Войдите, используя учетные данные пользователя. Чтобы открыть [портал оператора](azure-stack-key-features.md#portal) Azure Stack, перейдите в на сайт https://adminportal.local.azurestack.external/. Войдите, используя учетные данные Azure Active Directory (Azure AD), указанные во время установки.

<a name="connect-to-azure-stack-with-vpn"></a>
## <a name="connect-to-azure-stack-by-using-vpn"></a>Подключение к Azure Stack с помощью VPN

Вы можете установить VPN-подключение с разделенным туннелем к Пакету средств разработки Azure Stack. Через VPN-подключение можно получить доступ к порталу оператора Azure Stack, порталу пользователя и установленным локально средствам, таким как Visual Studio и PowerShell, для управления ресурсами Azure Stack. VPN-подключение поддерживается в развертываниях Azure AD и служб федерации Active Directory (AD FS). VPN-подключения позволяют нескольким клиентам одновременно подключаться к Azure Stack.

> [!NOTE]
> VPN-подключение не предоставляет возможность подключения к виртуальным машинам инфраструктуры Azure Stack.

### <a name="prerequisites"></a>Предварительные требования

1. Установите [Azure PowerShell, совместимый с Azure Stack](azure-stack-powershell-install.md), на своем локальном компьютере.  
2. Скачайте [средства, необходимые для работы с Azure Stack](azure-stack-powershell-download.md).

### <a name="set-up-vpn-connectivity"></a>Настройка VPN-подключения

Чтобы создать VPN-подключение к пакету средств разработки, откройте Windows PowerShell с правами администратора на локальном компьютере под управлением Windows. Затем выполните следующий скрипт (обновите значения IP-адреса и пароля для своей среды):

```PowerShell
# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit computer’s host IP address and certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Если программа установки завершается успешно, **azurestack** появится в списке VPN-подключений.

![Сетевые подключения](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Подключение к Azure Stack

Подключитесь к экземпляру Azure Stack, используя один из следующих двух способов:  

* Использование команды `Connect-AzsVpn `:
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  При появлении запроса обозначьте узел Azure Stack как доверенный и установите сертификат из **AzureStackCertificateAuthority** в хранилище сертификатов на локальном компьютере (Запрос может быть скрыт окном PowerShell.)

* На локальном компьютере выберите **Параметры сети** > **VPN** > **azurestack** > **Подключиться**. При запросе на вход введите имя пользователя (**AzureStack\AzureStackAdmin**) и пароль.

### <a name="test-vpn-connectivity"></a>Проверка VPN-подключения

Чтобы проверить соединение с порталом, откройте веб-браузер и перейдите на пользовательский портал (https://portal.local.azurestack.external/) или на портал оператора (https://adminportal.local.azurestack.external/). Войдите и создайте ресурсы.  

## <a name="next-steps"></a>Дополнительная информация

[Make virtual machines available to your Azure Stack users](azure-stack-tutorial-tenant-vm.md) (Создание предложения по виртуальным машинам для пользователей Azure Stack)
