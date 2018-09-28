---
title: Создание, просмотр и управление классическими оповещениями метрик с помощью Azure Monitor
description: Узнайте, как использовать портал Azure, CLI или PowerShell для создания, просмотра и управления классическими правилами генерации оповещений метрики.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: e325335d43ef31c44ac812aca66309132f5372a3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951616"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Создание и просмотр оповещений метрики, а также управление ими с помощью Azure Monitor

Классические оповещения метрик в Azure Monitor позволяют получать уведомления, когда одна из метрик достигает порогового значения. Классические оповещения метрик — это более старая функциональность, которая позволяет оповещать только метрики без измерений. Существует более новая функциональность, называемая "оповещения метрик", которая имеет улучшенные функциональные возможности по сравнению с классическими оповещениями метрики. Подробнее о новой функциональности оповещений метрик можно узнать в [обзоре оповещений метрик](alert-metric-overview.md). В этой статье мы расскажем, как создавать, просматривать классические правила генерации оповещений метрик и управлять ими через портал Azure, Azure CLI и PowerShell.

## <a name="create-a-classic-metric-alert-rule-using-azure-portal"></a>Создание классического правила генерации оповещений метрики с помощью портала Azure

1. На [портале](https://portal.azure.com/) найдите и выберите ресурс, который требуется отслеживать.

2. В разделе **Мониторинг** выберите **Оповещения (классические)**. Текст и значок для разных ресурсов могут незначительно отличаться. Если пункт **Оповещения (классические)** не отображается здесь, его можно найти в разделе **Оповещения** или **Правила генерации оповещений**

    ![Мониторинг](./media/alerts-metric-classic/AlertRulesButton.png)

3. Выберите команду **Добавить оповещение метрики (классическое)**, а затем заполните поля.

    ![Добавить оповещение](./media/alerts-metric-classic/AddAlertOnlyParamsPage.png)

4. Присвойте **имя** правилу генерации оповещений. Затем введите **описание**, которое также будет отображаться в уведомлениях по электронной почте.

5. Выберите **метрику**, которую нужно отслеживать. Затем задайте **условие** и **пороговое значение** для метрики. Кроме того, выберите **период**, в течение которого должно быть выполнено правило метрики, прежде чем будет активировано оповещение. Например, если используется период "За последние 5 минут", а оповещение определяет загрузку ЦП выше 80 %, оно активируется, если ЦП был загружен больше чем на 80 % не меньше 5 минут. После первой активации оповещение активируется повторно, если загрузка ЦП будет ниже 80 % не меньше 5 минут. Измерение метрики ЦП происходит каждую минуту.

6. Установите флажок **Владельцы, авторы и читатели электронных писем**, если администраторы и соадминистраторы должны получать уведомления по электронной почте при срабатывании оповещения.

7. Чтобы отправлять уведомления на дополнительные электронные адреса при срабатывании оповещения, добавьте их в поле **Дополнительные адреса электронной почты администратора**. Разделяйте адреса электронной почты точкой с запятой в следующем формате: *email@contoso.com; email2@contoso.com*.

8. Укажите допустимый универсальный код ресурса (URI) в поле **Веб-перехватчик**, если его необходимо вызывать при срабатывании оповещения.

9. При использовании службы автоматизации Azure можно выбрать модуль runbook, который будет выполняться при срабатывании оповещения.

10. Нажмите кнопку **ОК**, чтобы создать оповещение.

Через несколько минут оповещение включится и будет активироваться, как было описано выше.

## <a name="manage-your-classic-metric-alert-rules-using-azure-portal"></a>Управляйте классическими правилами генерации оповещений метрик с помощью портала Azure

Вы можете выбрать созданное оповещение и выполнить одну из следующих задач:

* просмотреть диаграмму, отображающую пороговые и фактические значения метрик за предыдущий день;
* изменить или удалить его;
* **отключить** или **включить** его, если нужно временно остановить или возобновить получение уведомлений для данного оповещения.

## <a name="creating-and-managing-classic-metric-alert-rule-using-azure-cli"></a>Создание классического правила генерации оповещений метрик и управлением им с помощью Azure CLI

В предыдущих разделах описаны создание, просмотр правил генерации оповещений метрики и управление ими с помощью портала Azure. В этом разделе вы узнаете, как сделать то же самое с помощью кроссплатформенного [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest). Самый быстрый способ начать использовать Azure CLI посредством [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Получение всех классических правил генерации оповещений метрики в группе ресурсов

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Просмотр сведений о конкретном классическом правиле генерации оповещения

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Создание классического правила генерации оповещений метрики

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Удаление классического правила генерации оповещений метрики

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

### <a name="create-view-and-manage-classic-metric-alerts-using-powershell"></a>Создание, просмотр и управление классическими оповещениями метрики с помощью PowerShell

В этих разделах показано, как использовать команды PowerShell для создания, просмотра и управления классическими оповещениями метрик. На примерах в этой статье проиллюстрировано, как можно использовать командлеты Azure Monitor для классических оповещений метрик.

1. Если вы этого еще не сделали, настройте PowerShell для выполнения на своем компьютере. Дополнительные сведения см. в разделе [Общие сведения об Azure PowerShell](/powershell/azure/overview). Можно также просмотреть полный список командлетов PowerShell (для мониторинга) в документации [Azure Monitor Cmdlets](https://docs.microsoft.com/powershell/module/azurerm.insights).

2. Сначала войдите в свою подписку Azure.

    ```PowerShell
    Connect-AzureRmAccount
    ```

3. Вы увидите экран входа. После выполнения входа вы увидите свою учетную запись, идентификатор клиента и идентификатор подписки по умолчанию. Все командлеты Azure будут работать в контексте подписки по умолчанию. Чтобы просмотреть список доступных вам подписок, используйте следующую команду.

    ```PowerShell
    Get-AzureRmSubscription
    ```

4. Чтобы сменить рабочий контекст на другую подписку, используйте следующую команду.

    ```PowerShell
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

5. Вы можете получить все классические правила генерации оповещений метрик в группе ресурсов.

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest
    ```

6. Вы можете просмотреть сведения о конкретном классическом правиле генерации оповещения.

    ```PowerShell
    Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Вы можете извлечь все правила генерации оповещений для целевого ресурса. Например, можно извлечь все правила генерации оповещений, установленные для виртуальной машины.

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig

8. You can use the `Add-AlertRule` cmdlet to create, update, or disable an alert rule. You can create email and webhook properties using  `New-AzureRmAlertRuleEmail` and `New-AzureRmAlertRuleWebhook`, respectively. In the Alert rule cmdlet, assign these properties as actions to the **Actions** property of the Alert Rule. The following table describes the parameters and values used to create an alert using a metric.

    | parameter | value |
    | --- | --- |
    | Name |simpletestdiskwrite |
    | Location of this alert rule |East US |
    | ResourceGroup |montest |
    | TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
    | MetricName of the alert that is created |\PhysicalDisk(_Total)\Disk Writes/sec. See the `Get-MetricDefinitions` cmdlet about how to retrieve the exact metric names |
    | operator |GreaterThan |
    | Threshold value (count/sec in for this metric) |1 |
    | WindowSize (hh:mm:ss format) |00:05:00 |
    | aggregator (statistic of the metric, which uses Average count, in this case) |Average |
    | custom emails (string array) |'foo@example.com','bar@example.com' |
    | send email to owners, contributors and readers |-SendToServiceOwners |

9. Create an Email action

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    ```

10. Создание действия веб-перехватчика

    ```PowerShell
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
    ```

## <a name="next-steps"></a>Дополнительная информация

- [Создание классического оповещения метрики с помощью шаблона Resource Manager](monitoring-enable-alerts-using-template.md).
- [Уведомление с помощью веб-перехватчика и классического оповещения метрики для системы за пределами Azure](insights-webhooks-alerts.md).

