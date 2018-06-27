---
title: Гибридные рабочие роли Runbook службы автоматизации Azure для Linux
description: В этой статье содержатся сведения об установке гибридной рабочей роли Runbook в службе автоматизации Azure, которая позволяет запускать модули runbook на компьютерах под управлением Linux в локальном центре обработки данных или облачной среде.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8507cf99ea22b24aa3026565cb7c4139e4c3742d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36267866"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Развертывание гибридной рабочей роли Runbook для Linux

Гибридную рабочую роль Runbook службы автоматизации Azure можно использовать для выполнения модулей runbook непосредственно на компьютере, размещающем роль, для работы с ресурсами в среде, что позволяет управлять этими локальными ресурсами. Гибридная рабочая роль Runbook Linux запускает модули runbook от имени особого пользователя, для которого можно повысить разрешения, чтобы выполнить команды, требующие повышения разрешений. Для хранения модулей runbook и управления ими используется служба автоматизации Azure, затем они передаются на один или несколько целевых компьютеров.

В этой статье описано, как установить гибридную рабочую роль Runbook на компьютере Linux.

## <a name="supported-linux-operating-systems"></a>Поддерживаемые операционные системы Linux

Дистрибутивы, поддерживаемые функцией гибридной рабочей роли Runbook:

* Amazon Linux 2012.09–2015.09 (x86/x64)
* CentOS Linux 5, 6 и 7 (x86/x64)
* Oracle Linux 5, 6 и 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 и 7 (x86/x64)
* Debian GNU/Linux 6, 7 и 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS и 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 и 12 (x86/x64)

## <a name="installing-a-linux-hybrid-runbook-worker"></a>Установка гибридной рабочей роли Runbook для Linux

Для установки и настройки гибридной рабочей роли Runbook на компьютере под управлением Linux используется простая процедура, позволяющая вручную установить и настроить роль. Для этого необходимо включить решение **гибридной рабочей роли службы автоматизации** в рабочей области Azure Log Analytics, а затем выполнить несколько команд для регистрации компьютера в качестве рабочей роли и его добавления в имеющуюся группу.

Минимальные требования для гибридной рабочей роли Runbook Linux:

* два ядра;
* 4 ГБ ОЗУ;
* Порт 443 (исходящий).

### <a name="package-requirements"></a>Требования к пакетам

| **Требуемый пакет** | **Описание** | **Минимальная версия**|
|--------------------- | --------------------- | -------------------|
|Glibc |Библиотека C GNU| 2.5-12 |
|Openssl| Библиотеки OpenSSL | 0.9.8e или 1.0|
|Curl | Веб-клиент cURL | 7.15.5|
|Python-ctypes | |
|PAM | Подключаемые модули аутентификации|
| **Дополнительный пакет** | **Описание** | **Минимальная версия**|
| PowerShell Core | Для запуска модулей runbook необходимо установить PowerShell (дополнительные сведения об установке см. в статье [Установка PowerShell Core в Linux](/powershell/scripting/setup/installing-powershell-core-on-linux)).  | 6.0.0 |

### <a name="installation"></a>Установка

Прежде чем продолжить, запишите рабочую область Log Analytics, связанную с вашей учетной записью службы автоматизации. Запишите также первичный ключ учетной записи службы автоматизации. Эти данные можно найти на портале Azure, выбрав учетную запись службы автоматизации, а затем указав **Workspace** в качестве идентификатора рабочей области и **Keys** в качестве первичного ключа. Сведения о портах и адресах, которые требуются для гибридной рабочей роли Runbook, см. в разделе [Настройка сети](automation-hybrid-runbook-worker.md#network-planning).

1. Включите решение **Гибридная рабочая роль службы автоматизации** в Azure одним из следующих методов:

   * Добавьте решение **Гибридная рабочая роль службы автоматизации** в свою подписку, используя процедуру, описанную в статье [Добавление решений для управления Azure Log Analytics в рабочую область](../log-analytics/log-analytics-add-solutions.md).
   * Выполните следующий командлет:

        ```azurepowershell-interactive
         Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

1. Установите агент OMS для Linux, выполнив следующую команду. Замените \<WorkspaceID\> и \<WorkspaceKey\> соответствующими значениями из рабочей области.

   ```bash
   wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
   ```

1. Выполните следующую команду, изменив значения параметров *-w*, *-k*, *-g* и *-e*. Замените значение параметра *-g* именем группы гибридной рабочей роли Runbook, к которой требуется присоединить новую гибридную рабочую роль Runbook Linux. Если имя не существует в учетной записи службы автоматизации, группа гибридной рабочей роли Runbook создается с этим именем.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
   ```

1. После завершения выполнения команды на странице **Группы гибридных рабочих ролей** на портале Azure отображается новая группа и количество элементов. Если указать имеющуюся группу, количество элементов в ней увеличивается. Вы можете выбрать группу из списка на странице **Группы гибридных рабочих ролей** и щелкнуть плитку **Гибридные рабочие роли**. На странице **Гибридные рабочие роли** отображается список элементов группы.

## <a name="turning-off-signature-validation"></a>Отключение проверки подписи

По умолчанию для гибридных рабочих ролей Runbook Linux требуется проверка подписи. При выполнении модуля runbook для рабочей роли без подписи вы увидите сообщение об ошибке проверки подписи. Чтобы отключить проверку подписи, выполните следующую команду. Замените второй параметр идентификатором вашей рабочей области Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>Поддерживаемые типы runbook

Гибридные рабочие роли Runbook на Linux не поддерживают полный набор типов Runbook, доступных в службе автоматизации Azure.

В гибридной рабочей роли Linux поддерживаются следующие типы runbook:

* Python 2;
* PowerShell

  > [!NOTE]
  > Модули runbook PowerShell требуют установки PowerShell Core на компьютере Linux. Подробные сведения об установке см. в статье [Установка PowerShell Core в Linux](/powershell/scripting/setup/installing-powershell-core-on-linux).

В гибридной рабочей роли Linux не поддерживаются следующие типы Runbook:

* Рабочий процесс PowerShell
* Графический
* графический модуль рабочего процесса PowerShell.

## <a name="troubleshooting"></a>Устранение неполадок

Гибридная рабочая роль Runbook Linux зависит от агента OMS для Linux, который используется для взаимодействия с учетной записью службы автоматизации с целью регистрации рабочей роли, получения заданий Runbook и передачи сведений о состоянии. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

### <a name="the-oms-agent-for-linux-isnt-running"></a>Агент OMS для Linux не запущен.

Если агент OMS для Linux не запущен, гибридная рабочая роль Runbook Linux не сможет взаимодействовать со службой автоматизации Azure. Проверьте, запущен ли агент, выполнив команду `ps -ef | grep python`. 

Вы увидите выходные данные, похожие на следующие (это процессы Python, запущенные от имени учетной записи **nxautomation**). Если решения "Управление обновлениями" и служба автоматизации Azure не включены, ни один из следующих процессов не будет запущен.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Ниже перечислены процессы, запущенные для гибридной рабочей роли Runbook в Linux. Все они находятся в каталоге `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf** — это процесс диспетчера рабочих ролей. Он запускается непосредственно на платформе Desired State Configuration (DSC).

* **worker.conf** — это процесс автоматически зарегистрированной рабочей роли. Он запускается диспетчером рабочих ролей. Этот процесс используется в решении "Управление обновлениями" и незаметен для пользователя. Этот процесс будет доступен, только если включить на компьютере решение "Управление обновлениями".

* **diy/worker.conf** — это процесс гибридной рабочей роли DIY. Процесс гибридной рабочей роли DIY используется для выполнения модулей Runbook пользователя в гибридной рабочей роли Runbook. Он отличается от процесса автоматически зарегистрированной рабочей роли только тем, что использует другую конфигурацию. Этот процесс доступен, только если включено решение службы автоматизации Azure и зарегистрирована гибридная рабочая роль DIY.

Если агент OMS для Linux не запущен, выполните следующую команду, чтобы запустить службу: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

### <a name="the-specified-class-doesnt-exist"></a>Указанный класс не существует.

При возникновении в `/var/opt/microsoft/omsconfig/omsconfig.log` ошибки о том, что указанный класс не существует, необходимо обновить агент OMS для Linux. Выполните следующую команду, чтобы повторно установить агент OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

Дополнительные действия по устранению проблем с решением "Управление обновлениями" см. в разделе [Устранение неполадок](automation-update-management.md#troubleshooting) статьи "Решение для управления обновлениями в Azure".

## <a name="next-steps"></a>Дополнительная информация

* Чтобы узнать, как настроить модули runbook для автоматизации процессов в локальном центре обработки данных или другой облачной среде, см. статью [Запуск модулей runbook в гибридной рабочей роли Runbook](automation-hrw-run-runbooks.md).
* Инструкции по удалению гибридных рабочих ролей Runbook см. в разделе [Удаление гибридных рабочих ролей Runbook в службе автоматизации Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker) статьи "Автоматизация ресурсов в центре обработки данных или облаке с помощью гибридной рабочей роли Runbook".
