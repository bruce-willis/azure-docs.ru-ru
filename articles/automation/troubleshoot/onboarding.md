---
title: Устранение ошибок при подключении управления обновлениями, отслеживания изменений и инвентаризации
description: Сведения о том, как устранить ошибки подключения с помощью решений управления обновлениями, отслеживания изменений и инвентаризации
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 044cb56b8991a1eb2dd6a1d35be621f2ffab3250
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064460"
---
# <a name="troubleshoot-errors-when-onboarding-solutions"></a>Устранение неполадок при подключении решения

Ошибки могут возникнуть при подключении таких решений, как отслеживание изменений или управление обновлениями и инвентаризация. В этой статье описываются различные ошибки, которые могут возникнуть, и пути их решения.

## <a name="general-errors"></a>Общие ошибки

### <a name="computer-grou-query-format-error"></a>Сценарий: ComputerGroupQueryFormatError

#### <a name="issue"></a>Проблема

Этот код ошибки означает, что сохраненный запрос на поиск группы компьютеров, который используется в этом решении, имеет неправильный формат. 

#### <a name="cause"></a>Причина:

Возможно, вы неудачно изменили запрос или автоматическое изменение внесено системой.

#### <a name="resolution"></a>Способы устранения:

Вы можете удалить запрос для этого решения и заново подключить решение. Запрос также будет создан заново. Текущий запрос можно найти в разделе **Сохраненные поисковые запросы** рабочей области. Этот запрос имеет имя **MicrosoftDefaultComputerGroup** и категорию, имя которой совпадает с именем связанного решения. Если включены сразу несколько решений, группа **MicrosoftDefaultComputerGroup** отображается в области **Сохраненные условия поиска** несколько раз.

### <a name="policy-violation"></a>Сценарий: PolicyViolation

#### <a name="issue"></a>Проблема

Этот код ошибки означает, что развертывание завершилось неудачей из-за нарушения одной или нескольких политик.

#### <a name="cause"></a>Причина: 

Политика находится в таком месте, из которого выполнение операции невозможно.

#### <a name="resolution"></a>Способы устранения:

Чтобы успешно развернуть решение, необходимо учитывать изменение указанной политики. Поскольку можно определить много разных политик, необходимые изменения зависят от нарушенной политики. Например, если политика определена в группе ресурсов, которая отказала в разрешении на изменение содержимого определенных типов ресурсов, можно выполнить одно из следующих действий.

* Полностью удалите политику.
* Попробуйте подключиться к другой группе ресурсов.
* Пересмотрите политику:
  * повторно примените политику к конкретному ресурсу (например, к конкретной учетной записи службы автоматизации);
  * пересмотрите набор ресурсов, к которым политика запрещает доступ.

Проверьте уведомления в правом верхнем углу портала Azure или перейдите к группе ресурсов, которая содержит выбранную учетную запись автоматизации, и нажмите **Развертывания** в разделе **Параметры**, чтобы изучить проблемное развертывание. Дополнительные сведения о политике Azure см. в [обзоре политики Azure](../../azure-policy/azure-policy-introduction.md?toc=%2fazure%2fautomation%2ftoc.json).

## <a name="mma-extension-failures"></a>Устранение неполадок расширения MMA

При развертывании решения, также развертывается множество связанных ресурсов. Одним из этих ресурсов является расширение Microsoft Monitoring Agent или агент OMS для Linux. К ним также можно отнести расширения виртуальных машин, установленные гостевым агентом виртуальной машины, который отвечает за связь с настроенной рабочей областью Operations Management Suite (OMS), и целью которого является последующая координация загрузки двоичных и прочих файлов, от которых зависит запуск решения, к которому выполняется подключение.
Обычно из уведомления, которое появляется в центре уведомлений, вы узнаете об ошибках установки MMA или агента OMS для Linux. Щелкнув уведомление, можно получить дополнительные сведения о конкретной ошибке. Дополнительные сведения о произошедших сбоях развертывания можно получить, перейдя к элементу "Развертывания" в разделе "Группы ресурсов".
К неудачной установке MMA или агента OMS для Linux могут привести множество причин, а шаги, которые необходимо предпринять для их устранения, в каждом случае отличаются. Действия по устранению неполадок подробнее описываются далее.

В следующем разделе описаны различные проблемы, возникающие при подключении, которые могут привести к ошибкам при развертывании расширения MMA.

### <a name="webclient-exception"></a>Сценарий: Исключение во время запроса WebClient

Расширению MMA виртуальной машины не удается установить связь с внешними ресурсами, что приводит к ошибке развертывания.

#### <a name="issue"></a>Проблема

Ниже приведены примеры возвращаемых сообщений об ошибках.

```
Please verify the VM has a running VM agent, and can establish outbound connections to Azure storage.
```

```
'Manifest download error from https://<endpoint>/<endpointId>/Microsoft.EnterpriseCloud.Monitoring_MicrosoftMonitoringAgent_australiaeast_manifest.xml. Error: UnknownError. An exception occurred during a WebClient request.
```

#### <a name="cause"></a>Причина:

Возможные причины возникновения этой ошибки перечислены ниже.

* Настроенный прокси-сервер виртуальной машины, у которого открыты только определенные порты.

* Настройки брандмауэра, которые блокируют доступ к запрашиваемым адресам и портам.

#### <a name="resolution"></a>Способы устранения:

Убедитесь в том, что у вас открыты порты и адреса, которые необходимы для сеанса связи. Список портов и адресов можно узнать в разделе [Настройка сети](../automation-hybrid-runbook-worker.md#network-planning).

### <a name="transient-environment-issue"></a>Сценарий: ошибка установки из-за проблем с окружающей средой

Во время развертывания установить расширение Microsoft Monitoring Agent не удалось, так как процесс установки был заблокирован другой установкой или действием

#### <a name="issue"></a>Проблема

Ниже приведены примеры сообщений об ошибках, которые могут отображаться на экране.

```
The Microsoft Monitoring Agent failed to install on this machine. Please try to uninstall and reinstall the extension. If the issue persists, please contact support.
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1618'
```

```
'Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.2) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.2\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 1601'
```

#### <a name="cause"></a>Причина:

Возможные причины возникновения этой ошибки перечислены ниже.

* Запущен другой процесс установки
* Во время развертывания шаблона была инициирована перезагрузка системы

#### <a name="resolution"></a>Способы устранения:

По своей природе эта ошибка является временной. Чтобы установить расширение, необходимо повторить развертывание.

### <a name="installation-timeout"></a>Сценарий: время ожидания установки истекло

Установить расширение MMA не удалось из-за превышения времени ожидания.

#### <a name="issue"></a>Проблема

Ниже приведен пример сообщения об ошибке, которое может отображаться на экране.

```
Install failed for plugin (name: Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent, version 1.0.11081.4) with exception Command C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\1.0.11081.4\MMAExtensionInstall.exe of Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent has exited with Exit code: 15614
```

#### <a name="cause"></a>Причина:

Эта ошибка возникает из-за того, что во время установки виртуальная машина находится под интенсивной нагрузкой.

### <a name="resolution"></a>Способы устранения:

Попытайтесь установить расширение MMA при низкой загруженности виртуальной машины.

## <a name="next-steps"></a>Дополнительная информация

Если вы не видите своего варианта проблемы или вам не удается ее решить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.
