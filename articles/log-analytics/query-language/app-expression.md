---
title: Выражение app() в запросах Azure Log Analytics | Документация Майкрософт
description: Выражение app используется в запросах Log Analytics для получения данных из определенного приложения Application Insights, находящегося в той же или другой группе ресурсов либо в другой подписке.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: d91e148320c4c59bb888975499aa1de16ffbf134
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955067"
---
# <a name="app-expression-in-log-analytics-query"></a>Выражение app() в запросах Log Analytics

Выражение `app` используется в запросах Log Analytics для получения данных из определенного приложения Application Insights, находящегося в той же или другой группе ресурсов либо в другой подписке. Его удобно использовать для добавления данных приложения в запрос Log Analytics и запрашивания данных из нескольких приложений с помощью запроса Application Insights.



## <a name="syntax"></a>Синтаксис

`app(`*Идентификатор*`)`


## <a name="arguments"></a>Аргументы

- *Идентификатор* идентифицирует приложение с помощью одного форматов, представленных в таблице ниже.

| Идентификатор | ОПИСАНИЕ | Пример
|:---|:---|:---|
| Имя ресурса | Понятное для человека имя приложения (или имя компонента) | app("fabrikamapp") |
| Полное имя | Полное имя приложения в формате subscriptionName/resourceGroup/componentName | app('AI-Prototype/Fabrikam/fabrikamapp') |
| ИД | GUID приложения | app("988ba129-363e-4415-8fe7-8cbab5447518") |
| Идентификатор ресурса Azure | Идентификатор ресурса Azure |app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Примечания

* У вас должен быть доступ на чтение приложения.
* Для идентификации приложения по имени его имя должно быть уникальным во всех доступных подписках. Если у вас есть несколько приложений с таким именем, запрос не будет выполнен из-за неоднозначности. В этом случае необходимо воспользоваться другим идентификатором.
* Связанное выражение [workspace](workspace-expression.md) используется для запрашивания данных из рабочих областей Log Analytics.

## <a name="examples"></a>Примеры

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения см. в статье [об использовании выражения workspace](workspace-expression.md) для обозначения рабочей области Azure Log Analytics.
- Дополнительные сведения о хранении [данных в Log Analytics](../../log-analytics/log-analytics-log-search.md).