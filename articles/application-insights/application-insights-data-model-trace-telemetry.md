---
title: Модель данных телеметрии Azure Application Insights — телеметрия трассировки | Документы Майкрософт
description: Модель данных Application Insights для телеметрии трассировки
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
ms.openlocfilehash: 2487df93baec80c0e4f7741aa7b052ac89459b3b
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35640081"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Телеметрия трассировки: модель данных Application Insights

Телеметрия трассировки (в [Application Insights](app-insights-overview.md)) представляет инструкции трассировки стиля `printf`, доступные для текстового поиска. `Log4Net`, `NLog` и другие записи файла журнала на основе текста преобразуются в экземпляры этого типа. Трассировка не имеет измерений в качестве расширения.

## <a name="message"></a>Сообщение

Сообщение трассировки.

Максимальная длина: 32 768 символов

## <a name="severity-level"></a>Уровень серьезности

Уровень серьезности трассировки. Возможные значения: `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Пользовательские свойства

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Дополнительная информация

- [Просмотр журналов трассировки .NET в Application Insights](app-insights-asp-net-trace-logs.md).
- [Просмотр журналов трассировки Java в Application Insights](app-insights-java-trace-logs.md).
- В [этой статье](application-insights-data-model.md) представлены типы данных и модель данных для Application Insights.
- [Написание пользовательской телеметрии трассировки](app-insights-api-custom-events-metrics.md#tracktrace)
- Ознакомление с [платформами](app-insights-platforms.md), поддерживаемыми Application Insights.
