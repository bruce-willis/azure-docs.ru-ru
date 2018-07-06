---
title: Устранение неполадок c помощью управления обновлениями
description: Подробные сведения об устранении неполадок c помощью управления обновлениями
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: b77d1210ff48a4bd30834fcbad64173bf77b1290
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064599"
---
# <a name="troubleshooting-issues-with-update-management"></a>Устранение неполадок c помощью управления обновлениями

В этой статье рассматриваются решения по устранению неполадок, которые могут возникнуть при использовании управления обновлениями

## <a name="windows"></a>Windows

Если при попытке подключить решение или виртуальную машину возникли проблемы, проверьте журнал событий **Operations Manager** в разделе **Журналы приложений и служб** на локальном компьютере для событий с идентификатором **4502**, а также сообщение события, содержащее **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent**.

В разделе ниже выделены отдельные сообщения об ошибках и возможные решения этих ошибок. Для устранения других неполадок с подключением см. в разделе [Troubleshoot solution onboarding](onboarding.md) (Устранение неполадок при подключении).

### <a name="machine-already-registered"></a>Сценарий: компьютер уже зарегистрирован на другой учетной записи

#### <a name="issue"></a>Проблема

Отображается следующее сообщение об ошибке:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

#### <a name="cause"></a>Причина:

Компьютер уже подключен к другой рабочей области для управления обновлениями.

#### <a name="resolution"></a>Способы устранения:

Выполните очистку старых артефактов на компьютере путем [удаления группы гибридных модулей Runbook](../automation-hybrid-runbook-worker.md#remove-a-hybrid-worker-group) и повторите попытку.

### <a name="machine-unable-to-communicate"></a>Сценарий: компьютер недоступен для взаимодействия со службой

#### <a name="issue"></a>Проблема

Отображается одно из следующих сообщений об ошибке.

```
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

#### <a name="cause"></a>Причина:

Возможно прокси-сервер, шлюз или брандмауэр блокирует сетевую связь.

#### <a name="resolution"></a>Способы устранения:

Проверьте сеть и убедитесь, что необходимые порты и адреса разрешены. Раздел [Требования сети](../automation-hybrid-runbook-worker.md#network-planning) содержит информацию о том, как создать список портов и адресов, необходимых для управления обновлениями, и гибридные рабочие роли Runbook.

### <a name="unable-to-create-selfsigned-cert"></a>Сценарий: не удалось создать самозаверяющий сертификат

#### <a name="issue"></a>Проблема

Отображается одно из следующих сообщений об ошибке.

```
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

#### <a name="cause"></a>Причина:

Гибридной рабочей роли Runbook не удалось создать самозаверяющий сертификат

#### <a name="resolution"></a>Способы устранения:

Убедитесь, что системная учетная запись имеет доступ для чтения к папке **C:\ProgramData\Microsoft\Crypto\RSA** и повторите попытку.

## <a name="linux"></a>Linux

### <a name="scenario-update-run-fails-to-start"></a>Сценарий: сбой запуска обновления

#### <a name="issue"></a>Проблема

Сбой запуска обновления на компьютере Linux.

#### <a name="cause"></a>Причина:

Гибридная рабочая роль Linux неработоспособна.

#### <a name="resolution"></a>Способы устранения:

Создайте копию в следующем файле журнала и сохранить его для устранения неполадок.

```
/var/opt/microsoft/omsagent/run/automationworker/worker.log
```

### <a name="scenario-update-run-starts-but-encounters-errors"></a>Сценарий: запуск обновлений начинается, но возникают ошибки

#### <a name="issue"></a>Проблема

Запуск обновления начинается, но возникают ошибки во время выполнения.

#### <a name="cause"></a>Причина:

Возможные причины:

* диспетчер пакетов неработоспособен;
* определенные пакеты могут конфликтовать с установкой исправлений облака;
* другие причины.

#### <a name="resolution"></a>Способы устранения:

Если сбои происходят во время запуска обновления после того, как он успешно запущен в Linux, проверьте выполнение задания на затронутом компьютере в ходе выполнения. Просмотрите конкретные сообщения об ошибках из диспетчера пакетов вашего компьютера и примите меры. Для успешного обновления развертываний управлению обновлениями необходима работоспособность диспетчера пакетов.

В некоторых случаях пакет обновления может повлиять на предотвращение завершения развертывания обновлений в управлении обновлениями. Если это происходит, необходимо или исключить эти пакеты из запусков будущих обновлений или установить их самостоятельно вручную.

Если не удается это исправить, создайте копию в следующем файле журнала и сохраните его **перед** началом следующего развертывания обновления для устранения неполадок.

```
/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log
```

## <a name="next-steps"></a>Дополнительная информация

Если вы не видите своего варианта проблемы или вам не удается ее решить, дополнительные сведения можно получить, посетив один из следующих каналов.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.