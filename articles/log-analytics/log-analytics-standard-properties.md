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
ms.date: 09/27/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: bcc5f23c163a391639d916b8a50c2c05d228ee91
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432092"
---
# <a name="standard-properties-in-log-analytics-records"></a>Стандартные свойства в записях Log Analytics
Данные в [Log Analytics](../log-analytics/log-analytics-queries.md) хранятся в виде набора записей. Каждый набор имеет определенный тип данных с уникальным набором свойств. Большинство типов данных имеют стандартные свойства, которые являются общими для нескольких типов. В этой статье описаны эти свойства и приведены примеры по их использованию в запросах.

Некоторые из этих свойств еще находятся в процессе реализации, поэтому они могут отображаться только для отдельных типов данных.

## <a name="timegenerated"></a>TimeGenerated
Свойство **TimeGenerated** содержит дату и время создания записи. Это общее свойство позволяет фильтровать и суммировать данные по времени. Если диапазон времени выбран для представления или на панели мониторинга на портале Azure, результаты будут отфильтрованы с помощью TimeGenerated.

### <a name="examples"></a>Примеры

Указанный ниже запрос возвращает количество событий с ошибками за каждый день на прошлой неделе.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

## <a name="type"></a>type
Свойство **Type** содержит имя таблицы, из которой извлечена запись. Это имя также является типом записи. Это свойство можно использовать в запросах, где объединяются записи из нескольких таблиц, например использующих оператор `search`, чтобы различать записи разных типов. В некоторых случаях вместо **Type** можно использовать **$table**.

### <a name="examples"></a>Примеры
Указанный ниже запрос возвращает количество записей по типу, собранных за последний час.

```Kusto
search * 
| where TimeGenerated > ago(1h) 
| summarize count() by Type 
```

## <a name="resourceid"></a>\_ResourceId
Свойство **\_ResourceId** содержит уникальный идентификатор для ресурса, с которым связана запись. Это стандартное свойство можно использовать, чтобы выполнить запрос только к записям из определенного ресурса или объединить связанные данные из нескольких таблиц.

Для ресурсов Azure значением параметра **_ResourceId** будет [URL-адрес идентификатора ресурса Azure](../azure-resource-manager/resource-group-template-functions-resource.md). Сейчас это свойство поддерживается только для ресурсов Azure, но в будущем оно будет доступно и для ресурсов за пределами Azure, например локальных компьютеров.

> [!NOTE]
> Некоторые типы данных уже содержат поля с идентификатором ресурса Azure или его частями, например идентификатором подписки. Хотя эти поля нужны для обеспечения обратной совместимости, рекомендуем использовать свойство _ResourceId для перекрестной корреляции, так как в этом случае она будет более согласованной.

### <a name="examples"></a>Примеры
Указанный ниже запрос объединяет данные о производительности и событиях для каждого компьютера. В результатах показаны все события с идентификатором _101_ и загрузкой процессора больше 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

Указанный ниже запрос объединяет записи _AzureActivity_ и записи _SecurityEvent_. В результатах показаны все операции с пользователями, которые вошли на эти виртуальные машины.

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
