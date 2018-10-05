---
title: Интеграция службы автоматизации Azure с системой управления версиями Azure DevOps Services
description: В этой статье описано, как настроить интеграцию учетной записи службы автоматизации Azure с системой управления версиями Azure DevOps Services.
services: automation
author: eamonoreilly
ms.author: eamono
keywords: azure powershell, Azure DevOps Services, source control, automation
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 03/19/2017
ms.openlocfilehash: ef21060e98972dd7bc561f9a4311fa0c4bdec3b4
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47227067"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Сценарий службы автоматизации Azure. Интеграция системы управления версиями службы автоматизации с Azure DevOps

В этом сценарии показан проект Azure DevOps, который используется для управления модулями Runbook службы автоматизации Azure или конфигурациями DSC в системе управления версиями.
В этой статье описано, как настроить интеграцию Azure DevOps со средой службы автоматизации Azure, чтобы при каждом возврате происходила непрерывная интеграция.

## <a name="getting-the-scenario"></a>Получение сценария

В сценарии используются два модуля Runbook PowerShell, которые можно импортировать непосредственно из [коллекции модулей Runbook](automation-runbook-gallery.md) на портале Azure или скачать из [коллекции PowerShell](https://www.powershellgallery.com).

### <a name="runbooks"></a>Модули Runbook

Модуль Runbook | ОПИСАНИЕ| 
--------|------------|
Sync-VSTS | Импорт модулей Runbook или конфигураций из системы управления версиями Azure DevOps, когда выполняется возврат. Если запустить модуль вручную, он импортирует и публикует все модули Runbook или конфигурации в учетную запись службы автоматизации.| 
Sync-VSTSGit | Импорт модулей Runbook или конфигураций из Azure DevOps в системе управления версиями Git, когда выполняется возврат. Если запустить модуль вручную, он импортирует и публикует все модули Runbook или конфигурации в учетную запись службы автоматизации.|

### <a name="variables"></a>Переменные

Переменная | ОПИСАНИЕ|
-----------|------------|
VSToken | Создаваемый вами ресурс защищенной переменной, который содержит персональный маркер доступа Azure DevOps. Сведения о создании персонального маркера доступа Azure DevOps см. в руководстве по [проверке подлинности Azure DevOps](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).
## <a name="installing-and-configuring-this-scenario"></a>Установка и настройка данного сценария

Создайте [персональный маркер доступа](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) в Azure DevOps для синхронизации модулей Runbook или конфигураций в учетной записи службы автоматизации.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Создайте [защищенную переменную](automation-variables.md) в учетной записи службы автоматизации для хранения персонального маркера доступа, чтобы модуль Runbook мог пройти проверку подлинности в Azure DevOps и синхронизировать модули Runbook или конфигурации в учетную запись службы автоматизации. Можно назвать ее VSToken.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Импортируйте модуль Runbook, который синхронизирует ваши модули Runbook или конфигурации в учетной записи службы автоматизации. Можно использовать [пример модуля Runbook Azure DevOps](https://www.powershellgallery.com/packages/Sync-VSTS) или [пример модуля Runbook Azure DevOps с Git](https://www.powershellgallery.com/packages/Sync-VSTSGit) из [коллекции PowerShell](https://www.powershellgallery.com) в зависимости от того, какая система управления версиями используется (Azure DevOps или Azure DevOps с Git), и развернуть в учетной записи службы автоматизации.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Теперь можно [опубликовать](automation-creating-importing-runbook.md#publishing-a-runbook) этот модуль Runbook, чтобы затем создать объект webhook. 
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Создайте объект [webhook](automation-webhooks.md) для этого модуля Runbook Sync-VSTS и заполните параметры, как показано ниже. Скопируйте URL-адрес веб-перехватчика, так как он требуется для перехватчика события в Azure DevOps. VSAccessTokenVariableName — это имя (VSToken) защищенной переменной, которую вы создали ранее для хранения персонального маркера доступа. 

При интеграции с помощью Azure DevOps (Sync-VSTS.ps1) используются следующие параметры:
### <a name="sync-vsts-parameters"></a>Параметры Sync-VSTS

Параметр | ОПИСАНИЕ| 
--------|------------|
WebhookData | Содержит сведения о возврате, отправленные из перехватчика события Azure DevOps. Этот параметр следует оставить пустым.| 
ResourceGroup | Имя группы ресурсов, в которой находится учетная запись службы автоматизации.|
AutomationAccountName | Имя учетной записи службы автоматизации, которая синхронизируется с Azure DevOps.|
VSFolder | Имя папки в Azure DevOps, где находятся модули Runbook и конфигурации.|
VSAccount | Название организации Azure DevOps.| 
VSAccessTokenVariableName | Имя защищенной переменной (VSToken), в который хранится персональный маркер доступа Azure DevOps.| 


![](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

При интеграции с помощью Azure DevOps с GIT (Sync-VSTSGit.ps1) используются следующие параметры.

Параметр | ОПИСАНИЕ|
--------|------------|
WebhookData | Будет содержать сведения о возврате, отправленные из перехватчика события Azure DevOps. Этот параметр следует оставить пустым.| 
ResourceGroup | Имя группы ресурсов, в которой находится учетная запись службы автоматизации.|
AutomationAccountName | Имя учетной записи службы автоматизации, которая синхронизируется с Azure DevOps.|
VSAccount | Название организации Azure DevOps.|
VSProject | Имя проекта в Azure DevOps, где находятся модули Runbook и конфигурации.|
GitRepo | Имя репозитория Git.|
GitBranch | Имя ветви в репозитории Azure DevOps Git.|
Папка | Имя папки в ветви Azure DevOps Git.|
VSAccessTokenVariableName | Имя защищенной переменной (VSToken), в который хранится персональный маркер доступа Azure DevOps.|

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Создайте перехватчик события в Azure DevOps для операций возврата в папку, который запускает этот веб-перехватчик при возврате кода. Выберите **веб-перехватчики** как службу для интеграции при создании подписки. Дополнительные сведения о перехватчиках событий см. в [документации по перехватчикам событий Azure DevOps](https://www.visualstudio.com/en-us/docs/marketplace/integrate/service-hooks/get-started).
![](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Теперь вы можете выполнять все операции возврата модулей Runbook и конфигураций в Azure DevOps, и они будут автоматически синхронизироваться в учетной записи службы автоматизации.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Если этот модуль Runbook запускается вручную, а не активируется Azure DevOps, то параметр webhookdata можно оставить пустым. Он выполняет полную синхронизацию из указанной папки Azure DevOps.

Если вы хотите удалить сценарий, то удалите перехватчик события из Azure DevOps, удалите модуль Runbook и переменную VSToken.
