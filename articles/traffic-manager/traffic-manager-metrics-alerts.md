---
title: Метрики и оповещения в диспетчере трафика Azure | Документация Майкрософт
description: В этой статье описываются метрики, доступные для диспетчера трафика в Azure.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: kumud
ms.openlocfilehash: fe4cc3c06af6868396f5db6fd88804022f5f9793
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47432483"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Метрики и оповещения диспетчера трафика

Диспетчер трафика предоставляет балансировку нагрузки на основе DNS, включающую в себя несколько методов маршрутизации и параметров мониторинга конечных точек. В этой статье описаны метрики и соответствующие оповещения, доступные для клиентов. 

## <a name="metrics-available-in-traffic-manager"></a>Доступные в диспетчере трафика метрики 

Диспетчер трафика предоставляет следующие метрики на основе каждого профиля, с помощью которых клиенты могут узнать уровень использования диспетчера трафика, а также состояние конечных точек в профиле.  

### <a name="queries-by-endpoint-returned"></a>Запросы, выполняемые возвращаемой конечной точкой
[Эта метрика](../monitoring-and-diagnostics/monitoring-supported-metrics.md) позволяет просмотреть количество запросов, обработанных профилем диспетчера трафика за указанный период. Вы также можете просмотреть эти сведения на уровне детализации конечной точки. Это поможет понять, сколько раз конечная точка возвращалась в ответах на запросы из диспетчера трафика.

В следующем примере (на рис. 1) показаны все ответы на запросы, возвращенные профилем диспетчера трафика. 

  
![Метрики диспетчера трафика — статистическое представление всех запросов](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Рис. 1. Статистическое представление всех запросов*
  
На рис. 2 отображаются те же сведения, но разделенные по конечным точкам. В результате отображаются все ответы на запросы, в которых возвращена конкретная конечная точка.

![Метрики диспетчера трафика — разделенное представление всех запросов на каждую конечную точку](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Рис. 2. Разделенное представление всех запросов для возвращенной конечной точки*

## <a name="endpoint-status-by-endpoint"></a>Проверка состояния конечных точек
[Эта метрика](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) позволяет проверить состояние работоспособности конечных точек в профиле. Эта метрика имеет два значения:
 - используйте **1**, если конечная точка работает;
 - используйте **0**, если конечная точка не работает.

Эту метрику можно просмотреть как статистическое значение, представляющее состояние всех метрик (рис. 3), или в разделенном представлении (см. рис. 4) для отображения состояния определенных конечных точек. В первом случае, если выбран уровень агрегирования **Сред.**, значением этой метрики является среднее арифметическое значение состояния всех конечных точек. Например, если в профиле имеется две конечные точки и только одна из них является работоспособной, эта метрика будет иметь значение **0,50** (как показано на рис. 3). 


![Метрики диспетчера трафика — составное представление состояния конечной точки](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Рис. 3. Составное представление метрики состояния конечной точки с выбранным значением агрегирования "Сред."*


![Метрики диспетчера трафика — разделенное представление состояния конечной точки](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Рис. 4. Разделенное представление метрик состояния конечной точки*

Вы можете использовать эти метрики на портале [службы Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md) с помощью [REST API](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor) и [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights), а также в разделе метрики при работе на портале диспетчера трафика.

## <a name="alerts-on-traffic-manager-metrics"></a>Оповещения о метриках диспетчера трафика
Помимо обработки и отображения метрик из диспетчера трафика, Azure Monitor позволяет клиентам настроить и получать оповещения, связанные с этими метриками. Вы можете выбрать для метрик условия, при выполнении которых будет отправляться оповещение, частоту мониторинга этих условий и способ отправки оповещений. Дополнительные сведения см. в [документации по оповещениям Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Дополнительная информация
- Узнайте больше о службе [Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).
- Узнайте, как [создать диаграмму с помощью Azure Monitor](../monitoring-and-diagnostics/monitoring-metric-charts.md#how-do-i-create-a-new-chart).
