---
title: Общие сведения о журналах Брандмауэра Azure
description: Эта статья содержит общие сведения о журналах диагностики Брандмауэра Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 6d8d0b2dc0f6baf48d2aacb9c7203937aef08d15
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957803"
---
# <a name="azure-firewall-logs"></a>Журналы Брандмауэра Azure

Работу брандмауэра Azure можно отслеживать с помощью журналов брандмауэра. Также журналы действий можно использовать для аудита операций на ресурсах брандмауэра Azure.

Доступ к некоторым из этих журналов можно получить через портал. Журналы можно передавать в [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), службу хранилища, Центры событий, а затем анализировать в Log Analytics или при помощи различных инструментов, таких как Excel и Power BI.

## <a name="diagnostic-logs"></a>Журналы диагностики

 Для брандмауэра Azure доступны следующие журналы диагностики.

* **Журнал правил приложений**

   Журнал правил приложений сохраняется в учетную запись хранения, передается в Центры событий или отправляется в Log Analytics только в том случае, если он включен для каждого брандмауэра Azure. Результаты каждого нового подключения, которое соответствует одному из настроенных правил приложения, находятся в журнале принятого или отклоненного подключения. Данные регистрируются в журнале в формате JSON, как показано в примере ниже.

   ```
   Category: application rule logs.
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

   Журнал правил сети сохраняется в учетную запись хранения, передается в Центры событий или отправляется Log Analytics только в том случае, если он включен для каждого брандмауэра Azure. Каждое новое подключение, которое соответствует одному из настроенных правил сети, регистрируется в журнале как принятое или отклоненное подключение. Данные регистрируются в журнале в формате JSON, как показано в примере ниже.

   ```
   Category: network rule logs.
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
* **Центры событий** — это отличный вариант для интеграции с другими инструментами управления событиями и сведениями о безопасности (SEIM), позволяющий получать оповещения о ваших ресурсах.
* **Log Analytics** лучше всего подходит для общего мониторинга приложения в реальном времени и изучения тенденций.

## <a name="activity-logs"></a>Журналы действий

   Записи этого журнала собираются по умолчанию, и их можно просмотреть на портале Azure.

   В [журналах действий Azure](../azure-resource-manager/resource-group-audit.md) (прежнее название — операционные журналы и журналы аудита) можно просматривать все операции, отправляемые в подписку Azure.


## <a name="next-steps"></a>Дополнительная информация

См. дополнительные сведения об [отслеживании метрик и журналов Брандмауэра Azure](tutorial-diagnostics.md).