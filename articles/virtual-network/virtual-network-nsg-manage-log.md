---
title: Журналы диагностики событий группы безопасности сети и счетчика правил в Azure | Документация Майкрософт
description: Узнайте, как включить журналы диагностики событий и счетчика правил для группы безопасности сети Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: c3f4a64c9e11d17899987bbe818506f61c415e3f
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757065"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Журнал ведения диагностики для группы безопасности сети

Группа безопасности сети (NSG) содержит правила, которые разрешают или запрещают трафик для подсети виртуальной сети, сетевого интерфейса или и того, и другого. При включении журнала ведения диагностики для NSG можно записывать в журнал сведения следующих категорий.

* **Журналы событий**. Содержат записи, по которым правила NSG на основе MAC-адреса применяются к виртуальным машинам. Состояние этих правил регистрируется каждые 60 секунд.
* **Журналы счетчиков**. Содержат записи с информацией о том, сколько раз каждое правило NSG было применено для запрета или разрешения трафика.

Журналы диагностики доступны только для NSG, для которых применена модель развертывания с помощью Azure Resource Manager. Вы не сможете включить ведение журналов диагностики для NSG, развернутых с помощью классической модели развертывания. Чтобы получить более полное представление об этих двух моделях, прочитайте статью [Развертывание с помощью Azure Resource Manager и классическое развертывание: сведения о моделях развертывания и состоянии ресурсов](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Журнал ведения диагностики следует включить отдельно для *каждой* NSG, для которой вы намерены собирать данные. Если вместо этого вам нужны журналы операций (действий), ознакомьтесь с [ведением журнала действий](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.

## <a name="enable-logging"></a>Включение ведения журналов

Чтобы журнал ведения диагностики, можно использовать [портал Azure](#azure-portal) или [PowerShell](#powershell).

### <a name="azure-portal"></a>Портал Azure

1. Войдите на [портал](https://portal.azure.com).
2. Щелкните **Все службы**, затем введите *группы безопасности сети*. Когда элемент **Группы безопасности сети** появится в результатах поиска, выберите его.
3. Выберите NSG, для которой хотите включить ведение журнала.
4. В разделе **Мониторинг** выберите **Журналы диагностики** и выберите **Включить диагностику**, как показано на следующем рисунке.
 
    ![Включение диагностики](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. В разделе **Параметры диагностики** выберите или введите приведенные ниже сведения, а затем нажмите кнопку **Сохранить**.

    | Параметр                                                                                     | Значение                                                          |
    | ---------                                                                                   |---------                                                       |
    | ИМЯ                                                                                        | Имя на ваш выбор.  Например: *myNsgDiagnostics*.      |
    | **Архивировать в учетной записи хранения**, **Передать в концентратор событий** и **Отправить в Log Analytics** | Можно выбрать любые назначения. Узнайте больше в разделе [Целевое расположение для журналов](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Выберите одну или обе категории журналов. Чтобы узнать больше о данных, записываемых в каждую категорию, ознакомьтесь с разделом [Категории журналов](#log-categories).                                                                                                                                             |
6. Просмотрите и проанализируйте журналы. Дополнительные сведения см. в разделе [Просмотр и анализ журналов](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

Вы можете выполнить приведенные ниже команды в [Azure Cloud Shell](https://shell.azure.com/powershell) или с помощью PowerShell на своем компьютере. Azure Cloud Shell — это бесплатная интерактивная оболочка. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью. Если вы решили запустить PowerShell на своем компьютере, вам потребуется модуль PowerShell *AzureRM* 6.1.1 или более поздней версии. Выполните `Get-Module -ListAvailable AzureRM` на компьютере, чтобы получить сведения об установленной версии. Если вам необходимо выполнить обновление, ознакомьтесь со статьей, посвященной [установке модуля Azure PowerShell](/powershell/azure/install-azurerm-ps). Если PowerShell работает локально, необходимо также выполнить `Login-AzureRmAccount`, чтобы войти в Azure с учетной записью, предоставляющей [необходимые разрешения](virtual-network-network-interface.md#permissions).

Чтобы включить журнал ведения диагностики, требуется идентификатор существующей NSG. Если у вас нет NSG, ее можно создать, выполнив командлет [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup).

Получите группу безопасности сети, для которой вы хотите включить журнал ведения диагностики, выполнив командлет [Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup). Например, чтобы получить NSG *myNsg* в группе ресурсов *myResourceGroup*, введите следующую команду.

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Журналы диагностики можно записывать в назначения трех типов. Дополнительные сведения см. в разделе [Целевое расположение для журналов](#log-destinations). В этой статье журналы отправляются в *Log Analytics* в качестве примера. Получите существующую рабочую область Log Analytics, выполнив командлет [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace). Например, чтобы получить рабочую область *myLaWorkspace* в группе ресурсов *LaWorkspaces*, введите следующую команду.

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName LaWorkspaces `
  -Name myLaWorkspace
```

Если у вас нет рабочей области, ее можно создать с помощью командлета [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace).

Существуют две категории, для которых можно включить ведение журнала. Дополнительные сведения см. в разделе [Категории журналов](#log-categories). Включите журнал ведения диагностики для NSG с помощью командлета [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting). Следующий пример записывает в журнал данные о категориях событий и счетчиков и сохраняет его в рабочую область для NSG, используя идентификаторы NSG и рабочей области, полученные ранее.

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Если требуется записывать данные только одной из категорий, добавьте в предыдущую команду параметр `-Categories` и *NetworkSecurityGroupEvent* или *NetworkSecurityGroupRuleCounter*. Если вы хотите записывать журналы не в рабочую область Log Analytics, а в другое [назначение](#log-destinations), используйте соответствующие параметры [учетной записи хранения](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) или [концентратора событий](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.

Просмотрите и проанализируйте журналы. Дополнительные сведения см. в разделе [Просмотр и анализ журналов](#view-and-analyze-logs).

## <a name="log-destinations"></a>Целевое расположение для журналов

Назначения данных диагностики:
- [Запись в учетную запись хранения](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) для аудита или проверки вручную. В параметрах диагностики ресурсов можно задать время хранения (в днях).
- [Потоковая передача журнала в концентратор событий](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) для приема сторонней службой или пользовательским аналитическим решением, например PowerBI.
- [Запись в Azure Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics).

## <a name="log-categories"></a>Категории журналов

Данные в формате JSON записываются в журналы следующих категорий.

### <a name="event"></a>Событие

Журнал событий содержит сведения о том, какие правила NSG на основе MAC-адреса применяются к виртуальным машинам. Для каждого события регистрируются данные, представленные в этом примере:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>Счетчик правил

Журнал счетчика правил содержит сведения о всех случаях применения правил к ресурсам. Для каждого применения правила регистрируются данные, представленные в этом примере:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> Исходный IP-адрес для обмена данными записывается. Однако можно включить [ведение журнала потока NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) для NSG. В этом случае в журнал будут записываться все сведения о счетчике правил, а также исходный IP-адрес, с которого был инициирован обмен данными. Данные журнала потоков NSG записываются в учетную запись хранения Azure. Данные можно анализировать с помощью функции [анализа трафика](../network-watcher/traffic-analytics.md) службы "Наблюдатель за сетями".

## <a name="view-and-analyze-logs"></a>Просмотр и анализ журналов

Узнайте, как просмотреть данные журнала диагностики, ознакомившись с разделом [Сбор и использование данных журнала из ресурсов Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Если данные диагностики отправляются в:
- **Log Analytics**, то важные сведения можно получать с помощью решения для [анализа групп безопасности сети](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
). Это решение наглядно представляет правила NSG, которые разрешают или запрещают трафик по MAC-адресу сетевого интерфейса в виртуальной машине.
- **учетную запись хранения**, то данные записываются в файл PT1H.json. Расположение журналов:
    - журнал событий: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - журнал счетчика правил: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Дополнительная информация

- Узнайте больше о [ведении журнала действий](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ранее известном как журнал аудита или операционный журнал. Ведение журнала действий включено по умолчанию для всех создаваемых NSG, независимо от модели развертывания Azure. Чтобы определить в журнале активности, какие операции были выполнены с группами безопасности сети, найдите записи, содержащие следующие типы ресурсов:
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- Чтобы узнать, как вести журнал диагностики и записывать в него исходные IP-адреса для каждого потока, ознакомьтесь с разделом [Руководство по регистрации потока сетевого трафика на виртуальную машину и с нее с помощью портала Azure](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).