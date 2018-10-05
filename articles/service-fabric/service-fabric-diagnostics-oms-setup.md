---
title: Настройка мониторинга с помощью Log Analytics в Azure Service Fabric | Документация Майкрософт
description: Узнайте, как настроить визуализацию и анализ событий в Log Analytics для мониторинга кластеров Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: srrengar
ms.openlocfilehash: a73a288852eea713623b65324853761e10fad282
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220472"
---
# <a name="set-up-log-analytics-for-a-cluster"></a>Настройка Log Analytics для кластера

Для мониторинга событий на уровне кластера рекомендуется использовать Log Analytics. Рабочую область Log Analytics можно настроить в Azure Resource Manager, PowerShell или с помощью Azure Marketplace. Если необходимо сохранить обновленный шаблон развертывания Resource Manager для будущего использования, примените тот же шаблон для настройки среды Log Analytics. Развертывание через Marketplace проще, если вы уже развернули кластер с включенной диагностикой. Если у вас нет доступа на уровне подписки в учетной записи, в которую производится развертывание, используйте PowerShell или выполните развертывание с помощью шаблона Resource Manager.

> [!NOTE]
> Чтобы настроить Log Analytics для мониторинга кластера, необходимо включить систему диагностики для просмотра событий уровня кластера или платформы. Дополнительные сведения можно получить из статей [Агрегирование и сбор событий с помощью системы диагностики Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) и [Агрегирование и сбор событий с помощью системы диагностики Azure для Linux](service-fabric-diagnostics-event-aggregation-lad.md)

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Развертывание рабочей области Log Analytics с помощью Azure Marketplace

Если нужно добавить рабочую область Log Analytics после развертывания кластера, перейдите в Azure Marketplace на портале и найдите решение **аналитики Service Fabric**. Это пользовательское решение для развертывания Service Fabric, содержащее данные, относящиеся к Service Fabric. При этом будет создано и решение (панель мониторинга для просмотра аналитики), и рабочая область (агрегирование базовых данных кластера).

1. В области навигации слева щелкните **Создать**. 

2. Найдите **Аналитика Service Fabric**. Выберите появившийся ресурс.

3. Нажмите кнопку **Создать**.

    ![Аналитика Service Fabric в Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. В окне создания аналитики Service Fabric щелкните **Выберите рабочую область** для поля **Рабочая область OMS**, а затем выберите **Создание рабочей области**. Укажите необходимые записи. Единственным требованием является совпадение подписки для кластера Service Fabric и рабочей области. Когда завершится проверка записей, начнется развертывание рабочей области. Это займет всего несколько минут.

5. Когда процесс завершится, снова выберите **Создать** в нижней части окна создания аналитики Service Fabric. Убедитесь, что новая рабочая область отображается в разделе **Рабочая область OMS**. В таком случае решение добавится в созданную рабочую область.

При использовании Windows выполните указанные ниже шаги, чтобы подключить Log Analytics к учетной записи хранения, в которой хранятся события кластера. 

>[!NOTE]
>Для кластеров Linux эта функция подключения еще недоступна. 

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Подключение рабочей области Log Analytics к кластеру 

1. Рабочая область должна быть подключена к данным диагностики, поступающим из кластера. Перейдите в группу ресурсов, в которой вы создали решение "Аналитика Service Fabric". Выберите **ServiceFabric\<\>** и перейдите на страницу обзора. На этой странице можно изменить параметры решения, рабочей области и получить доступ к рабочей области Log Analytics.

2. В меню навигации слева в разделе **Источники данных рабочей области** выберите **Журналы учетных записей хранения**.

3. На странице **Журналы учетной записи хранения** выберите **Добавить** в верхней части, чтобы добавить в рабочую область журналы кластера.

4. Выберите **Учетная запись хранения**, чтобы добавить учетную запись хранения, созданную в кластере. Если вы использовали имя по умолчанию, она называется **sfdg\<имя_группы_ресурсов\>**. Вы можете проверить, какое значение указано для параметра **applicationDiagnosticsStorageAccountName** в шаблоне Azure Resource Manager, на основе которого вы развертывали кластер. Если имя не отображается, прокрутите список вниз и выберите **Загрузить еще**. Выберите имя учетной записи хранения.

5. Укажите тип данных. Задайте для параметра типа данных значение **События Service Fabric**.

6. При этом для параметра "Источник" будет автоматически установлено значение **WADServiceFabric\*EventTable**.

7. Нажмите кнопку **ОК**, чтобы подключить рабочую область к журналам кластера.

    ![Добавление журналов учетной записи хранения в Log Analytics](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Теперь учетная запись будет отображаться в списке журналов учетной записи хранения в разделе источников данных для рабочей области.

Вы успешно добавили решение "Аналитика Service Fabric" в рабочую область Log Analytics, подключенную должным образом к платформе кластера и таблице журналов приложения. Аналогичным образом вы можете добавить в рабочую область дополнительные источники.


## <a name="deploy-log-analytics-by-using-a-resource-manager-template"></a>Развертывание Log Analytics с помощью шаблона Resource Manager

При развертывании кластера с помощью шаблона Resource Manager этот шаблон создает рабочую область Log Analytics, добавляет в нее решение Service Fabric и настраивает в ней чтение данных из соответствующих таблиц службы хранилища.

Вы можете использовать [этот пример шаблона](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure), а также изменить его в соответствии со своими потребностями. Этот шаблон делает следующее:

* создает кластер Service Fabric с 5 узлами;
* создает рабочую область Log Analytics и решение Service Fabric;
* настраивает агент OMS для сбора и отправки данных с 2 счетчиков производительности в рабочую область;
* настраивает WAD для сбора данных Service Fabric и отправляет их в таблицы хранилища Azure (WADServiceFabric*EventTable);
* настраивает рабочую область Log Analytics для считывания событий из этих таблиц.


Шаблон можно развернуть как обновление Resource Manager для кластера с помощью API `New-AzureRmResourceGroupDeployment` в модуле AzureRM PowerShell. Пример команды будет выглядеть так:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager определит, что эта команда — это обновление имеющегося ресурса. Он обработает только изменения между шаблоном, управляющим имеющимся развертыванием, и новым предоставленным шаблоном.

## <a name="deploy-log-analytics-by-using-azure-powershell"></a>Развертывание Log Analytics с помощью Azure PowerShell

Ресурс Log Analytics можно также развернуть через PowerShell с помощью команды `New-AzureRmOperationalInsightsWorkspace`. Чтобы использовать этот метод, обязательно установите [Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1). Чтобы создать рабочую область Log Analytics и добавить в нее решение Service Fabric, используйте следующий сценарий: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

После этого выполните шаги, описанные в разделе выше, чтобы подключить Log Analytics к соответствующей учетной записи хранения.

Вы также можете добавить другие решения или внести другие изменения в рабочую область Log Analytics с помощью PowerShell. Дополнительные сведения см. в статье [Управление Log Analytics с помощью PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Дополнительная информация
* [Разверните агент Log Analytics](service-fabric-diagnostics-oms-agent.md) на узлах для сбора данных счетчиков производительности и статистики Docker, а также журналов для контейнеров.
* Ознакомьтесь с функциями [поиска по журналам и запросов к журналам](../log-analytics/log-analytics-log-searches.md), которые являются частью решения Log Analytics.
* [Использование конструктора представлений для создания пользовательских представлений Log Analytics](../log-analytics/log-analytics-view-designer.md)
