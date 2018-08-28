---
title: Развертывание локального агента и тестирование виртуальных машин, созданных на основе образа, в рамках проверки как услуги Azure Stack | Документация Майкрософт
description: Разверните локальный агент и тестирование виртуальных машин, созданных на основе образа, в рамках проверки как услуги Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 9d32c23f66563988d023df3bf6a33efa30237e57
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234157"
---
# <a name="deploy-the-local-agent-and-test-virtual-machines"></a>Развертывание локального агента и тестирование виртуальных машин

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Узнайте, как использовать локальный агент модели "проверка как услуга" для проверки оборудования. Локальный агент необходимо развернуть в решении Azure Stack, которое проверяется до выполнения проверочных тестов.

> [!Note]  
> Необходимо убедиться в том, что компьютер, на котором выполняется локальный агент, не потеряет доступ к Интернету. Компьютер должен быть доступен только тем пользователям, которые имеют право использовать Azure Stack VaaS.

Чтобы проверить свои виртуальные машины:

1. Установите локальный агент.
2. Внедрите сбои в систему.
3. Запустите локальный агент.

## <a name="download-and-start-the-local-agent"></a>Скачивание и запуск локального агента

Скачайте агент на компьютер, соответствующий требованиям, в центре обработки данных, который не является частью системы Azure Stack, но имеет доступ ко всем конечным точкам Azure Stack.

### <a name="machine-prerequisites"></a>Предварительные требования к компьютерам

Убедитесь, что ваш компьютер соответствует следующим условиям:

- доступ ко всем конечным точкам Azure Stack;
- установка .NET 4.6 и PowerShell 5.0;
- не менее 8 ГБ ОЗУ;
- минимум 8-ядерные процессоры;
- не менее 200 ГБ дискового пространства;
- постоянное сетевое подключение к Интернету.

Azure Stack является тестируемой системы. Компьютер не должен быть частью Azure Stack или располагаться в облаке Azure Stack.

### <a name="download-and-install-the-agent"></a>Загрузка и установка агента

1. Откройте Windows PowerShell в командной строке с повышенными привилегиями на компьютере, который будет использоваться для выполнения тестов.
2. Выполните следующую команду, чтобы скачать локальный агент:

    ```PowerShell  
        Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.3.2.0.nupkg" -outfile "OnPremAgent.zip"
        Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent.3.2.0 -Force
        Set-Location VaaSOnPremAgent.3.2.0\lib\net46
    ````

3. Выполните следующую команду, чтобы установить зависимости локального агента:

    ```PowerShell  
        $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-VaaSPrerequisites -AadTenantId <AadTenantId> `
        -ServiceAdminCreds <ServiceAdminCreds> `
        -ArmEndpoint https://adminmanagement.<ExternalFqdn> `
        -Region <Region>
    ````

    **Параметры**

    | Параметр | ОПИСАНИЕ |
    | --- | --- |
    | aadServiceAdminUser | Глобальный администратор для клиента Azure AD. Например, это может быть vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Пароль для пользователя с правами администратора. |
    | AADTenantId | Идентификатор клиента Azure AD для учетной записи Azure, зарегистрированный с помощью проверки как услуги. |
    | ExternalFqdn | Полное доменное имя можно получить из файла конфигурации. Инструкции см. в статье [Test parameters for validation as a service Azure Stack](azure-stack-vaas-parameters-test.md) (Тестовые параметры для Azure Stack модели "проверка как услуга"). |
    | Регион | Регион вашего клиента Azure AD. |

Команда скачивает образ общедоступного репозитория образов (VHD операционной системы) и выполняет копирование из хранилища BLOB-объектов Azure в хранилище Azure Stack. 

![Скачивание необходимых компонентов](media/installingprereqs.png)

> [!Note]  
> Если при скачивании этих образов скорость низкая, скачайте их по отдельности в локальный общий ресурс и укажите параметры **-LocalPackagePath** *FileShareOrLocalPath*. Дополнительные рекомендации по скачиванию общедоступного репозитория образов см. в разделе [Обработка медленного сетевого подключения](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) статьи [Troubleshoot validation as a service](azure-stack-vaas-troubleshoot.md) (Устранение ошибок проверки как услуги).

## <a name="fault-injection"></a>Внедрение ошибок

Корпорация Майкрософт разработала Azure Stack для обеспечения отказоустойчивости и для допуска нескольких типов ошибок программного обеспечения и оборудования. Внесение ошибок увеличивает число сбоев в системе. Это увеличение помогает раньше выявить проблемы, таким образом можно уменьшить количество инцидентов, которые приводят к отказу системы.

Выполните следующие команды для внедрения ошибок в систему.

1. Откройте Windows PowerShell из командной строки с повышенными привилегиями.

2. Выполните следующую команду:

    ```PowerShell  
        Import-Module .\VaaSPreReqs.psm1 -Force
        Install-ServiceFabricSDK Install-ServiceFabricSDK
    ```

## <a name="run-the-agent"></a>Запуск агента

1. Откройте Windows PowerShell из командной строки с повышенными привилегиями.

2. Выполните следующую команду:

    ````PowerShell  
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ````

      **Параметры**  
    
    | Параметр | ОПИСАНИЕ |
    | --- | --- |
    | VaaSUserId | Идентификатор пользователя, используемый для входа на портал VaaS (например, UserName@Contoso.com). |
    | VaaSTenantId | Идентификатор клиента Azure AD для учетной записи Azure, зарегистрированный с помощью проверки как услуги. |

    > [!Note]  
    > При запуске агента текущий рабочий каталог должен быть расположением исполняемого файла узла обработчика задач **Microsoft.VaaSOnPrem.TaskEngineHost.exe.**

Если вы не видите сообщения об ошибках, локальный агент был успешно выполнен. Следующий пример текста отображается в окне консоли.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Запущенный агент](media/startedagent.png)

Агент однозначно идентифицируется по имени. По умолчанию используется полное доменное имя (FQDN) компьютера, на котором он был запущен. Уменьшите окно, чтобы избежать случайных щелчков по нему, так как изменение фокуса приостанавливает остальные действия.

## <a name="next-steps"></a>Дополнительная информация

- [Проверка нового решения Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Если у вас медленное или нестабильное подключение к Интернету, можно скачать образ общедоступного репозитория образов. Дополнительные сведения см. в разделе [Обработка медленного сетевого подключения](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).
- Дополнительные сведения см. в статье [Документация партнера по Azure Stack](https://docs.microsoft.com/azure/azure-stack/partner).
