---
title: Модель данных телеметрии Azure Application Insights — телеметрия исключений | Документы Майкрософт
description: Модель данных Application Insights для телеметрии исключений
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: dc5480b90ef6b5520f47c51f0c105202d7071089
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47093599"
---
# <a name="exception-telemetry-application-insights-data-model"></a>Телеметрия исключений: модель данных Application Insights

В [Application Insights](app-insights-overview.md) экземпляр исключения представляет обработанное или необработанное исключение, возникшее во время выполнения отслеживаемого приложения.

## <a name="problem-id"></a>Идентификатор проблемы

Идентификатор места в коде, где возникло исключение. Используется для группирования исключений. Обычно это сочетание типа исключения и функции из стека вызовов.

Максимальная длина: 1024 символа

## <a name="severity-level"></a>Уровень серьезности

Уровень серьезности трассировки. Возможные значения: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="exception-details"></a>Сведения об исключении

(Эти сведения будут добавлены позже.)

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Пользовательские измерения

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Дополнительная информация

- В [этой статье](application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- Вы можете научиться [диагностировать исключения в веб-приложениях с помощью Application Insights](app-insights-asp-net-exceptions.md).
- Ознакомление с [платформами](app-insights-platforms.md), поддерживаемыми Application Insights.
