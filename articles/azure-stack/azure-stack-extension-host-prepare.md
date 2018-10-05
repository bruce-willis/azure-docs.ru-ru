---
title: Подготовка хост-процесса для расширений для Azure Stack | Документация Майкрософт
description: Сведения о подготовке хост-процесса для расширений с автоматическим включением с помощью будущего пакета обновления Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 09/26/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.openlocfilehash: 3e35b0d9ba697b54b0fb85096caceeaa024b1f3d
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47405246"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Подготовка хост-процесса для расширений для Azure Stack

Хост-процесс для расширений защищает Azure Stack путем уменьшения количества необходимых портов TCP/IP. В этой статье рассматривается подготовка Azure Stack для хост-процесса для расширений, который автоматически включается с помощью пакета обновления Azure Stack более поздней версии, чем 1808.

## <a name="certificate-requirements"></a>Требования к сертификатам

Хост-процесс для расширений реализует два новых пространства имен доменов, чтобы гарантировать уникальные записи узла для каждого расширения портала. Для обеспечения безопасного обмена данными новым доменным пространствам имен требуются два дополнительных групповых сертификата.

В таблице показаны новые пространства имен и связанные с ними сертификаты:

| Папка развертывания | Требуемый субъект сертификата и альтернативное имя субъекта | Область (для каждого региона) | Пространство имен поддомена |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Хост-процесс для расширений администратора | *.adminhosting.\<регион>.\<FQDN> (групповые SSL-сертификаты) | Хост-процесс для расширений администратора | adminhosting.\<регион>.\<FQDN> |
| Общедоступный хост-процесс для расширений | *.hosting.\<регион>.\<FQDN> (групповые SSL-сертификаты) | Общедоступный хост-процесс для расширений | hosting.\<регион>.\<FQDN> |

Подробные требования к сертификатам можно найти в статье [Требования к сертификатам инфраструктуры открытых ключей Azure Stack](azure-stack-pki-certs.md).

## <a name="create-certificate-signing-request"></a>Создание запроса на подпись сертификата

Инструмент проверки готовности Azure Stack предоставляет возможность создать запрос на подпись сертификата для двух новых обязательных SSL-сертификатов. Выполните действия, описанные в статье [Создание запроса на подпись сертификата Azure Stack](azure-stack-get-pki-certs.md).

> [!Note]  
> Можно пропустить этот шаг в зависимости от того, как вы запросили SSL-сертификаты.

## <a name="validate-new-certificates"></a>Проверка новых сертификатов

1. Откройте сеанс PowerShell с повышенными правами на узле жизненного цикла аппаратного обеспечения или рабочей станции управления Azure Stack.
2. Запустите следующий командлет, чтобы установить инструмент проверки готовности Azure Stack.

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Выполните следующий сценарий, чтобы создать нужную структуру папки:

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > При развертывании с помощью служб федерации Azure Active Directory (AD FS) каталоги ниже должны быть добавлены в **$directories** в сценарии `ADFS`, `Graph`.

4. Выполните следующие командлеты для запуска проверки сертификата:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD -ExtensionHostFeature
    ```

5. Поместите сертификаты в соответствующие каталоги.

6. Проверьте выходные данные. Все сертификаты должны пройти все проверки.


## <a name="import-extension-host-certificates"></a>Импорт сертификатов хост-процесса для расширений

Для выполнения следующих шагов используйте компьютер, который может подключиться к привилегированной конечной точке Azure Stack. Обеспечьте доступ к новым файлам сертификатов с этого компьютера.

1. Для выполнения следующих шагов используйте компьютер, который может подключиться к привилегированной конечной точке Azure Stack. Проверьте доступ к новым файлам сертификатов с этого компьютера.
2. Откройте интегрированную среду сценариев PowerShell, чтобы выполнить следующие блоки сценария.
3. Импортируйте сертификат для размещения конечной точки. Измените сценарий в соответствии со своей средой.
4. Импортируйте сертификат для конечной точки размещения администратора.

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
5. Импортируйте сертификат для конечной точки размещения.
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```



### <a name="update-dns-configuration"></a>Обновление конфигурации DNS

> [!Note]  
> Этот шаг не является обязательным, если вы использовали делегирование зоны DNS для интеграции DNS.
Если записи A отдельного узла были настроены для публикации конечных точек Azure Stack, вам нужно создать две дополнительные записи A узла:

| IP-адрес | имя узла; | type |
|----|------------------------------|------|
| \<IP> | Adminhosting.<Region>.<FQDN> | A |
| \<IP> | Hosting.<Region>.<FQDN> | A |

Выделенные IP-адреса можно получить с помощью привилегированной конечной точки, выполнив командлет **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Порты и протоколы

В статье [Интеграция центра обработки данных Azure Stack. Публикация конечных точек](azure-stack-integrate-endpoints.md) описываются порты и протоколы, которым для публикации Azure Stack перед развертыванием хост-процесса для расширений требуется входящая связь.

### <a name="publish-new-endpoints"></a>Публикация новых конечных точек

Есть две новые конечные точки, которые необходимо опубликовать через брандмауэр. Выделенные IP-адреса из общедоступного пула виртуальных IP-адресов можно получить, выполнив командлет **Get-AzureStackStampInformation**.

> [!Note]  
> Внесите это изменение, а затем включите хост-процесс для расширений. Это обеспечит непрерывную доступность порталов Azure Stack.

| Конечная точка (виртуальный IP-адрес) | Протокол | порты; |
|----------------|----------|-------|
| AdminHosting | HTTPS | 443 |
| Hosting | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Обновление имеющихся правил публикации (после включения хост-процесса для расширений)

> [!Note]  
> Включение хост-процесса для расширений в пакете обновления Azure Stack 1808 пока **недоступно**. Таким образом, вы можете подготовиться для хост-процесса для расширений, импортировав требуемые сертификаты. Порты можно закрыть только после автоматического включения хост-процесса для расширений с помощью пакета обновления Azure Stack версии, вышедшей после обновления 1808.

В имеющихся правилах брандмауэра нужно закрыть следующие порты конечных точек.

> [!Note]  
> Эти порты рекомендуется закрыть после успешной проверки.

| Конечная точка (виртуальный IP-адрес) | Протокол | порты; |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Портал (для администратора) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13020<br>13021<br>13026<br>30015 |
| Портал (для пользователя) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003 |
| Azure Resource Manager (для администратора) | HTTPS | 30024 |
| Azure Resource Manager (для пользователя) | HTTPS | 30024 |

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь со сведениями об [интеграции брандмауэра](azure-stack-firewall.md).
- Ознакомьтесь со сведениями о [создании запроса на подпись сертификата Azure Stack](azure-stack-get-pki-certs.md).
