---
title: Стандартные свойства записей Log Analytics в Azure Monitor | Документация Майкрософт
description: Описание свойств, общих для многих типов данных Log Analytics в Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 663f0b04c528c180e4130c1c157441cbc0ceb98b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955873"
---
# <a name="standard-properties-in-log-analytics-records"></a>Стандартные свойства в записях Log Analytics
Данные в [Log Analytics](../log-analytics/log-analytics-queries.md) хранятся в виде набора записей. Каждый набор имеет определенный тип данных с уникальным набором свойств. Большинство типов данных имеют стандартные свойства, которые являются общими для нескольких типов. В этой статье описаны эти свойства и приведены примеры по их использованию в запросах.

Некоторые из этих свойств еще находятся в процессе реализации, поэтому они могут отображаться только для отдельных типов данных.


## <a name="resourceid"></a>_ResourceId
Свойство **_ResourceId** содержит уникальный идентификатор для ресурса, с которым связана запись. Это стандартное свойство можно использовать, чтобы выполнить запрос только к записям из определенного ресурса или объединить связанные данные из нескольких таблиц.

Для ресурсов Azure значением параметра **_ResourceId** будет [URL-адрес идентификатора ресурса Azure](../azure-resource-manager/resource-group-template-functions-resource.md). Сейчас это свойство поддерживается только для ресурсов Azure, но в будущем оно будет доступно и для ресурсов за пределами Azure, например локальных компьютеров. 

### <a name="examples"></a>Примеры
В примере ниже показан запрос, объединяющий данные о производительности и событиях для каждого компьютера. В результатах показаны все события с идентификатором _101_ и загрузкой процессора больше 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

В примере ниже показан запрос, объединяющий записи _AzureActivity_ с записями _SecurityEvent_. В результатах показаны все операции с пользователями, которые вошли на эти виртуальные машины.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о [хранении данных Log Analytics](../log-analytics/log-analytics-queries.md).
- Пройдите урок по [написанию запросов в Log Analytics](../log-analytics/query-language/get-started-queries.md).
- Пройдите урок по [объединению таблиц в запросах Log Analytics](../log-analytics/query-language/joins.md).