---
title: Управление смарт-группами
description: Управление смарт-группами, созданными на основе экземпляров оповещений
author: anantr
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: d04684e7d4eed1b997ec803dc9ccc1dcc079bde0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46959382"
---
# <a name="manage-smart-groups"></a>Управление смарт-группами
[Смарт-группы](https://aka.ms/smart-groups) используют алгоритмы машинного обучения, чтобы объединять оповещения в группы на основе их совместного появления или подобия. Это позволяет управлять оповещениями не по отдельности, а на уровне всей смарт-группы. Из этой статьи вы узнаете, как работать со смарт-группами в Azure Monitor.
1.  Просмотреть смарт-группы, сформированные из экземпляров оповещений, можно двумя способами.
     1. Щелкните **Смарт-группы** на странице **Сводка по оповещениям**.    
    ![Мониторинг](./media/monitoring-alerts-managing-smart-groups/sg-alerts-summary.jpg)
     2. Щелкните Alerts by Smart Groups (Оповещения по смарт-группам) на странице "Все оповещения".   
     ![Мониторинг](./media/monitoring-alerts-managing-smart-groups/sg-all-alerts.jpg)
2.  Отобразится список всех смарт-групп, созданных из экземпляров оповещений. Теперь вместо работы с индивидуальными оповещениями можно оперировать их смарт-группами.   
![Мониторинг](./media/monitoring-alerts-managing-smart-groups/sg-list.jpg)
3.  При щелчке смарт-группы откроется страница со сведениями о ней, такими как критерий группировки, а также списком всех оповещений в группе. Такое объединение позволяет сконцентрироваться на отдельных группах оповещений, а не работать со множеством индивидуальных оповещений.   
![Мониторинг](./media/monitoring-alerts-managing-smart-groups/sg-details.jpg)

