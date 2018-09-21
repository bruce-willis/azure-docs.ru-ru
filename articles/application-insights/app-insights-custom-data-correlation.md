---
title: Azure Application Insights | Документация Майкрософт
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: 6d19c64c74a01a012bac3d867e2d65393ae21cd3
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45577777"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Корреляция данных Application Insights с пользовательскими источниками данных

Application Insights собирает несколько разных типов данных: исключения, трассировки, просмотр страниц и другие. Хотя этого достаточно для исследования производительности, надежности и потребления приложения, бывают случаи, когда полезно коррелировать данные, хранящиеся в Application Insights, с другими полностью настраиваемыми наборами данных.

Ситуации, когда можно использовать настраиваемые данные.

- Таблицы обогащения данных или поиска. Например, дополните имя сервера именем владельца сервера и местом расположения задания, в котором оно может быть найдено. 
- Корреляция с источниками данных, не относящихся к Application Insights. Например, сопоставьте данные о покупке в интернет-магазине с информацией из службы выполненных покупок, чтобы определить, насколько точны оценки времени доставки. 
- Полностью настраиваемые данные. Большинство клиентов любят язык запросов и производительность платформы данных Log Analytics, которая поддерживает Application Insights, и хотят использовать ее для запроса данных, которые совсем не связаны с Application Insights. Например, для отслеживания производительности солнечных батарей как части интеллектуальной домашней установки, описанной [здесь]( http://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Корреляция пользовательских данных с данными Application Insights 

Поскольку Application Insights поддерживается мощной платформой данных Log Analytics, для приема данных Log Analytics можно использовать на полную силу. Используя оператор "join", мы напишем запросы, которые будут коррелировать пользовательские данные с данными, доступными в Log Analytics. 

## <a name="ingesting-data"></a>Прием данных

В этом разделе мы рассмотрим, как получить данные в Log Analytics.

Подготовьте новое рабочее пространство Log Analytics, если у вас его еще нет, следуя [этим инструкциям]( https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm), которые включают шаг "создание рабочего пространства".

Начало отправки данных в Log Analytics. Существует несколько вариантов.

- Для синхронного механизма можно напрямую вызвать [API сборщика данных](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) или использовать соединитель Logic App, для этого просто найдите "Azure Log Analytics" и выберите вариант "Отправить данные".

 ![Снимок экрана "Выбор и действие"](./media/app-insights-custom-data-correlation/01-logic-app-connector.png)  

- Для асинхронного варианта используйте API сборщика данных для сборки конвейера обработки. Подробные сведения можно найти в [этой статье](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api).

## <a name="correlating-data"></a>Корреляция данных

Служба Application Insights основана на платформе данных Log Analytics. Поэтому можно использовать [межресурсные соединения](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search), чтобы сопоставить любые данные, которые были внесены в Log Analytics с ваших данных Application Insights.

Например, можно принимать реестр заданий и местоположения в таблице под названием LabLocations_CL в рабочем пространстве Log Analytics под названием myLA. Чтобы просмотреть запросы, отслеживаемые в приложении Application Insights, называемое myAI, и сопоставить имена машин, которые обслуживали запросы в местах расположения этих машин, хранящиеся в ранее упомянутой пользовательской таблице, нужно запустить следующий запрос из Application Insights или контекста Log Analytics.

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения см. по ссылке [​​API сборщика данных](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).
- Дополнительные сведения о [межресурсном соединении](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).
