---
title: Предварительные требования для шифрования дисков Azure | Документация Майкрософт
description: Эта статья содержит предварительные требования для использования шифрования дисков Microsoft Azure с виртуальными машинами IaaS.
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: 1b2daefc-1326-44dd-9c8b-10e413769af7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: mstewart
ms.openlocfilehash: d248a97235ead134f29e468aaafcd04211590e02
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43247496"
---
# <a name="azure-disk-encryption-prerequisites"></a>Предварительные требования для шифрования дисков Azure 
 В этой статье объясняются компоненты, которые должны быть установлены до того, как вы сможете использовать шифрование дисков Azure. Шифрование дисков Azure интегрируется с [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), обеспечивая управление ключами шифрования. Для настройки шифрования дисков Azure можно использовать [Azure PowerShell](/powershell/azure/overview), [Azure CLI](/cli/azure/) или [портал Azure](https://portal.azure.com).

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

2. Проверьте версии установленных модулей AzureRM. При необходимости [обновите модуль Azure PowerShell](/powershell/azure/install-azurerm-ps#update-the-azure-powershell-module).
    -  Версия модуля AzureRM должна быть не ниже 6.0.0.
    - Рекомендуется использовать последнюю версию модуля AzureRM.

     ```powershell
     Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Войдите в Azure, используя командлет [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount).
     
     ```azurepowershell-interactive
     Connect-AzureRmAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzureRmSubscription and 
     specify it with Set-AzureRmContext.  #>
     Get-AzureRmSubscription
     Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  При необходимости ознакомьтесь со статьей [Начало работы с Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a>Установка Azure CLI для использования на вашем локальном компьютере (необязательно)

[Azure CLI 2.0](/cli/azure) — это интерфейс командной строки для управления ресурсами Azure. Этот интерфейс обеспечивает гибкие функции подачи запросов, выполнение длительных операций без блокировки и простое создание скриптов. Его можно использовать в браузере с [Azure Cloud Shell](../cloud-shell/overview.md), а также установить на локальном компьютере и использовать в любом сеансе PowerShell.

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

4. При необходимости ознакомьтесь со статьей [Начало работы с Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 


## <a name="prerequisite-workflow-for-key-vault"></a>Необходимый рабочий процесс для Key Vault
Если вы уже знакомы с предварительными требованиями Key Vault и Azure AD для шифрования дисков Azure, можно использовать [скрипт PowerShell предварительных требований к шифрованию дисков Azure](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Дополнительные сведения об использовании скрипта предварительных требований см. в [кратком руководстве по шифрованию виртуальных машин](quick-encrypt-vm-powershell.md) и [приложении шифрования дисков Azure](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. При необходимости создайте группу ресурсов.
2. Создать хранилище ключей. 
3. Установите политики расширенного доступа к хранилищу ключей.
 
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
2. Чтобы создать группу ресурсов, используйте команду [az group create](/cli/azure/group#az-group-create). Для перечисления расположений используйте команду [az account list-locations](/cli/azure/account#az-account-list). 
     
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

 - **Включение хранилища ключей для развертывания (если необходимо):** позволяет поставщику ресурсов Microsoft.Compute извлекать секреты из этого хранилища ключей в случае использования ссылки на него при создании ресурса, например, виртуальной машины.

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
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' with your value. 
     
     $VMName = 'MySecureVM';
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Включение шифрования дисков Azure для виртуальных машин Windows IaaS](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Включение шифрование дисков Azure для Linux](azure-security-disk-encryption-linux.md)

