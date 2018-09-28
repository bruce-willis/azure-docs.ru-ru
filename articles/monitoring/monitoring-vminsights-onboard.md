---
title: Подключение Azure Monitor для виртуальных машин | Документация Майкрософт
description: В этой статье объясняется, как подключить и настроить Azure Monitor для виртуальных машин, чтобы получать сведения о производительности распределенного приложения и обнаруживать проблемы работоспособности.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: e5421ca791ae9d0059639000f33b77be57f4d891
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46968039"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Как подключить Azure Monitor для виртуальных машин 
В этой статье объясняется, как настроить Azure Monitor для виртуальных машин, чтобы отслеживать работоспособность операционной системы на виртуальных машинах Azure, обнаруживать и сопоставлять зависимости размещенных на них приложений.  

Включить Azure Monitor для виртуальных машин можно одним из следующих методов. Сведения о каждом из них приведены далее в этой статье.  

* Для одной виртуальной машины Azure через страницу **Аналитические сведения (предварительная версия)** непосредственно на виртуальной машине.
* Для нескольких виртуальных машин Azure с помощью Политики Azure, чтобы существующие и новые виртуальные машины имели все необходимые зависимости и были правильно настроены.  Вы получите отчеты о всех виртуальных машинах, которые не соответствуют требованиям, что позволит вам принять решение о методах устранения обнаруженных несоответствий.  
* Для многих виртуальных машин Azure или масштабируемого набора виртуальных машин в определенной подписке или группе ресурсов с помощью PowerShell.

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы необходимо выполнить все предварительные условия, описанные в следующих подразделах.

### <a name="log-analytics"></a>Log Analytics 

Сейчас рабочая область Log Analytics поддерживается в следующих регионах:

  - Западно-центральная часть США  
  - Восточная часть США  
  - Западная Европа  
  - Юго-Восточная Азия<sup>1</sup>  

<sup>1</sup> Этот регион в настоящее время не поддерживает функцию мониторинга работоспособности в Azure Monitor для виртуальных машин.   

Если у вас нет рабочей области, вы можете создать ее с помощью [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json) или [портала Azure](../log-analytics/log-analytics-quick-create-workspace.md).  

Чтобы включить это решение, необходимо быть членом роли участника Log Analytics. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>Поддерживаемые операционные системы

Azure Monitor для виртуальных машин официально поддерживает следующие версии операционных систем Windows и Linux:

|Версия ОС |Производительность |Карты |Health |  
|-----------|------------|-----|-------|  
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|RHEL 7, 6| X | X| X |  
|Ubuntu 18.04, 16.04, 14.04 | X | X| X |  
|Cent OS Linux 7, 6 | X | X| X |  
|SLES 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4, 8 | X<sup>1</sup> | | X | 

<sup>1</sup> Функция производительности в Azure Monitor для виртуальных машин доступна только через Azure Monitor, но не при входе напрямую на виртуальную машину Azure через панель слева.  

### <a name="hybrid-environment-connected-sources"></a>Подключенные источники в гибридной среде
Функция схемы в Azure Monitor для виртуальных машин получает данные от Microsoft Dependency Agent. Dependency Agent является зависимым от агента Log Analytics для создания подключений к Log Analytics. Это означает, что в системе нужно установить агент Log Analytics и настроить для него Dependency Agent.  В приведенной ниже таблице описаны подключенные источники, которые функция схемы поддерживает в гибридной среде.

| Подключенный источник | Поддерживаются | ОПИСАНИЕ |
|:--|:--|:--|
| Агенты Windows | Yes | Кроме [агента Log Analytics для Windows](../log-analytics/log-analytics-concept-hybrid.md) агентам Windows требуется Microsoft Dependency Agent. Полный список версий операционных систем см. в разделе [Поддерживаемые операционные системы](#supported-operating-systems). |
| Агенты Linux | Yes | Кроме [агента Log Analytics для Linux](../log-analytics/log-analytics-concept-hybrid.md) агентам Linux требуется Microsoft Dependency Agent. Полный список версий операционных систем см. в разделе [Поддерживаемые операционные системы](#supported-operating-systems). |
| Группа управления System Center Operations Manager | Нет  | |  

В ОС Windows System Center Operations Manager и Log Analytics используют Microsoft Monitoring Agent для сбора и отправки данных мониторинга. System Center Operations Manager и Log Analytics предоставляют в базовой конфигурации разные версии агента. В каждой из этих версий предусмотрена возможность отправлять отчеты в System Center Operations Manager, Log Analytics или в оба решения.  

В ОС Linux агент Log Analytics для Linux собирает и отправляет данные мониторинга в Log Analytics.   

Если компьютеры Windows или Linux не могут подключиться напрямую к службе, необходимо настроить агент Log Analytics таким образом, чтобы он подключался к Log Analytics через шлюз OMS. Дополнительные сведения о развертывании и настройке шлюза OMS см. в разделе [Подключения компьютеров с помощью шлюза OMS без доступа к Интернету](../log-analytics/log-analytics-oms-gateway.md).  

Dependency Agent можно скачать из следующего расположения.

| Файл | ОС | Version (версия) | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.1 | 09D56EF43703A350FF586B774900E1F48E72FE3671144B5C99BB1A494C201E9E |

## <a name="diagnostic-and-usage-data"></a>Данные диагностики и использования
Корпорация Майкрософт автоматически собирает данные об использовании и производительности при работе службы Azure Monitor. Эти данные используются, чтобы поддерживать и улучшать качество, безопасность и целостность службы. Чтобы предоставить возможности для точного и эффективного устранения неполадок, данные функции схемы включают в себя сведения о конфигурации программного обеспечения, такие как версия операционной системы, а также IP-адрес, DNS-имя и имя рабочей станции. Корпорация Майкрософт не собирает сведения об именах, адресах и другую контактную информацию.

Дополнительные сведения о сборе и использовании данных см. в [заявлении о конфиденциальности служб Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>Включение счетчиков производительности
Azure Monitor для виртуальных машин настраивает сбор данных от счетчиков производительности, используемых решением, в рабочей области Log Analytics.  В следующей таблице перечислены объекты и счетчики, которые настраиваются решением и опрашиваются каждые 60 секунд.

### <a name="windows-performance-counters"></a>Счетчики производительности Windows

|Имя объекта |Имя счетчика |  
|------------|-------------|  
|Логический диск |Процент свободного места |  
|Логический диск |Среднее время чтения с диска (с) |  
|Логический диск |Среднее время обращения к диску (с) |  
|Логический диск |Среднее время записи на диск (с) |  
|Логический диск |Байт в секунду для диска |  
|Логический диск |Скорость чтения с диска (байт/с)  |  
|Логический диск |Операций чтения с диска в секунду  |  
|Логический диск |Обращений к диску в секунду |  
|Логический диск |Скорость записи на диск (байт/с) |  
|Логический диск |Операций записи на диск в секунду |  
|Логический диск |Свободно мегабайт |  
|Память |Доступный объем в МБ |  
|Сетевой адаптер |Полученных байтов/с |  
|Сетевой адаптер |Отправленных байтов/с |  
|Процессор |% загруженности процессора |  

### <a name="linux-performance-counters"></a>Счетчики производительности Linux

|Имя объекта |Имя счетчика |  
|------------|-------------|  
|Логический диск |Процент используемого места |  
|Логический диск |Скорость чтения с диска (байт/с)  |  
|Логический диск |Операций чтения с диска в секунду  |  
|Логический диск |Обращений к диску в секунду |  
|Логический диск |Скорость записи на диск (байт/с) |  
|Логический диск |Операций записи на диск в секунду |  
|Логический диск |Свободно мегабайт |  
|Логический диск |Скорость обмена с логическим диском (байт/с) |  
|Память |Доступный объем памяти в МБ |  
|Сеть |Всего получено байт |  
|Сеть |Всего передано байт |  
|Процессор |% загруженности процессора |  

## <a name="enable-from-the-azure-portal"></a>Включение на портале Azure
Чтобы включить мониторинг виртуальной машины на портале Azure, сделайте следующее.

1. На портале Azure выберите **Виртуальные машины**. 
2. Выберите виртуальную машину из списка. 
3. На странице виртуальной машины в разделе **Мониторинг** выберите **Аналитические сведения (предварительная версия)**.
4. На странице **Аналитические сведения (предварительная версия)** выберите **Попробовать**.

    ![Включение Azure Monitor для виртуальной машины](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. Если имеющаяся рабочая область Log Analytics расположена в той же подписке, что и кластер, выберите ее из раскрывающегося списка на странице **Azure Monitor Insights Onboarding** (Подключение к службе аналитических сведений Azure Monitor).  В списке предварительно выбрана рабочая область по умолчанию и расположение, в которое развертывается виртуальная машина в подписке. 

    >[!NOTE]
    >Если вы хотите создать новую рабочую область Log Analytics для хранения данных мониторинга от виртуальной машины, выполните инструкции в статье [Создание рабочей области Log Analytics портале Azure](../log-analytics/log-analytics-quick-create-workspace.md). Создайте рабочую область в той же подписке, где развернута виртуальная машина. 

После включения мониторинга может потребоваться около 10 минут, прежде чем вы сможете просмотреть метрики работоспособности для виртуальной машины. 

![Включение Azure Monitor для обработки развертывания мониторинга виртуальных машин](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Включение при помощи Политики Azure
Чтобы включить для нескольких виртуальных машин Azure решение, которое обеспечивает согласованную совместимость и автоматическое включение при подготовке новых виртуальных машин, мы рекомендуем использовать [Политику Azure](../azure-policy/azure-policy-introduction.md).  Применение предлагаемых политик в Политике Azure дает следующие преимущества для новых виртуальных машин:

* включение Azure Monitor для виртуальных машин на каждой виртуальной машине в определенной области;
* развертывание агента Log Analytics; 
* развертывание Dependency Agent для обнаружения зависимостей приложений и отображения их на схеме;
* проверка наличия образа ОС виртуальной машины Azure в предварительно определенном списке в определении политики;  
* проверка входа виртуальных машин Azure в рабочие области, отличные от указанной;
* отчет о результатах проверки на соответствие; 
* поддержка исправлений для несоответствующих виртуальных машин.

Чтобы активировать этот процесс для клиента, выполните следующее:

- настройте рабочую области Log Analytics с помощью описанных здесь шагов;
- импортируйте определение инициативы на ваш клиента (на уровне группы управления или подписки);
- назначьте политику для требуемой области;
- просмотрите результаты проверки на соответствие;

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>Добавление политики и инициативы в подписку
Чтобы использовать эти политики, выполните предоставленный скрипт PowerShell [Add-VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2), который создан специально для этой задачи и размещен в коллекции Azure PowerShell. Этот скрипт добавляет в подписку политики и инициативы.  Выполните следующие действия, чтобы настроить в подписке Политику Azure. 

1. Скачайте скрипт PowerShell в локальную файловую систему.

2. Выполните в этой папке следующую команду PowerShell, чтобы добавить политики. Скрипт поддерживает следующие необязательные параметры: 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >Примечание. Если вы планируете назначить инициативу или политику нескольким подпискам, определения следует сохранить в группе управления, которая содержит все соответствующие подписки. Это означает, что нужно использовать параметр -ManagementGroupID.
    >
   
    Пример выполнения без параметров: `.\Add-VMInsightsPolicy.ps1`.

### <a name="create-a-policy-assignment"></a>Создание назначения политики
Когда скрипт PowerShell `Add-VMInsightsPolicy.ps1` будет выполнен, добавьте следующие инициативы и политики:

* **развертывание агента Log Analytics для виртуальных машин Windows — предварительная версия**;
* **развертывание агента Log Analytics для виртуальных машин Linux — предварительная версия**;
* **развертывание Dependency Agent для виртуальных машин Windows — предварительная версия**;
* **развертывание Dependency Agent для виртуальных машин Linux — предварительная версия**;
* **аудит развертывания агента Log Analytics — образ (ОС) виртуальной машины отсутствует в списке — предварительная версия**;
* **аудит развертывания Dependency Agent — образ (ОС) виртуальной машины отсутствует в списке — предварительная версия**.

Добавляется следующий параметр инициативы:

- **рабочая область Analytice log** (вы должны предоставить идентификатор ресурса для рабочей области, если назначение выполняется с помощью PowerShell или CLI).

    Из виртуальных машин, которые политики аудита **VMs not in OS scope...** (Виртуальные машины не в области действия ОС...) признают несоответствующими, условия политики развертывания включают только те виртуальные машины, которые развернуты из хорошо известных образов виртуальных машин Azure. Поддержку ОС виртуальных машин можно проверить в документации.  Если ОС не поддерживается, необходимо создать копию политики развертывания и изменить ее так, чтобы образ оказался в области действия.

Добавляется следующая необязательная автономная политика:

- **виртуальная машина настроена для несоответствующей рабочей области Log Analytics — предварительная версия**.

    Ее можно использовать для выявления виртуальных машин с настроенным [расширением виртуальной машины Log Analytics](../virtual-machines/extensions/oms-windows.md), но с другой рабочей областью (не той, которая указана в назначение политики). Здесь в качестве параметра принимается идентификатор рабочей области.

В этом первом выпуске вы можете создать назначение политики только на портале Azure. Чтобы понять, как выполняются эти шаги, изучите статью [Create a policy assignment to identify non-compliant resources in your Azure environment](../azure-policy/assign-policy-definition.md) (Создание назначения политики для выявления несоответствующих ресурсов в среде Azure).

## <a name="enable-with-powershell"></a>Включение с помощью PowerShell
Чтобы включить Azure Monitor для виртуальных машин на нескольких виртуальных машинах или в масштабируемом наборе виртуальных машин, используйте предоставленный скрипт PowerShell [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0), который создан специально для этой задачи и размещен в коллекции PowerShell Azure.  Этот сценарий поочередно перебирает все виртуальные машины и масштабируемые наборы виртуальных машин в подписке, в указанной с помощью параметра *ResourceGroup* группе ресурсов, на одной виртуальной машине или в масштабируемом наборе, для которых указано *Имя*.  Для каждой виртуальной машины или масштабируемого набора виртуальных машин скрипт проверяет, установлено ли расширение виртуальной машины, и пытается установить его там, где оно отсутствует.  Если условие выполняется, он переходит к установке расширений Log Analytics и Dependency Agent для виртуальной машины.   

Для этого скрипта требуется модуль Azure PowerShell 5.7.0 или более поздней версии. Чтобы узнать версию, выполните команду `Get-Module -ListAvailable AzureRM`. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Если модуль PowerShell запущен локально, необходимо также выполнить командлет `Connect-AzureRmAccount`, чтобы создать подключение к Azure.

Чтобы получить помощь по работе с этим скриптом, выполните `Get-Help` и изучите список аргументов с подробной информацией и примерами использования.   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

В следующем примере представлены команды PowerShell, которые позволяют включить Azure Monitor для виртуальных машин, и описан ожидаемый результат:

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>Включение в гибридной среде
В этом разделе объясняется, как настроить для виртуальных машин или физических компьютеров, размещенных в вашем центре обработки данных или другой облачной среде, мониторинг средствами Azure Monitor для виртуальных машин.  

Dependency Agent в Azure Monitor для виртуальных машин не передает данные и не требует изменений в настройках брандмауэра или портов. Представленные на схеме данные всегда передаются агентом Log Analytics в службу Azure Monitor напрямую или через [шлюз OMS](../log-analytics/log-analytics-oms-gateway.md), если политики ИТ-безопасности запрещают подключение компьютеров сети к Интернету.

Ознакомьтесь с требованиями и методами развертывания для [агента Log Analytics в Linux и Windows](../log-analytics/log-analytics-concept-hybrid.md).

Список основных шагов:

1. Установка агента Log Analytics для Windows или Linux.
2. Установка в Dependency Agent схемы Azure Monitor для виртуальных машин.
3. Включение сбора данных от счетчиков производительности.
4. Включение Azure Monitor для виртуальных машин.

### <a name="install-the-dependency-agent-on-windows"></a>Установка Dependency Agent на Windows 
Dependency Agent можно установить вручную на компьютерах Windows, выполнив `InstallDependencyAgent-Windows.exe`. Если запустить этот исполняемый файл без параметров, запустится мастер для установки в интерактивном режиме.  

>[!NOTE]
>Для установки или удаления агента требуются права администратора.

В следующей таблице приводится несколько параметров, поддерживаемых при установке агента из командной строки.  

| Параметр | ОПИСАНИЕ |
|:--|:--|
| /? | Возвращает список параметров командной строки. |
| /S | Выполняет автоматическую установку без взаимодействия с пользователем. |

Например, чтобы запустить программу установки с параметром `/?`, введите `InstallDependencyAgent-Windows.exe /?`.

Файлы для Dependency Agent в Windows по умолчанию устанавливаются в папку `C:\Program Files\Microsoft Dependency Agent`.  Если Dependency Agent не запускается после завершения установки, просмотрите подробные сведения об ошибке в записях журналов. Журнал размещается в каталоге `%Programfiles%\Microsoft Dependency Agent\logs`. 

### <a name="install-the-dependency-agent-on-linux"></a>Установка Dependency Agent в Linux
Dependency Agent устанавливается на серверах Linux с помощью скрипта оболочки `InstallDependencyAgent-Linux64.bin`, содержащего самоизвлекающийся двоичный файл. Вы можете запустить этот файл с помощью `sh` или добавить разрешения на выполнение в сам файл.

>[!NOTE]
> Для установки или настройки агента требуется доступ с правами привилегированного пользователя.
> 

| Параметр | ОПИСАНИЕ |
|:--|:--|
| -help | Получение списка параметров командной строки. |
| -s | Выполняет автоматическую установку без вывода сообщений для пользователя. |
| --check | Проверяет разрешения и операционную систему, но не устанавливает агент. |

Например, чтобы запустить программу установки с параметром `-help`, введите `InstallDependencyAgent-Linux64.bin -help`.

Установите Dependency Agent для Linux с правами привилегированного пользователя, выполнив следующую команду: `sh InstallDependencyAgent-Linux64.bin`
    
Если Dependency Agent не запускается, просмотрите подробные сведения об ошибке в записях журналов. В агентах Linux журналы находятся в каталоге `/var/opt/microsoft/dependency-agent/log`.

Файлы для Dependency Agent размещаются в следующих каталогах:

| Файлы | Расположение |
|:--|:--|
| Основные файлы | /opt/microsoft/dependency-agent |
| Файлы журналов | /var/opt/microsoft/dependency-agent/log |
| Файлы конфигурации | /etc/opt/microsoft/dependency-agent/config |
| Исполняемые файлы службы | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Двоичные файлы хранилища | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>Включение счетчиков производительности
Если указанная в решении рабочая область Log Analytics еще не настроена для сбора данных от необходимых решению счетчиков производительности, их необходимо включить отдельно. Это можно сделать вручную, как описано [здесь](../log-analytics/log-analytics-data-sources-performance-counters.md), или скачав и выполнив скрипт PowerShell, размещенный в [коллекции Powershell Azure](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1).
 
### <a name="onboard-azure-monitor-for-vms"></a>Включение Azure Monitor для виртуальных машин.
Этот метод использует шаблон JSON с описанием конфигурации, которая включает компоненты решения в рабочую область Log Analytics.  

Если вы не знакомы с концепцией развертывания ресурсов с помощью шаблона, ознакомьтесь со статьями:
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Если вы решили использовать Azure CLI, необходимо сначала установить интерфейс командной строки и использовать его локально. Необходимо запустить Azure CLI версии 2.0.27 или более поздней. Для определения версии выполните `az --version`. Если вам необходимо установить или обновить Azure CLI, ознакомьтесь со статьей [Установка Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Создание и выполнение шаблона

1. Скопируйте и вставьте в него следующий синтаксис JSON:

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. Сохраните этот файл в локальную папку, присвоив ему имя **installsolutionsforvminsights.json**.
3. Измените значения параметров **WorkspaceName**, **ResourceGroupName** и **WorkspaceLocation**.  Для **WorkspaceName** укажите полный идентификатор ресурса рабочей области Log Analytics, включая имя рабочей области, а для **WorkspaceLocation** — регион, в котором определена рабочая область.
4. Теперь все готово, чтобы развернуть этот шаблон с помощью следующей команды PowerShell:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Изменение конфигурации может занять несколько минут. После завершения настройки конфигурации появится сообщение, похожее на приведенное ниже, с таким результатом:

    ```powershell
    provisioningState       : Succeeded
    ```
После включения мониторинга может потребоваться около 10 минут, прежде чем вы сможете просмотреть состояние работоспособности и метрики для гибридного компьютера. 

## <a name="next-steps"></a>Дополнительная информация

Включив мониторинг для виртуальной машины, вы получите возможность анализировать эту информацию в Azure Monitor для виртуальных машин.  Чтобы узнать, как использовать функции работоспособности, см. статью [Understand the health of your Azure virtual machines with Azure Monitor for VMs](monitoring-vminsights-health.md) (Общие сведения о работоспособности виртуальных машин Azure с использованием Azure Monitor для виртуальных машин), а для просмотра обнаруженных зависимостей приложений см. статью [Using Azure Monitor for VMs Map to understand application components](monitoring-vminsights-maps.md) (Использование карты Azure Monitor для виртуальных машин для понимания компонентов приложения).  