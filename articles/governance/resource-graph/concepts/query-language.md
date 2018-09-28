---
title: Общие сведения о языке запросов графика ресурсов Azure
description: Сведения об использовании языка запросов для графика ресурсов Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: 3600fc47a0fb318a49c1b37722cb7fffa51ec6f2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951956"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Общие сведения о языке запросов графика ресурсов Azure

Язык запросов для графика ресурсов Azure поддерживает ряд операторов и функций. Они работают так же, как элементы языка запросов Kusto (KQL). Тем не менее важно понимать, что, хотя язык запросов графика ресурсов аналогичен [KQL](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators), они различаются.

> [!NOTE]
> Ссылки на документацию KQL могут требовать проверки подлинности.

Для начала работы с языком запросов, используемым графиком ресурсов, рекомендуется изучить документацию по KQL, чтобы получить представление о структуре языка и взаимодействии различных поддерживаемых операторов и функций.

## <a name="supported-tabular-operators"></a>Поддерживаемые табличные операторы

Ниже приведен список поддерживаемых табличных операторов в графике ресурсов:

- [distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/distinct-operator)
- [extend](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/extend-operator)
- [limit](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/limit-operator)
- [order by](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/order-operator)
- [project](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-operator)
- [project-away](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/project-away-operator)
- [sample](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-operator)
- [sample-distinct](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sample-distinct-operator)
- [sort by](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/sort-operator)
- [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator)
- [take](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/take-operator)
- [В начало](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-operator)
- [top-nested](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-nested-operator)
- [top-hitters](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/top-hitters-operator)
- [where](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/where-operator)

## <a name="supported-functions"></a>Поддерживаемые функции

Ниже приведен список поддерживаемых функций в графике ресурсов:

- [ago()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago%28%29)
- [buldschema()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/buildschema%28%29)
- [count()](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/count%28%29)
- [strcat()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/strcat%28%29)
- [isnotempty()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/isnotempty%28%29_-notempty%28%29)
- [tostring()](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/tostring%28%29)

## <a name="next-steps"></a>Дополнительная информация

- См. описание используемого языка в разделе [Запросы для начинающих](../samples/starter.md)
- См. описание расширенных вариантов использования в разделе [Расширенные запросы](../samples/advanced.md)
- Подробнее о [просмотре ресурсов](explore-resources.md)