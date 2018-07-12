---
title: Панель мониторинга общих сведений об Azure Application Insights | Документация Майкрософт
description: Мониторинг приложений с помощью Azure Application Insights и функции "Панель мониторинга общих сведений".
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: mbullwin
ms.openlocfilehash: cf7c151121ddf8b2dc57d5db66a62f4d46e463de
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970978"
---
# <a name="application-insights-overview-dashboard"></a>Обзорная панель мониторинга Application Insights

Application Insights всегда предоставляло сводную панель обзора, чтобы быстро мгновенно оценить работоспособность и производительность приложения. Новая обзорная панель мониторинга предоставляет более гибкие возможности.

## <a name="how-do-i-test-out-the-new-experience"></a>Как протестировать новую возможность?

Новая обзорная панель мониторинга теперь запускается по умолчанию:

![Область панели мониторинга общих сведений](.\media\app-insights-overview-dashboard\overview.png)

## <a name="better-performance"></a>Повышенная производительность

Выбор диапазона времени упростили, создав интерфейс с возможностью работы одним щелчком.

![Диапазон времени](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

В целом производительность значительно увеличилась. Вы можете одним щелчком получить доступ к популярным функциям, таким как **Поиск** и **Аналитика**. Каждый динамически обновляемый элемент ключевого показателя эффективности по умолчанию предоставляет сведения о соответствующих компонентах Application Insights. Чтобы подробнее узнать о неудачных запросах, выберите **Сбои** в заголовке **Исследовать**:

![Сбои](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Панель мониторинга приложений

Панель мониторинга приложения использует существующие технологии панели мониторинга в пределах Azure для предоставления полностью настраиваемого представления панели со сведениями о работоспособности и производительности приложения.

Чтобы получить доступ к панели мониторинга по умолчанию, выберите в левом верхнем углу _Панель мониторинга приложения_.

![Представление панели мониторинга](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Если это ваш первый доступ к панели мониторинга, то будет запущено представление по умолчанию:

![Представление панели мониторинга](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

Вы можете сохранить представление по умолчанию, если оно вам понравилось. Кроме того, можно добавить и удалить элементы на панели мониторинга для оптимального соответствия потребностям вашей команды.

> [!NOTE]
> Все пользователи, имеющие доступ к ресурсу Application Insights, используют одинаковые возможности панели мониторинга приложений. Изменения, внесенные одним пользователем, влияют на представление для всех пользователей.

Чтобы вернуться к обзору возможностей просто выберите следующее:

![Кнопка "Обзор"](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>Дополнительная информация

- [Воронки](usage-funnels.md)
- [Сохранение](app-insights-usage-retention.md)
- [Средство "Маршруты пользователей"](app-insights-usage-flows.md)
- [Панели мониторинга](app-insights-dashboards.md)
