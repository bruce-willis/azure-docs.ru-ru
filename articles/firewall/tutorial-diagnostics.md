---
title: Руководство. Журналы мониторинга брандмауэра Azure
description: В этом руководстве содержится информация о том, как включить журналы брандмауэра Azure и управлять ими.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991965"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>Руководство. Журналы мониторинга брандмауэра Azure

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Примеры в статьях о брандмауэре Azure предполагают, что общедоступная предварительная версия брандмауэра Azure уже активирована. Дополнительные сведения см. в статье [Enable the Azure Firewall public preview](public-preview.md) (Включение общедоступной предварительной версии брандмауэра Azure).

Работу брандмауэра Azure можно отслеживать с помощью журналов брандмауэра. Также журналы действий можно использовать для аудита операций на ресурсах брандмауэра Azure.

Доступ к некоторым из этих журналов можно получить через портал. Журналы можно передавать в [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), службу хранилища, концентраторы событий, а затем анализировать в Log Analytics или при помощи различных инструментов, таких как Excel и Power BI.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Включение ведения журнала на портале Azure
> * Включение ведения журнала с помощью PowerShell
> * Просмотр и анализ журнала действий
> * Просмотр и анализ журналов правил сети и приложений

## <a name="diagnostic-logs"></a>Журналы диагностики

 Для брандмауэра Azure доступны следующие журналы диагностики.

* **Журнал правил приложений**

   Журнал правил приложений сохраняется в учетную запись хранения, передается в концентраторы событий или отправляется в Log Analytics только в том случае, если он включен для каждого брандмауэра Azure. Результаты каждого нового подключения, которое соответствует одному из настроенных правил приложения, находятся в журнале принятого или отклоненного подключения. Данные регистрируются в журнале в формате JSON, как показано в примере ниже.

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Журнал правил сети**

   Журнал правил сети сохраняется в учетную запись хранения, передается в концентраторы событий или отправляется Log Analytics только в том случае, если он включен для каждого брандмауэра Azure. Каждое новое подключение, которое соответствует одному из настроенных правил сети, регистрируется в журнале как принятое или отклоненное подключение. Данные регистрируются в журнале в формате JSON, как показано в примере ниже.

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Существует три способа хранения журналов:

* **Учетная запись хранения** лучше всего подходит для длительного хранения журналов и их просмотра по мере необходимости.
* **Концентраторы событий** — это отличный вариант для интеграции с другими инструментами управления событиями и сведениями о безопасности (SEIM), позволяющий получать оповещения о ваших ресурсах.
* **Log Analytics** лучше всего подходит для общего мониторинга приложения в реальном времени и изучения тенденций.

## <a name="activity-logs"></a>Журналы действий

   Записи этого журнала собираются по умолчанию, и их можно просмотреть на портале Azure.

   В [журналах действий Azure](../azure-resource-manager/resource-group-audit.md) (прежнее название — операционные журналы и журналы аудита) можно просматривать все операции, отправляемые в подписку Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Включение журнала ведения диагностики на портале Azure

Для включения ведения журнала диагностики потребуется несколько минут, чтобы данные появились в журналах после завершения этой процедуры. Если ничего не происходит, проверьте еще раз через несколько минут.

1. На портале Azure откройте группу ресурсов брандмауэра, а затем щелкните брандмауэр.
2. В разделе **Мониторинг** щелкните **Журналы диагностики**.

   Для брандмауэра Azure доступны два журнала обслуживания.

   * Журнал правил приложений
   * Журнал правил сети

3. Щелкните **Включить диагностику**, чтобы начать сбор данных.
4. На странице **Параметры диагностики** представлены параметры журналов диагностики. 
5. В этом примере журналы хранятся в Log Analytics, поэтому введите **firewall Log Analytics**.
6. Чтобы настроить рабочую область, щелкните **Отправить в Log Analytics**. Для хранения журналов диагностики можно также использовать концентраторы событий и учетную запись хранения.
7. В разделе **Log Analytics** щелкните **Настроить**.
8. На странице "Рабочие области OMS" щелкните **Создать рабочую область**.
9. На странице **Рабочая область Log Analytics** введите **брандмауэр OMS**, чтобы дать новое имя **рабочей области OMS**.
10. Выберите свою подписку, используя существующую группу ресурсов брандмауэра (**Test-FW-RG**), выберите расположение **восточная часть США**и выберите **бесплатную**ценовую категорию.
11. Последовательно выберите **ОК**.
   ![Начало процесса настройки][1]
12. В разделе **Журнал** щелкните **AzureFirewallApplicationRule** и **AzureFirewallNetworkRule**, чтобы собрать журналы для правил приложения и сети.
   ![Сохранение параметров диагностики][2]
13. Выберите команду **Сохранить**.

## <a name="enable-logging-with-powershell"></a>Включение ведения журнала с помощью PowerShell

Ведение журнала действий автоматически включается для каждого ресурса Resource Manager. Чтобы начать сбор соответствующих данных, журнал ведения диагностики должен быть включен.

Чтобы включить ведение журналов диагностики, выполните следующие действия.

1. Запишите или запомните ИД ресурса учетной записи хранения, где хранятся данные журнала, в формате: */subscriptions/\<идентификатор_подписки\>/resourceGroups/\<имя_группы_ресурсов\>/providers/Microsoft.Storage/storageAccounts/\<имя_учетной_записи_хранения\>*.

   Можно использовать любую учетную запись хранения в подписке. Получить эти сведения можно на портале Azure. Нужные сведения можно найти на странице **Свойство** ресурса.

2. Запишите или запомните идентификатор ресурса брандмауэра, для которого нужно включить ведение журнала, в формате: */subscriptions/\<идентификатор_подписки\>/resourceGroups\</имя_группы_ресурсов\>/providers/Microsoft.Network/azureFirewalls/\<имя_брандмауэра\>*.

   Получить эти сведения можно на портале.

3. Включите ведение журнала диагностики с помощью следующего командлета PowerShell:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Журналам диагностики отдельная учетная запись хранения не требуется. За использование хранилища для журналов доступа и производительности взимается плата.

## <a name="view-and-analyze-the-activity-log"></a>Просмотр и анализ журнала действий

Данные журнала действий можно просматривать и анализировать с помощью любого из следующих методов:

* **Средства Azure.** Информацию из журналов действий можно получать с помощью Azure PowerShell, Azure CLI, REST API Azure или портала Azure. Пошаговые инструкции для каждого метода подробно описаны в статье [Activity operations with Resource Manager](../azure-resource-manager/resource-group-audit.md) (Выполнение операций в журналах действий с помощью Resource Manager).
* **Power BI.** Если у вас еще нет учетной записи [Power BI](https://powerbi.microsoft.com/pricing), вы можете использовать бесплатную пробную версию. Используя [пакет содержимого журналов действий Azure для Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), можно анализировать данные с помощью предварительно настроенных панелей мониторинга, которые можно использовать "как есть" или дополнительно настроить.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Просмотр и анализ журналов правил сети и приложений

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) собирает файлы счетчика и журнала событий. Эта служба предоставляет средства визуализации и эффективные возможности поиска для анализа журналов.

Вы также можете подключиться к учетной записи хранения и извлечь записи журнала JSON для журналов доступа и производительности. После скачивания JSON-файлов их можно преобразовать в формат CSV и просматривать в Excel, Power BI или другом средстве визуализации данных.

> [!TIP]
> Если вы знакомы с Visual Studio и основными понятиями изменения значений констант и переменных в C#, можно использовать [инструменты преобразования журналов](https://github.com/Azure-Samples/networking-dotnet-log-converter), доступные на сайте GitHub.


## <a name="next-steps"></a>Дополнительная информация

Теперь, когда брандмауэр для сбора журналов настроен, можно открыть Log Anaytics, чтобы просмотреть данные.

> [!div class="nextstepaction"]
> [Решения для мониторинга сетей в Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
