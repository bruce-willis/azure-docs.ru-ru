---
title: включение файла
description: включение файла
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a1cda1cea2089363331ae437cb7ad802429779f4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888702"
---
Возвращенные данные с ожидаемым значением и верхней и нижней границами по умолчанию. На практике можно определить параметр [sensitivity], а затем использовать (ExpectedValue + sensitivity * UpperMargin) в качестве верхней границы, а (ExpectedValue - sensitivity * LowerMargin) — в качестве нижней границы для самостоятельной настройки точек аномалий. Значение [sensitivity] должно быть больше 1. Ниже приведены несколько диаграмм для настройки.

> [!NOTE]
> Этим диаграммы не создаются с помощью примера приложения. Они создаются с помощью отдельного средства, используемого с примером приложения.

![Настройка: sensitivity = 1.0](../media/sensitivity_1.png)
![Настройка: sensitivity = 1.5](../media/sensitivity_1.5.png)
![Настройка: sensitivity = 2](../media/sensitivity_2.png)
![Настройка: sensitivity = 3.5](../media/sensitivity_3.5.png)