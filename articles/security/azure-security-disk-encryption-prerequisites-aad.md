---
title: Предварительные требования для шифрования дисков Azure с использованием приложения Azure AD (предыдущий выпуск) | Документация Майкрософт
description: Эта статья содержит предварительные требования для использования шифрования дисков Microsoft Azure с виртуальными машинами IaaS.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 973cbdc1-12bc-4e0e-90cd-f19b045417a2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2018
ms.author: mstewart
ms.openlocfilehash: 701418c4c2f17d151061ffde704b5a7f05551d9c
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338467"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Предварительные требования для шифрования дисков Azure (предыдущий выпуск)

**В новом выпуске шифрования дисков Azure устранена необходимость предоставлять параметр приложения Azure AD для включения шифрования дисков виртуальной машины. В новом выпуске больше не требуется вводить учетные данные Azure AD на этапе включения шифрования. При использовании нового выпуска все новые виртуальные машины должны быть зашифрованы без параметров приложения Azure AD. Инструкции по включению шифрования дисков виртуальных машин при использовании нового выпуска см. в статье [Предварительные требования для шифрования дисков Azure](azure-security-disk-encryption-prerequisites.md). Виртуальные машины, которые уже были зашифрованы с помощью параметров приложения Azure AD, по-прежнему поддерживаются. Их следует и дальше обслуживать с использованием синтаксиса AAD.**

 В этой статье объясняются компоненты, которые должны быть установлены до того, как вы сможете использовать шифрование дисков Azure. Наряду с общими требованиями, шифрование дисков Azure интегрировано с [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) и для управления ключами шифрования в хранилище ключей использует приложение Azure AD для проверки подлинности. Вы также можете использовать [Azure PowerShell](/powershell/azure/overview) или [Azure CLI](/cli/azure/) для настройки или конфигурации хранилища ключей и приложения Azure AD.

Ниже перечислены предварительные требования для включения шифрования дисков Azure на виртуальных машинах IaaS Azure в сценариях, которые обсуждались в статье [Azure Disk Encryption for IaaS VMs](azure-security-disk-encryption-overview.md) (Шифрование дисков Azure для виртуальных машин IaaS). Эти требования обязательно должны быть выполнены. 

> [!NOTE]
> Выполнение некоторых приведенных рекомендаций может привести к более интенсивному использованию данных, сети или вычислительных ресурсов, а следовательно к дополнительным затратам на лицензии или подписки. Необходима действующая подписка Azure, которая позволяет создавать ресурсы Azure в поддерживаемых регионах.


## <a name="bkmk_OSs"></a> Поддерживаемые операционные системы
Шифрование дисков Azure поддерживается в приведенных ниже операционных системах.

- Версии Windows Server: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 и Windows Server 2016.
    - Для Windows Server 2008 R2 перед включением шифрования в Azure требуется установить .NET Framework 4.5. Установите его из Центра обновления Windows вместе с необязательным обновлением Microsoft .NET Framework 4.5.2 для Windows Server 2008 R2 для 64-разрядных систем ([KB2901983](https://support.microsoft.com/kb/2901983)).    
- Версии клиентов Windows: клиент Windows 8 и клиент Windows 10.
- Шифрование дисков Azure поддерживается только для определенных дистрибутивов и версий серверов Linux из коллекции Azure. Список поддерживаемых сейчас версий см. в статье [Шифрование дисков Azure: часто задаваемые вопросы](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
- Для шифрования дисков Azure требуется, чтобы хранилище ключей и виртуальные машины находились в одном регионе и подписке Azure. Настройка ресурсов в отдельных регионах приведет к сбою при включении функции шифрования дисков Azure.

## <a name="bkmk_LinuxPrereq"></a> Дополнительные требования для виртуальных машин Linux IaaS 

- Службе шифрования дисков Azure на виртуальной машине Linux требуется 7 ГБ ОЗУ, чтобы выполнять шифрование диска ОС на [поддерживаемых образах](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Как только процесс шифрования диска ОС будет завершен, виртуальную машину можно настроить для работы с меньшим объемом памяти.
- Перед включением шифрования диски данных, которые нужно зашифровать, должны быть правильно указаны в /etc/fstab. Используйте постоянное имя устройства для этой записи, так как имена устройств в формате "/dev/sdX" необязательно будут с одним и тем же диском в случае перезагрузок, особенно после применения шифрования. Подробнее об этом поведении см. статье [Устранение неполадок при изменении имени устройства виртуальной машины Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md).
- Убедитесь, что параметры /etc/fstab для подключения настроены правильно. Чтобы настроить эти параметры, запустите команду mount -a или перезагрузите виртуальную машину и подключите ее заново таким образом. Как только это будет завершено, проверьте выходные данные команды lsblk, чтобы убедиться, что нужный диск все еще подключен. 
    - Если файл /etc/fstab не подключает диск должным образом до включения шифрования, служба шифрования дисков Azure не сможет правильно подключить его.
    - Служба шифрования дисков Azure переместит информацию о подключении из /etc/fstab в собственный файл конфигурации как часть процесса шифрования. Не волнуйтесь, если увидите, что запись отсутствует в /etc/fstab после завершения шифрования диска данных.
    -  После перезагрузки процессу шифрования дисков Azure потребуется некоторое время для подключения только что зашифрованных дисков. После перезагрузки они не будут доступны сразу. Прежде чем зашифрованные диски станут доступными для других процессов, потребуется некоторое время для их запуска, разблокировки и подключения. Этот процесс может занять более минуты после перезагрузки в зависимости от характеристик системы.

Примеры команд, которые можно использовать для подключения дисков данных и создания необходимых записей /etc/fstab, можно найти в [строках 197–205 этого файла сценария](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L197-L205). 


## <a name="bkmk_GPO"></a> Сетевые подключения и групповая политика

**Чтобы использовать функцию шифрования дисков Azure, конфигурация конечной точки сети виртуальной машины IaaS должна соответствовать приведенным ниже требованиям:**
  - Виртуальная машина IaaS должна иметь возможность подключиться к конечной точке Azure Active Directory \[login.microsoftonline.com\], чтобы получить маркер для подключения к хранилищу ключей.
  - Для записи ключей шифрования в ваше хранилище ключей виртуальная машина IaaS должна иметь возможность подключиться к конечной точке хранилища ключей.
  - Виртуальная машина IaaS должна иметь возможность подключиться к конечной точке службы хранилища Azure, в которой размещен репозиторий расширений Azure, и к учетной записи хранения Azure, в которой размещены VHD-файлы.
  -  Если ваша политика безопасности ограничивает доступ к Интернету с виртуальных машин Azure, можно разрешить указанный выше универсальный код ресурса (URI) и настроить определенное правило, чтобы разрешить исходящие подключения к данным IP-адресам. Дополнительные сведения см. в статье [Доступ к Azure Key Vault из-за брандмауэра](../key-vault/key-vault-access-behind-firewall.md).    


**Групповая политика:**
 - Решение шифрования дисков Azure использует внешний предохранитель ключа BitLocker для виртуальных машин IaaS под управлением Windows. Если виртуальные машины присоединены к домену, не применяйте групповые политики, требующие использования предохранителей TPM. Сведения о групповой политике "Разрешить использование BitLocker без совместимого TPM" см. в [справке по групповым политикам BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup).

-  Политика Bitlocker на присоединенных к домену виртуальных машинах с настраиваемой групповой политикой должна включать следующий параметр: [Configure user storage of bitlocker recovery information (Настроить сведения о восстановлении bitlocker в пользовательском хранилище данных) > Разрешить 256-разрядный ключ восстановления](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Шифрование дисков Azure завершится ошибкой, если параметры настраиваемой групповой политики для Bitlocker несовместимы. На компьютерах без соответствующего параметра политики может потребоваться применить новую политику, принудительно обновить ее (gpupdate.exe /force) и перезагрузить компьютер.  


## <a name="bkmk_PSH"></a> Azure PowerShell
В [Azure PowerShell](/powershell/azure/overview) доступен набор командлетов, которые используют модель [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) для управления ресурсами Azure. Его можно использовать в браузере с [Azure Cloud Shell](../cloud-shell/overview.md), а также установить на локальном компьютере, следуя инструкциям ниже, и применять в любом сеансе PowerShell. Если вы уже установили его локально, убедитесь, что вы используете последнюю версию версии пакета SDK для Azure PowerShell для настройки шифрования дисков Azure. Скачайте последнюю версию [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Установите Azure PowerShell для использования на вашем локальном компьютере (необязательно): 
1. Следуйте инструкциям по ссылкам для вашей операционной системы, а затем перейдите к остальным шагам.      
    - [Установка и настройка Azure PowerShell для Windows](/powershell/azure/install-azurerm-ps). 
        - Установите PowerShellGet, Azure PowerShell и загрузите модуль AzureRM. 
    - [Установка и настройка Azure PowerShell в macOS и Linux](/powershell/azure/install-azurermps-maclinux).
        -  Установите PowerShell Core, Azure PowerShell для .NET Core и загрузите модуль AzureRM.Netcore.
2. Установите модуль [PowerShell для Azure Active Directory](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Проверьте версии установленных модулей.
      ```powershell
      Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Войдите в Azure, используя командлет [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).
     
     ```powershell
     Connect-AzureRmAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzureRmSubscription and 
     specify it with Set-AzureRmContext.  #>
     Get-AzureRmSubscription
     Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Подключитесь к Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. При необходимости ознакомьтесь со статьями [Начало работы с Azure PowerShell](/powershell/azure/get-started-azureps) и [AzureAD](/powershell/module/azuread).

## <a name="bkmk_CLI"></a> Azure CLI

[Azure CLI 2.0](/cli/azure) — это интерфейс командной строки для управления ресурсами Azure. Этот интерфейс обеспечивает гибкие функции подачи запросов, выполнение длительных операций без блокировки и простое создание скриптов. Его можно использовать в браузере с [Azure Cloud Shell](/cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell.

1. [Установите Azure CLI](/cli/azure/install-azure-cli) для использования на вашем локальном компьютере (необязательно):

2. Проверьте номер установленной версии.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Затем войдите в Azure с помощью команды [az login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. При необходимости см. статью [Начало работы с Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Необходимый рабочий процесс для Key Vault и приложения Azure AD

Если вы уже знакомы с предварительными требованиями Key Vault и Azure AD для шифрования дисков Azure, можно использовать [скрипт PowerShell предварительных требований к шифрованию дисков Azure](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Дополнительные сведения об использовании скрипта предварительных требований см. в [кратком руководстве по шифрованию виртуальных машин](quick-encrypt-vm-powershell.md) и [приложении шифрования дисков Azure](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Создать хранилище ключей. 
2. Настройте приложение Azure AD и субъект-службу.
3. Настройте политику доступа к хранилищу ключей для приложения Azure AD.
4. Установите политики расширенного доступа к хранилищу ключей.
 
## <a name="bkmk_KeyVault"></a> Создание хранилища ключей 
Шифрование дисков Azure интегрировано с [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/), что позволяет управлять секретами и ключами шифрования дисков в подписке Key Vault и контролировать их. Вы можете создать хранилище ключей или использовать существующее для шифрования дисков Azure. Дополнительные сведения о хранилищах ключей см. в статье [Приступая к работе с Azure Key Vault](../key-vault/key-vault-get-started.md) и [Защита хранилища ключей](../key-vault/key-vault-secure-your-key-vault.md). Для создания хранилища ключей можно использовать шаблон Resource Manager, Azure PowerShell или Azure CLI. 


>[!WARNING]
>Чтобы убедиться, что секреты шифрования не пересекают региональные границы, шифрованию дисков Azure требуется, чтобы хранилище ключей и виртуальные машины были расположены в одном регионе. Создайте и используйте хранилище ключей, которое находится в том же регионе, что и виртуальная машина для шифрования. 


### <a name="bkmk_KVPSH"></a> Создание хранилища ключей с помощью PowerShell

Вы можете создать хранилище ключей в Azure PowerShell с помощью командлета [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault). Дополнительные командлеты для хранилища ключей см. на странице [AzureRM.KeyVault](/powershell/module/azurerm.keyvault/). 

1. При необходимости [подключитесь к подписке Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Чтобы создать группу ресурсов, используйте команду [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup).  Чтобы перечислить расположения центров обработки данных, используйте команду [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocationn). 
     
     ```azurepowershell-interactive
     # Get-AzureRmLocation 
     New-AzureRmResourceGroup –Name 'MySecureRG' –Location 'East US'
     ```

3. Чтобы создать новое хранилище ключей, используйте командлет [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault).
    
      ```azurepowershell-interactive
     New-AzureRmKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -Location 'East US'
     ```

4. Запишите возвращенные значения **имени хранилища**, **имени группы ресурсов**, **идентификатора ресурса**, **кода URI хранилища** и **идентификатора объекта** для последующего использования при шифровании дисков. 


### <a name="bkmk_KVCLI"></a> Создание хранилища ключей с помощью Azure CLI
Вы можете управлять вашим хранилищем ключей в Azure CLI с помощью команд [az keyvault](/cli/azure/keyvault#commands). Чтобы создать хранилище ключей, используйте команду [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. При необходимости [подключитесь к подписке Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Чтобы создать группу ресурсов, используйте команду [az group create](/cli/azure/groupt#az-group-create). Для перечисления расположений используйте команду [az account list-locations](/cli/azure/account#az-account-list). 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MySecureRG" -l "East US"
     ```

3. Чтобы создать новое хранилище ключей, используйте команду[az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MySecureRG" --location "East US"
     ```

4. Запишите возвращенные значения **имени хранилища**, **имени группы ресурсов**, **идентификатора ресурса**, **кода URI хранилища** и **идентификатора объекта** для последующего использования. 

### <a name="bkmk_KVRM"></a> Создание хранилища ключей с помощью шаблона Resource Manager

Можно создать хранилище ключей с помощью [шаблона Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. В шаблоне быстрого запуска Azure щелкните **Deploy to Azure** (Развернуть в Azure).
2. Выберите подписку, группу ресурсов, расположение группы ресурсов, имя хранилища ключей, идентификатор объекта, юридические условия и соглашение, а затем щелкните **Приобрести**. 


## <a name="bkmk_ADapp"></a> Настройка приложения Azure AD и субъекта-службы 
Если шифрование нужно включить для виртуальной машины, работающей в Azure, то служба шифрования дисков Azure создает ключи шифрования и записывает их в ваше хранилище ключей. Для управления ключами шифрования в хранилище ключей требуется аутентификация Azure AD. Для этой цели создайте приложение Azure AD. Можно использовать аутентификацию на основе секрета клиента или [на основе сертификата клиента в Azure AD](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Настройка приложения Azure AD и субъекта-службы с помощью Azure PowerShell 
Чтобы выполнить приведенные ниже команды, нужно получить и использовать [модуль PowerShell для Azure AD](/powershell/azure/active-directory/install-adv2). 

1. При необходимости [подключитесь к подписке Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Чтобы создать приложение Azure AD, используйте командлет PowerShell [New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication). Вместо MyApplicationHomePage и MyApplicationUri можно указать любые значения.

     ```azurepowershell-interactive
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzureRmADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId — это идентификатор ClientID Azure AD, а $aadClientSecret — секрет клиента, который потребуется позднее для включения шифрования дисков Azure. Секрет клиента Azure AD следует хранить с соблюдением мер предосторожности. В результате запуска `$azureAdApplication.ApplicationId` вернется значение идентификатора приложения (ApplicationID).


### <a name="bkmk_ADappCLI"></a> Настройка приложения Azure AD и субъекта-службы с помощью Azure CLI

Вы можете управлять своими субъектами-службами в Azure CLI с помощью команд [az ad sp](/cli/azure/ad/sp). Дополнительные сведения см. в статье [Создание субъекта-службы Azure с помощью Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli).

1. При необходимости [подключитесь к подписке Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Создайте субъект-службу.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Возвращаемый идентификатор приложения — это идентификатор клиента Azure AD, используемый в других командах. Это также имя субъекта-службы, которое будет использоваться для команды az keyvault set-policy. Пароль — это секрет клиента, который понадобится позже, чтобы включить шифрование дисков Azure. Секрет клиента Azure AD следует хранить с соблюдением мер предосторожности.
 
### <a name="bkmk_ADappRM"></a> Настройка приложения Azure AD и субъекта-службы на портале Azure
Выполните шаги, описанные в статье [Создание приложения Azure Active Directory и субъекта-службы с доступом к ресурсам с помощью портала](../azure-resource-manager/resource-group-create-service-principal-portal.md), чтобы создать приложение Azure AD. Каждый шаг, указанный ниже, содержит ссылку непосредственно на раздел статьи. 

1. [Проверьте необходимые разрешения](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
2. [Создайте приложение Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). 
     - При создании приложения можно использовать любое имя и URL-адрес входа.
3. [Получение идентификатора приложения и ключ проверки подлинности](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key). 
     - Ключ проверки подлинности является секретом клиента и используется как AadClientSecret для Set-AzureRmVMDiskEncryptionExtension. 
        - Ключ проверки подлинности используется приложением в качестве учетной записи для входа в Azure AD. На портале Azure эти секреты называются ключами, но они не имеют никакого отношения к хранилищам ключей. Защитите этот секрет должным образом. 
     - Идентификатор приложения будет использоваться позже как AadClientId для Set-AzureRmVMDiskEncryptionExtension и как ServicePrincipalName для Set-AzureRmKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Настройка политики доступа к хранилищу ключей для приложения Azure AD
Для записи секретов шифрования в указанное хранилище ключей, шифрованию дисков Azure требуется идентификатор клиента и секрет клиента приложения Azure Active Directory, которое имеет соответствующие разрешения. 

> [!NOTE]
> Для шифрования дисков Azure требуется настроить в клиентском приложении Azure AD следующие политики доступа: разрешения _WrapKey_ и _Set_.

### <a name="bkmk_KVAPPSH"></a> Настройка политики доступа к хранилищу ключей для приложения Azure AD с помощью Azure PowerShell
Приложению Azure AD нужны права на доступ к ключам и секретам, которые размещены в хранилище. Используйте командлет [Set-AzureKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), чтобы предоставить приложению необходимые разрешения. Здесь в качестве значения параметра _–ServicePrincipalName_ следует использовать идентификатор клиента, созданный при регистрации приложения. Чтобы узнать больше, ознакомьтесь с записью блога [Azure Key Vault - Step by Ste](http://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx) (Пошаговая инструкция для Azure Key Vault). 

1. При необходимости [подключитесь к подписке Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Настройте политики доступа к хранилищу ключей для приложения AD с помощью PowerShell.

     ```azurepowershell-interactive
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $rgname = 'MySecureRG'
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
     ```

### <a name="bkmk_KVAPCLI"></a> Настройка политики доступа к хранилищу ключей для приложения Azure AD с помощью Azure CLI
Используйте [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault.md#az-keyvault-set-policy), чтобы настроить политику доступа. Дополнительные сведения см. в разделе [Управление Key Vault с помощью интерфейса командной строки 2.0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. При необходимости [подключитесь к подписке Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Предоставьте субъекту-службе, который вы создали через интерфейс Azure CLI, доступ к секретам и упакуйте ключи с помощью следующей команды:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> Настройка политики доступа к хранилищу ключей для приложения Azure AD с помощью портала

1. Откройте группу ресурсов, в которой содержится ваше хранилище ключей.
2. Выберите свое хранилище ключей, перейдите в раздел **Политики доступа**, а затем нажмите кнопку **Добавить новую**.
3. В разделе **Выбор субъекта** найдите созданное приложение Azure AD и выберите его. 
4. В колонке **Разрешения ключей** установите флажок **Упаковка ключа** в разделе **Операции шифрования**.
5. В колонке **Разрешения секретов** установите флажок **Установить** в разделе **Операции управления секретами**.
6. Нажмите кнопку **ОК**, чтобы сохранить политику доступа. 

![Криптографические операции Azure Key Vault — упаковка ключа](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Разрешения секретов Azure Key Vault — настройка ](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Установка политики расширенного доступа к хранилищу ключей
Платформа Azure должна иметь доступ к ключам шифрования или секретам, расположенным в вашем хранилище ключей, чтобы предоставлять их виртуальной машине при ее загрузке для расшифровки томов. Включите шифрование диска в хранилище ключей, иначе развертывание завершится сбоем.  

### <a name="bkmk_KVperPSH"></a> Установка политик расширенного доступа для хранилища ключей с помощью Azure PowerShell
 Используйте командлет PowerShell [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), чтобы включить шифрование диска для хранилища ключей.

  - **Включение хранилища ключей для шифрования диска:** для шифрования дисков Azure требуется использовать параметр EnabledForDiskEncryption.
      
     ```azurepowershell-interactive 
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDiskEncryption
     ```

  - **Включение хранилища ключей для развертывания (если необходимо):** позволяет поставщику ресурсов Microsoft.Compute извлекать секреты из этого хранилища ключей в случае использования ссылки на него при создании ресурса, например, виртуальной машины.

     ```azurepowershell-interactive
      Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDeployment
     ```

  - **Включение хранилища ключей для развертывания шаблона (если необходимо):** позволяет Azure Resource Manager получать секреты из этого хранилища ключей в случае использования ссылки на него при развертывании шаблона.

     ```azurepowershell-interactive             
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForTemplateDeployment`
     ```

### <a name="bkmk_KVperCLI"></a> Установка политик расширенного доступа для хранилища ключей с помощью Azure CLI
Включите шифрование дисков для хранилища ключей с помощью команды [az keyvault update](/cli/azure/keyvault#az-keyvault-update). 

 - **Включение хранилища ключей для шифрования диска:** требуется использовать параметр enabled-for-disk-encryption. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-disk-encryption "true"
     ```  

 - **Включение хранилища ключей для развертывания (если необходимо):** позволяет виртуальным машинам получать сертификаты, хранимые в виде секретов, из хранилища.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-deployment "true"
     ``` 

 - **Включение хранилища ключей для развертывания шаблона (если необходимо):** позволяет Resource Manager получать секреты из хранилища.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Установка политики расширенного доступа для хранилища ключей с помощью портала Azure

1. Выберите хранилище ключей, перейдите на вкладку **Политики доступа** и выберите **Щелкните, чтобы показать политики расширенного доступа**.
2. Установите флажок **Включить доступ к шифрованию дисков Azure для шифрования томов**.
3. Выберите **Включить доступ к виртуальным машинам Azure для развертывания** или **Включить доступ к Azure Resource Manager для развертывания шаблонов**, если это необходимо. 
4. Выберите команду **Сохранить**.

![Установка политики расширенного доступа к хранилищу Azure Key Vault](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Настройка ключа шифрования ключей (необязательно)
Если вы хотите использовать ключ шифрования ключей (KEK), чтобы добавить уровень безопасности ключей шифрования, поместите KEK в хранилище ключей. Используйте командлет [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey), чтобы создать ключ шифрования ключей в хранилище ключей. Кроме того, KEK можно импортировать из своего локального модуля HSM службы управления ключами. Дополнительные сведения см. в документации по [Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Когда ключ шифрования ключей указан, шифрование дисков Azure использует его для упаковки секретов шифрования перед записью в Key Vault. 

* Для URL-адресов секрета и ключа шифрования ключей (KEK) хранилища ключей необходимо включить управление версиями. Это требование Azure. Ниже приведены примеры действительных URL-адресов секрета и ключа шифрования ключей.

  * Пример допустимого URL-адреса секрета: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*.
  * Пример допустимого URL-адреса ключа шифрования ключей: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*.

* Шифрование дисков Azure не поддерживает указание номеров портов в URL-адресах секрета и ключа шифрования ключей для хранилища ключей. Ниже приведены примеры недопустимых и допустимых URL-адресов хранилища ключей.

  * Неприемлемый URL-адрес хранилища ключей: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*.
  * Приемлемый URL-адрес хранилища ключей: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*.

### <a name="bkmk_KEKPSH"></a> Настройка ключа шифрования ключей с помощью Azure PowerShell 
Перед использованием скрипта PowerShell нужно ознакомиться с предварительными требованиями к шифрованию дисков Azure, чтобы понять шаги. Для примера скрипта могут потребоваться изменения в вашей среде. Этот скрипт создает все необходимые компоненты для шифрования дисков Azure и шифрует существующую виртуальную машину IaaS, упаковывая ключ шифрования диска с помощью ключа шифрования ключей. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MySecureRG', and 'MySecureVault' with your values.
     # Use Get-AzureRmLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzureRmResourceGroup
     
     $Loc = 'MyLocation';
     $rgname = 'MySecureRG';
     $KeyVaultName = 'MySecureVault'; 
     New-AzureRmResourceGroup –Name $rgname –Location $Loc;
     New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc;
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $KeyVaultResourceId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption;
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname;
     
 #Step 4: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' with your value. 
     
     $VMName = 'MySecureVM';
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> Аутентификация на основе сертификата (необязательно)
Если вы хотите использовать аутентификацию на основе сертификата, вы можете загрузить его в свое хранилище и развернуть в клиенте. Перед использованием скрипта PowerShell нужно ознакомиться с предварительными требованиями к шифрованию дисков Azure, чтобы понять шаги. Для примера скрипта могут потребоваться изменения в вашей среде.

     
 ```powershell

 # Fill in "MySecureRG", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> Аутентификация на основе сертификата и KEK (необязательно)

Если вы хотите использовать аутентификацию на основе сертификата и упаковать ключ шифрования с помощью KEK, вы можете использовать приведенный ниже скрипт в качестве примера. Перед использованием скрипта PowerShell нужно ознакомиться со всеми предыдущими предварительными требованиями к шифрованию дисков Azure, чтобы понять шаги. Для примера скрипта могут потребоваться изменения в вашей среде.

> [!IMPORTANT]
> В настоящее время аутентификация на основе сертификата Azure AD на виртуальных машинах Linux не поддерживается.



     
 ```powershell
# Fill in 'MySecureRG', 'MySecureVault', and 'MyLocation' (if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Включение шифрования дисков Azure для виртуальных машин Windows IaaS](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Включение шифрование дисков Azure для Linux](azure-security-disk-encryption-linux-aad.md)
