---
title: Мониторинг службы "Фабрика данных Azure" с помощью OMS | Документация Майкрософт
description: Узнайте, как отслеживать службу "Фабрика данных Azure", направляя данные в Operations Management Suite (OMS) для анализа.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: douglasl
ms.openlocfilehash: fb5da75d0e42dd0700cad008d348ff846d602409
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42143765"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>Мониторинг службы "Фабрика данных Azure" с помощью Operations Management Suite (OMS)

Можно использовать интеграцию службы "Фабрика данных Azure" с Azure Monitor для передачи данных в Operations Management Suite (OMS). Это удобно в следующих случаях:

1.  Требуется создавать сложные запросы по обширному набору метрик, публикуемых службой "Фабрика данных" в OMS. Можно также создавать настраиваемые оповещения на основе этих запросов с помощью OMS.

2.  Требуется отслеживать фабрики данных. Может направлять данные из нескольких фабрик данных в одну рабочую область OMS.

Уделите 7 минут своего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

## <a name="get-started"></a>Начало работы

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Настройка параметров диагностики и рабочей области OMS

Включите параметры диагностики для фабрики данных.

1.  Выберите **Azure Monitor** -> **Параметры диагностики**, выберите фабрику данных и щелкните "Включить диагностику".

    ![monitor-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Укажите параметры диагностики, включая конфигурацию рабочей области OMS.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Установка пакета Azure Data Factory Analytics для OMS из Azure Marketplace

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Щелкните **Создать** и выберите рабочую область OMS и ее параметры.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Мониторинг метрик службы "Фабрика данных Azure" с помощью OMS

При установке пакета **Azure Data Factory Analytics** для OMS создается стандартный набор представлений, позволяющий использовать следующие метрики:

- выполнение ADF: 1) Pipeline Runs by Data Factory;

- выполнение ADF: 2) Activity Runs by Data Factory;

- выполнение ADF: 3) Trigger Runs by Data Factory;

- ошибки ADF: 1) Top 10 Pipeline Errors by Data Factory;

- ошибки ADF: 2) Top 10 Activity Errors by Data Factory;

- ошибки ADF: 3) Top 10 Trigger Errors by Data Factory;

- статистика ADF: 1) Activity Runs by Type;

- статистика ADF: 2) Trigger Runs by Type;

- статистика ADF: 3) Max Pipeline Runs Duration.

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Можно визуализировать приведенные выше метрики, просматривать запросы, использующие эти метрики, изменять запросы, создавать оповещения и пр.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о мониторинге конвейеров и управлении ими с помощью сценариев см. в статье [Отслеживание фабрики данных Azure](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically).
