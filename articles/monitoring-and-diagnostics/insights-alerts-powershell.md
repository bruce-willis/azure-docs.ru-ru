---
title: Создание классических оповещений для служб Azure с помощью PowerShell | Документация Майкрософт
description: Узнайте, как активировать сообщения электронной почты, уведомления, вызовы URL-адресов веб-сайтов (веб-перехватчики) или автоматизированные операции при выполнении заданных условий.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286205"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Создание оповещений для служб Azure с помощью PowerShell
> [!div class="op_single_selector"]
> * [Портал](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [ИНТЕРФЕЙС КОМАНДНОЙ СТРОКИ](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> В этой статье объясняется, как создавать классические оповещения метрик прежней версии. Azure Monitor теперь поддерживает [новые улучшенные оповещения на основе метрик](monitoring-near-real-time-metric-alerts.md). Эти оповещения поддерживают мониторинг нескольких метрик и охватывают метрики размерностей. В PowerShell поддержка новых оповещений на основе метрик ожидается в ближайшее время.
>
>

В этой статье показано, как настроить классические оповещения метрик Azure с помощью PowerShell.  

Вы можете получать оповещения на основе метрик для служб Azure или на основе событий, которые происходят в Azure.

* **Значения метрик**. Оповещение активируется, когда значение указанной метрики выходит за рамки заданного порогового значения. То есть сначала оно активируется, когда условие выполняется, а затем — когда условие перестает выполняться.    
* **События журнала действий**. Оповещение может активироваться при *каждом* событии или при определенном событии. Дополнительные сведения о журналах действий см. в статье [Создание оповещений журнала действий (классических)](monitoring-activity-log-alerts.md).

Для классического оповещения метрики можно настроить задачу, выполняемую при его активации:

* отправка уведомлений по электронной почте администратору службы и соадминистраторам;
* отправка сообщений электронной почты на указанные дополнительные адреса;
* вызов веб-перехватчика.
* запуск выполнения Runbook Azure (только на портале Azure).

Настроить правила генерации оповещений и получать сведения об этих правилах можно в следующих расположениях: 

* [портал Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [Azure CLI 2.0](insights-alerts-command-line-interface.md)
* [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Чтобы получить дополнительную информацию, всегда можно ввести ```Get-Help``` и команду PowerShell, по которой требуется справка.

## <a name="create-alert-rules-in-powershell"></a>Создание правил генерации оповещений в PowerShell
1. Войдите в Azure.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. Получите список доступных вам подписок. Убедитесь, что вы работаете с нужной подпиской. В противном случае вы можете получить данные о нужной подписке в выходных данных команды `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Получите список существующих правил для группы ресурсов, используя следующую команду:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Чтобы создать правило, необходимо сначала получить определенные важные сведения.

    - **Идентификатор ресурса**, для которого необходимо установить оповещение.
    - Доступные **определения метрик** для этого ресурса.

     Получить идентификатор ресурса можно на портале Azure. Если ресурс уже создан, выберите его на портале Azure. В следующей колонке в разделе **Параметры** выберите **Свойства**. В следующей колонке отображается поле **Идентификатор ресурса**. 
     
     Идентификатор ресурса можно также получить с помощью [обозревателя ресурсов Azure](https://resources.azure.com/).

     Ниже приведен пример идентификатора ресурса для веб-приложения:

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     С помощью `Get-AzureRmMetricDefinition` можно просмотреть список всех определений метрик для конкретного ресурса:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     В следующем примере создается таблица, содержащая имя метрики и единицу измерения для этой метрики.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Чтобы получить полный список доступных параметров для Get-AzureRmMetricDefinition, выполните команду `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. В следующем примере настраивается оповещение о ресурсе веб-сайта. Оповещение активируется, когда любой трафик непрерывно поступает в течении 5 минут, и затем снова активируется, если в течение 5 минут трафик не поступает.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Чтобы при активации оповещения создавался веб-перехватчик или отправлялось электронное сообщение, необходимо сначала создать это сообщение или веб-перехватчик. Сразу после этого создайте правило с тегом -Actions, как показано в следующем примере. Веб-перехватчики или сообщения электронной почты нельзя связывать с уже созданными правилами.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Просмотрите отдельные правила, чтобы проверить, правильно ли созданы оповещения.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Удалите свои оповещения. Эти команды удаляют правила, созданные ранее при работе с этой статьей.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Дополнительная информация
* [Ознакомьтесь с общими сведениями о мониторинге Azure](monitoring-overview.md), включая типы информации, которую можно собирать и отслеживать.
* Узнайте, как [настроить веб-перехватчики в оповещениях](insights-webhooks-alerts.md).
* Узнайте, как [настроить оповещения о событиях журнала действий](monitoring-activity-log-alerts.md).
* Узнайте больше о [модулях Runbook службы автоматизации Azure](../automation/automation-starting-a-runbook.md).
* Ознакомьтесь с [обзором сбора журналов диагностики](monitoring-overview-of-diagnostic-logs.md) , чтобы собирать подробные метрики о службе с высокой частотой.
* Прочитайте [обзор сбора метрики](insights-how-to-customize-monitoring.md) и узнайте, как можно обеспечить, чтобы служба была доступна и отвечала на запросы.
