---
title: Справочник по языку в Log Analytics Azure Monitor | Документация Майкрософт
description: Справочные сведения по языку Kusto в Log Analytics. Дополнительные элементы, относящиеся к Log Analytics, а также элементы, которые не поддерживаются в запросах Log Analytics.
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
ms.date: 09/25/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5173790436a29fa9947346d711da1a2ddb32bf62
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451074"
---
# <a name="log-analytics-query-language-reference"></a>Справочник по языку запросов Log Analytics
В [запросах Log Analytics](../log-analytics-queries.md) используется тот же язык и механизм, что и в [Обозревателе данных Azure](/azure/data-explorer/). Справочник по языку и другие сведения о языке см. на этой странице: [Справочник по языку Kusto](/azure/kusto/query).



## <a name="kusto-elements-not-support-in-log-analytics"></a>Элементы Kusto, неподдерживаемые в Log Analytics
Хотя в запросах Log Analytics реализуется служба Kusto, некоторые ее элементы не поддерживаются. См. описание ниже.

### <a name="statements-not-supported-in-log-analytics"></a>Инструкции, которые не поддерживаются в Log Analytics:

* [псевдоним](/kusto/query/aliasstatement);
* [параметры запроса](/azure/kusto/query/queryparametersstatement).

### <a name="functions-not-supported-in-log-analytics"></a>Функции, которые не поддерживаются в Log Analytics:

* [cluster()](/azure/kusto/query/clusterfunction);
* [cursor_after()](/azure/kusto/query/cursorafterfunction);
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction);
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent);
* [database()](/azure/kusto/query/databasefunction);
* [current_principal()](/azure/kusto/query/current-principalfunction);
* [extent_id()](/azure/kusto/query/extentidfunction);
* [extent_tags()](/azure/kusto/query/extenttagsfunction).

### <a name="operators-not-supported-in-log-analytics"></a>Операторы, которые не поддерживаются в Log Analytics:

* [соединение между кластерами](/azure/kusto/query/joincrosscluster);
* [оператор externaldata](/azure/kusto/query/externaldata-operator).

### <a name="plugins-not-supported-in-log-analytics"></a>Подключаемые модули, которые не поддерживаются в Log Analytics:

* [модуль sql_request](/azure/kusto/query/sqlrequestplugin).


## <a name="additional-operators-in-log-analytics"></a>Дополнительные операторы в Log Analytics
Чтобы обеспечить поддержку некоторых функций, в Log Analytics добавлены следующие операторы Kusto, которые недоступны вне этой службы: 

* [app()](app-expression.md);
* [workspace()](workspace-expression.md).

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с дополнительными сведениями о запросах в [Log Analytics](../log-analytics-queries.md).
- Пройдите урок по написанию [запроса в Log Analytics](/log-analytics/query-language/get-started-queries.md).
- Ознакомьтесь с полной [справочной документацией по Kusto](/azure/kusto/query/).