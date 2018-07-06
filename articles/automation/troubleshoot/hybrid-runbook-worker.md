---
title: Устранение неполадок с гибридными рабочими ролями Runbook в службе автоматизации Azure
description: В этой статье приводятся сведения об устранении неполадок с гибридными рабочими ролями Runbook в службе автоматизации Azure
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1cae7253a4bfcb4f83baf003a4d9d3c367d8f014
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064477"
---
# <a name="troubleshoot-hybrid-runbook-workers"></a>Устранение неполадок с гибридными рабочими ролями Runbook

В этой статье приводятся сведения об устранении неполадок с гибридными рабочими ролями Runbook.

## <a name="general"></a>Общие сведения

Гибридная рабочая роль Runbook зависит от агента, который используется для взаимодействия с учетной записью автоматизации для регистрации рабочей роли, получения заданий runbook и сообщения о состоянии. В Windows используется агент Microsoft Monitoring Agent. В Linux — агент Operations Management Suite для Linux.

###<a name="runbook-execution-fails"></a>Сценарий: происходит сбой выполнения модуля Runbook

#### <a name="issue"></a>Проблема

Происходит сбой выполнения модуля Runbook со следующей ошибкой:

```
"The job action 'Activate' cannot be run, because the process stopped unexpectedly. The job action was attempted three times."
```

Модуль Runbook приостанавливается сразу после третьей попытки выполнения. Определенные условия могут помешать успешному выполнению модуля Runbook, а в сообщении об ошибке не содержится никаких дополнительных сведений, позволяющих определить причину.

#### <a name="cause"></a>Причина:

Вот возможные причины:

* Модули Runbook не могут выполнить аутентификацию с помощью локальных ресурсов.

* Гибридная рабочая роль находится за прокси-сервером или брандмауэром.

* Модули Runbook не могут выполнить аутентификацию с помощью локальных ресурсов.

* Компьютер, предназначенный для выполнения гибридной рабочей роли Runbook, соответствует минимальным требованиям к оборудованию.

#### <a name="resolution"></a>Способы устранения:

Проверьте, имеет ли компьютер исходящий доступ к *.azure-automation.net на порту 443.

Прежде чем назначать компьютеры, на которых будет выполняться гибридная рабочая роль Runbook, необходимо выполнить на них соответствующие минимальные требования к оборудованию. Иначе в зависимости от использования ресурсов других фоновых процессов и возникновения конфликтов, вызванных модулями runbook во время выполнения, компьютер становится чрезмерно загруженным. Из-за этого при выполнении задания runbook возникают задержки и простои.

Убедитесь, что компьютер, предназначенный для выполнения гибридной рабочей роли Runbook, соответствует минимальным требованиям к оборудованию. Если требования выполнены, отследите использование ЦП и памяти, чтобы определить корреляцию между производительностью процессов гибридной рабочей роли Runbook и Windows. Если есть нагрузка на память или ЦП, возможно, необходимо обновить или добавить дополнительные процессоры либо увеличить объем памяти, чтобы устранить ошибку и проблему нехватки ресурсов. Как вариант, выберите другой вычислительный ресурс, который соответствует минимальным требованиям и масштабируется в соответствии с требованиями рабочей нагрузки.

Проверьте журнал событий **Microsoft-SMA** на наличие соответствующего события с описанием *Процесс Win32 завершен с кодом [4294967295]*. Эта ошибка возникла из-за того, что аутентификация в модулях Runbook еще не настроена или для группы гибридных рабочих ролей указаны учетные данные запуска от имени. Просмотрите [разрешения для модулей Runbook](../automation-hrw-run-runbooks.md#runbook-permissions), чтобы убедиться, что аутентификация для этих модулей настроена правильно.

## <a name="linux"></a>Linux

Гибридная рабочая роль Runbook Linux зависит от агента OMS для Linux, который используется для взаимодействия с учетной записью службы автоматизации с целью регистрации рабочей роли, получения заданий Runbook и передачи сведений о состоянии. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

###<a name="oms-agent-not-running"></a>Сценарий: агент OMS для Linux не запущен

Если агент OMS для Linux не запущен, гибридная рабочая роль Runbook Linux не сможет взаимодействовать со службой автоматизации Azure. Проверьте, запущен ли агент, выполнив следующую команду: `ps -ef | grep python`. Вы увидите выходные данные, похожие на следующие; это процессы python, запущенные от имени учетной записи **nxautomation**. Если решения "Управление обновлениями" и "Служба автоматизации Azure" не включены, ни один из следующих процессов не будет запущен.

```bash
nxautom+   8567      1  0 14:45 ?        00:00:00 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/main.py /var/opt/microsoft/omsagent/state/automationworker/oms.conf rworkspace:<workspaceId> <Linux hybrid worker version>
nxautom+   8593      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/state/automationworker/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
nxautom+   8595      1  0 14:45 ?        00:00:02 python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/worker/hybridworker.py /var/opt/microsoft/omsagent/<workspaceId>/state/automationworker/diy/worker.conf managed rworkspace:<workspaceId> rversion:<Linux hybrid worker version>
```

Ниже перечислены процессы, запущенные для гибридной рабочей роли Runbook Linux. Все они находятся в каталоге `/var/opt/microsoft/omsagent/state/automationworker/`.

* **oms.conf** — это процесс диспетчера рабочих ролей, запускаемый напрямую из DSC.

* **worker.conf** — это процесс автоматически зарегистрированной рабочей роли. Он запускается диспетчером рабочих ролей. Этот процесс используется в решении "Управление обновлениями" и незаметен для пользователя. Если решение "Управление обновлениями" не включено на компьютере, этот процесс будет отсутствовать.

* **diy/worker.conf** — это процесс гибридной рабочей роли DIY. Процесс гибридной рабочей роли DIY используется для выполнения модулей Runbook пользователя в гибридной рабочей роли Runbook. Он отличается от процесса автоматически зарегистрированной рабочей роли только тем, что использует другую конфигурацию. Если решение "Служба автоматизации Azure" не включено и гибридная рабочая роль DIY для Linux отсутствует, этот процесс будет отсутствовать.

Если агент OMS для Linux не запущен, выполните следующую команду, чтобы запустить службу: `sudo /opt/microsoft/omsagent/bin/service_control restart`.

###<a name="class-does-not-exist"></a>Сценарий: указанный класс не существует

Если вы видите сообщение об ошибке о том, что **указанный класс не существует**, в `/var/opt/microsoft/omsconfig/omsconfig.log` необходимо обновить агент OMS для Linux. Выполните следующую команду, чтобы повторно установить агент OMS:

```bash
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <WorkspaceID> -s <WorkspaceKey>
```

## <a name="windows"></a>Windows

Гибридная рабочая роль Runbook для Windows зависит от службы Microsoft Monitoring Agent, которая используется для взаимодействия с учетной записью службы автоматизации для регистрации рабочей роли, получения заданий Runbook и сообщения о состоянии. Если при регистрации рабочей роли произошла ошибка, это могло произойти по следующим причинам:

###<a name="mma-not-running"></a>Сценарий: Microsoft Monitoring Agent не работает.

#### <a name="issue"></a>Проблема

Служба `healthservice` не работает на компьютере с гибридной рабочей ролью Runbook.

#### <a name="cause"></a>Причина:

Если не запущена служба Windows Microsoft Monitoring Agent, то гибридная рабочая роль Runbook не может взаимодействовать со службой автоматизации Azure.

#### <a name="resolution"></a>Способы устранения:

Проверьте, запущен ли агент, введя в PowerShell команду `Get-Service healthservice`. Если служба остановлена, введите в PowerShell команду `Start-Service healthservice`, чтобы запустить эту службу.

###<a name="event-4502"></a> Событие 4502 в журнале Operations Manager

#### <a name="issue"></a>Проблема

В журнале событий в папке **Application and Services Logs\Operations Manager** отображается событие 4502 и EventMessage с **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** со следующим описанием: *Сертификат, представленный службой \<wsid\>.oms.opinsights.azure.com, не был выдан центром сертификации для служб Майкрософт. Обратитесь к администратору сети, чтобы узнать, использует ли он прокси-сервер, перехватывающий связь по протоколам TLS и SSL. Дополнительные сведения об устранении неполадок, связанных с подключением, содержатся в статье базы знаний KB3126513.*

#### <a name="cause"></a>Причина:

Это может быть вызвано тем, что прокси-сервер или сетевой брандмауэр блокируют подключение к Microsoft Azure. Проверьте, имеет ли компьютер исходящий доступ к *.azure-automation.net на порту 443.

#### <a name="resolution"></a>Способы устранения:

Журналы сохраняются локально в каждом гибридном компоненте Worker по адресу C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes. Можно проверить, зарегистрированы ли в журналах событий в папках **Application and Services Logs\Microsoft-SMA\Operations** и **Application and Services Logs\Operations Manager** какие-либо предупреждения или ошибки, которые указывают на проблемы подключения или другие проблемы, связанные с адаптацией роли службы автоматизации Azure, либо на проблемы во время выполнения обычных операций.

[Выходные данные Runbook и сообщения](../automation-runbook-output-and-messages.md) отправляются в службу автоматизации Azure из гибридных рабочих ролей точно так же, как задания Runbook, которые выполняются в облаке. Потоки Verbose и Progress можно активировать точно так же, как и для других модулей Runbook.

## <a name="next-steps"></a>Дополнительная информация

Если данные о проблеме не отображаются или не удается решить проблему, воспользуйтесь одним из следующих каналов, чтобы получить дополнительную поддержку.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
