---
title: Запуск проверочного теста в Azure Stack | Документация Майкрософт
description: Сведения о том, как собирать файлы журналов для диагностики в Azure Stack.
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: a70c736489b25f6e8fd0d838c4c7b4b4db96a4f2
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188264"
---
# <a name="run-a-validation-test-for-azure-stack"></a>Запуск проверочного теста в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*
 
Вы можете проверить состояние Azure Stack. Если возникнет проблема, обратитесь в службу поддержки пользователей Майкрософт. Специалист службы поддержки попросит вас выполнить командлет **Test-AzureStack** из узла управления. Проверочный тест выявит ошибку. Затем служба поддержки проанализирует подробные журналы, обращая внимание на область, в которой произошла ошибка, и вместе с вами попробует устранить проблему.

## <a name="run-test-azurestack"></a>Выполнение Test-AzureStack

Если возникнет проблема, обратитесь в службу поддержки пользователей Майкрософт, а затем выполните командлет **Run Test-AzureStack**.

1. У вас проблема.
2. Обратитесь в службу поддержки пользователей Майкрософт.
3. Выполните командлет **Test-AzureStack** из привилегированной конечной точки.
    1. Получите доступ к привилегированной конечной точке. См. инструкции по [использованию привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md). 
    2. В ASDK войдите в узел управления как **AzureStack\CloudAdmin**.  
    В интегрированной системе потребуется использовать IP-адрес привилегированной конечной точки для управления, предоставленного поставщиком оборудования OEM.
    3. Откройте PowerShell от имени администратора.
    4. Выполните команду `Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. Выполните команду `Test-AzureStack`
4. Если все тесты сообщают об ошибках, выполните этот командлет: `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` Он собирает журналы выполнения Test-AzureStack. Дополнительные сведения о журналах диагностики см. в статье [Средства диагностики Azure Stack](azure-stack-diagnostics.md).
5. Отправьте журналы **SeedRing** в службу поддержки пользователей Майкрософт. Служба поддержки Майкрософт совместно с вами постарается устранить проблему.

## <a name="reference-for-test-azurestack"></a>Справочник по Test-AzureStack

Этот раздел содержит общие сведения о командлете Test-AzureStack и сводку отчета о проверке.

### <a name="test-azurestack"></a>Test-AzureStack

Этот командлет проверяет состояние Azure Stack. Командлет сообщает о состоянии оборудования и программного обеспечения Azure Stack. Этот отчет поможет персоналу службы поддержки быстрее устранить проблемы, связанные с Azure Stack.

> [!Note]  
> Командлет **Test-AzureStack** может обнаруживать сбои, которые не приводят к сбоям в облаке, например сбой одного диска или одного физического узла.

#### <a name="syntax"></a>Синтаксис

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>Параметры

| Параметр               | Значение           | Обязательно | значение по умолчанию |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | Нет        | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | Нет        | FALSE   |
| AdminCredential         | PSCredential    | Нет        | Нет данных      |
| список                    | SwitchParameter | Нет        | FALSE   |
| Игнорировать                  | Строка          | Нет        | Нет данных      |
| Включить                 | Строка          | Нет        | Нет данных      |
| BackupSharePath         | Строка          | Нет        | Нет данных      |
| BackupShareCredential   | PSCredential    | Нет        | Нет данных      |


Командлет Test-AzureStack поддерживает общие параметры: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, OutBuffer, PipelineVariable и OutVariable. См. дополнительные сведения об [общих параметрах](http://go.microsoft.com/fwlink/?LinkID=113216). 

### <a name="examples-of-test-azurestack"></a>Примеры Test-AzureStack

В следующих примерах предполагается, вы вошли как **CloudAdmin** и у вас есть доступ к привилегированной конечной точке. См. инструкции по [использованию привилегированной конечной точки в Azure Stack](azure-stack-privileged-endpoint.md). 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>Интерактивный запуск Test-AzureStack без облачных сценариев

В сеансе привилегированной конечной точки выполните следующую команду:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>Запуск Test-AzureStack с облачными сценариями

С помощью командлета **Test-AzureStack** можно выполнять облачные сценарии в Azure Stack. Ниже приведены соответствующие сценарии.

 - Создание группы ресурсов.
 - Создание планов.
 - Создание предложений.
 - Создание учетных записей хранения.
 - Создание виртуальной машины.
 - Выполнение операций в большом двоичном объекте с помощью учетной записи хранения, созданной в сценарии тестирования.
 - Выполнение операций в очереди с помощью учетной записи хранения, созданной в сценарии тестирования.
 - Выполнение операций в таблице с помощью учетной записи хранения, созданной в сценарии тестирования.

Для выполнения облачных сценариев требуются учетные данные администратора облака. 
> [!Note]  
> Вы не можете запускать сценарии в облаке с помощью учетных данных службы федерации Active Directory (AD FS). Командлет **Test-AzureStack** можно запустить только из привилегированной конечной точки. Но эта конечная точка не поддерживает учетные данные AD FS.

Введите имя администратора облака в формате имени участника-пользователя serviceadmin@contoso.onmicrosoft.com (Azure AD). При появлении запроса введите пароль учетной записи администратора облака.

В сеансе привилегированной конечной точки выполните следующую команду:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>Запуск Test-AzureStack без облачных сценариев

В сеансе привилегированной конечной точки выполните следующую команду:

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>Список доступных сценариев теста

В сеансе привилегированной конечной точки выполните следующую команду:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>Выполнение конкретного теста

В сеансе привилегированной конечной точки выполните следующую команду:

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

Чтобы исключить определенные тесты, выполните эту команду:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>Выполнение командлета Test-AzureStack для тестирования параметров резервного копирования инфраструктуры

Перед настройкой резервного копирования инфраструктуры вы можете протестировать путь к общему ресурсу резервных копий и учетные данные с помощью теста **AzsBackupShareAccessibility**.

В сеансе привилегированной конечной точки выполните следующую команду:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
После настройки резервного копирования вы можете выполнить команду AzsBackupShareAccessibility для проверки доступа к общему ресурсу из ERCS. В сеансе PEP выполните следующую команду:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

Чтобы проверить новые учетные данные с настроенным общим ресурсом резервных копий, в сеансе PEP выполните следующую команду:

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>Проверочный тест

В следующей таблице перечислены проверочные тесты, выполняемые с помощью командлета **Test-AzureStack**.

| ИМЯ                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Сводка по инфраструктуре, размещающей облако Azure Stack                                                                                  |
| Сводка по службам хранилища Azure Stack                                                                                              |
| Сводка по экземпляру роли инфраструктуры Azure Stack                                                                                  |
| Использование инфраструктуры, размещающей облако Azure Stack                                                                              |
| Производительность инфраструктуры Azure Stack                                                                                               |
| Сводка по API и порталу Azure Stack                                                                                                |
| Сводка по сертификату Azure Resource Manager для Azure Stack                                                                                               |
| Роли контроллера управления инфраструктурой, сетевого контроллера, службы хранилища и инфраструктуры привилегированной конечной точки          |
| Экземпляры роли контроллера управления инфраструктурой, сетевого контроллера, службы хранилища и инфраструктуры привилегированной конечной точки |
| Сводка по роли инфраструктуры Azure Stack                                                                                           |
| Облачные службы Service Fabric Azure Stack                                                                                         |
| Производительность экземпляра роли инфраструктуры Azure Stack                                                                              |
| Сводка по производительности узлов в облаке Azure Stack                                                                                        |
| Сводка по потреблению ресурсов службы Azure Stack                                                                                  |
| Критические события единицы масштабирования Azure Stack (за последние 8 часов)                                                                             |
| Сводка по физическим дискам служб хранилища Azure Stack                                                                               |
|Сводка по доступу к общему ресурсу резервных копий Azure Stack                                                                                     |

## <a name="next-steps"></a>Дополнительная информация

 - Дополнительные сведения о средствах диагностики Azure Stack и ведении журналов проблем см. в статье [Средства диагностики Azure Stack](azure-stack-diagnostics.md).
 - Дополнительные сведения об устранении неполадок см. в статье [Устранение неполадок, связанных с Microsoft Azure Stack](azure-stack-troubleshooting.md).
