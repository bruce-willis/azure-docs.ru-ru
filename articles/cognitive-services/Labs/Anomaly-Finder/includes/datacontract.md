---
title: включение файла
description: включение файла
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: e37d3ef5b6f65ad31bc19f9f8c15350014d1c9ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381328"
---
С помощью [API средства для поиска аномалий](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder) можно отправить данные временных рядов в формате JSON на конечную точку API, а затем считать результат из ответа API. При отправке данных временных рядов каждая точка данных включает следующие элементы:  
* Timestamp — метка времени для точки данных. Убедитесь, что используется строка даты и времени в формате UTC, например 2017-08-01T00:00:00Z.
* Value — значение этой точки данных.

Результаты состоят из следующих элементов:
* Period — периодичность, с которой API обнаруживает аномалии.
* WarningText — возможные сведения о предупреждении.
* ExpectedValue — прогнозируемое значение по модели на основе обучения.
* IsAnomaly — результат, указывающий, являются ли точки данных аномалиями в обоих направлениях (пиками или спадами).
* IsAnomaly_Neg — признак, указывающий, являются ли точки данных аномалиями в отрицательном направлении (спадами).
* IsAnomaly_Pos — признак, указывающий, являются ли точки данных аномалиями в положительном направлении (пиками).
* UpperMargin — сумма значений ExpectedValue и UpperMargin определяет верхнюю границу, в пределах которой точка данных еще считается нормальной.
* LowerMargin — разность значений ExpectedValue и LowerMargin определяет нижнюю границу, в пределах которой точка данных еще считается нормальной.

Подробные сведения о контракте данных см. [здесь](../apiref.md).

