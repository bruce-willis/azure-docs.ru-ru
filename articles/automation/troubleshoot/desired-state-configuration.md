---
title: Устранение неполадок с платформой Desired State Configuration (DSC) в службе автоматизации Azure
description: В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).
services: automation
ms.service: automation
ms.component: ''
author: georgewallace
ms.author: gwallace
ms.date: 06/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5d2eae67fcff74a7016f7f6125e31a9c8c2bda97
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064631"
---
# <a name="troubleshoot-desired-state-configuration-dsc"></a>Устранение неполадок с платформой Desired State Configuration (DSC)

В этой статье приводятся сведения об устранении неполадок с платформой Desired State Configuration (DSC).

## <a name="common-errors-when-working-with-desired-state-configuration-dsc"></a>Распространенные ошибки при настройке требуемого состояния (DSC)

### <a name="failed-not-found"></a>Сценарий: узел находится в состоянии сбоя с ошибкой "Не найдено"

#### <a name="issue"></a>Проблема

В отчете об узле указано состояние **сбоя** и содержится сообщение об ошибке:

```
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

#### <a name="cause"></a>Причина:

Эта ошибка обычно возникает из-за того, что узлу назначается имя конфигурации (например, ABC) вместо имени конфигурации узла (например, ABC.WebServer).

#### <a name="resolution"></a>Способы устранения:

* Убедитесь, что вы назначаете узлу "имя конфигурации узла", а не "имя конфигурации".
* Конфигурацию узла можно назначить узлу с помощью портала Azure или с помощью командлета PowerShell.

  * Чтобы назначить узлу конфигурацию узла с помощью портала Azure, откройте страницу **Узлы DSC**, затем выберите узел и нажмите кнопку **Назначить конфигурацию узла**.  
  * Чтобы назначить узлу конфигурацию узла с помощью PowerShell, используйте командлет **Set-AzureRmAutomationDscNode** .

### <a name="no-mof-files"></a>Сценарий: при компиляции конфигурации не были созданы файлы конфигурации узла (MOF-файлы)

#### <a name="issue"></a>Проблема

Задание компиляции DSC приостановлено с ошибкой:

```
Compilation completed successfully, but no node configuration.mofs were generated.
```

#### <a name="cause"></a>Причина:

Если выражение, указанное в конфигурации DSC рядом с ключевым словом **Node**, возвращает результат `$null`, конфигурация узла не создается.

#### <a name="resolution"></a>Способы устранения:

Эту проблему можно устранить одним из следующих способов.

* Убедитесь, что выражение рядом с ключевым словом **Node** в определении конфигурации не равно $null.
* Если при компиляции конфигурации вы передаете ConfigurationData, то убедитесь, что передаются ожидаемые значения из [ConfigurationData](../automation-dsc-compile.md#configurationdata), которые необходимы конфигурации.

### <a name="dsc-in-progress"></a>Сценарий: отчет узла DSC зависает в состоянии "Выполняется"

#### <a name="issue"></a>Проблема

Агент DSC выводит такое сообщение:

```
No instance found with given property values
```

#### <a name="cause"></a>Причина:

Вы обновили версию WMF, что привело к повреждению WMI.

#### <a name="resolution"></a>Способы устранения:

Чтобы решить эту проблему, выполните инструкции из статьи [Известные проблемы и ограничения настройки требуемого состояния (DSC)](https://msdn.microsoft.com/powershell/wmf/5.0/limitation_dsc).

### <a name="issue-using-credential"></a>Сценарий: не удается использовать учетные данные в конфигурации DSC

#### <a name="issue"></a>Проблема

Задание компиляции DSC приостановлено со следующей ошибкой:

```
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

#### <a name="cause"></a>Причина:

Вы использовали учетные данные в конфигурации, но не передали соответствующие данные **ConfigurationData**, чтобы присвоить **PSDscAllowPlainTextPassword** значение true для конфигурации каждого узла.

#### <a name="resolution"></a>Способы устранения:

* Передайте правильные данные **ConfigurationData**, чтобы присвоить **PSDscAllowPlainTextPassword** значение true для конфигурации каждого узла. См. дополнительные сведения о [ресурсах в службе Automation DSC Azure](../automation-dsc-compile.md#assets).

## <a name="next-steps"></a>Дополнительная информация

Если данные о проблеме не отображаются или не удается решить проблему, воспользуйтесь одним из следующих каналов, чтобы получить дополнительную поддержку.

* Получите ответы специалистов Azure на [форумах Azure](https://azure.microsoft.com/support/forums/).
* Подключитесь к [@AzureSupport](https://twitter.com/azuresupport) — официальной учетной записи Microsoft Azure. Она помогает оптимизировать работу пользователей благодаря возможности доступа к ресурсам сообщества Azure (ответы на вопросы, поддержка и консультации специалистов).
* Если вам нужна дополнительная помощь, отправьте запрос в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Получить поддержку**.