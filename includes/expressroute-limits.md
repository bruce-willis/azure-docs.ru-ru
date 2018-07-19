---
title: включение файла
description: включение файла
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6e0d2b881e00acc6b1fccd3196187309fc36cba2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755981"
---
#### <a name="expressroute-limits"></a>Ограничения ExpressRoute
Ниже приведены ограничения, которые применяются к ресурсам ExpressRoute для каждой подписки.

| Ресурс | Ограничение по умолчанию |
| --- | --- |
| Каналы ExpressRoute для каждой подписки |10 |
| Каналы ExpressRoute на регион для каждой подписки для ARM |10 |
| Максимальное количество маршрутов для частного пиринга Azure со стандартом ExpressRoute |4000 |
| Максимальное количество маршрутов для частного пиринга Azure с надстройкой ExpressRoute Premium |10 000 |
| Максимальное количество маршрутов для общедоступного пиринга Azure со стандартом ExpressRoute |200 |
| Максимальное количество маршрутов для общедоступного пиринга Azure с надстройкой ExpressRoute Premium |200 |
| Максимальное количество маршрутов для общедоступного пиринга Майкрософт в Azure со стандартом ExpressRoute |200 |
| Максимальное количество маршрутов для общедоступного пиринга Майкрософт в Azure с надстройкой ExpressRoute Premium |200 |
| Максимальное число каналов ExpressRoute, связанных с одной и той же виртуальной сетью в разных расположениях пиринга |4. |
| Количество связей виртуальных сетей на канал ExpressRoute |см. таблицу ниже |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Количество виртуальных сетей на канал ExpressRoute
| **Размер канала** | **Число связей виртуальных сетей для стандартной установки** | **Число связей виртуальных сетей с надстройкой Premium** |
| --- | --- | --- |
| 50 Мбит/с |10 |20 |
| 100 Мбит/с |10 |25 |
| 200 Мбит/с |10 |25 |
| 500 Мбит/с |10 |40 |
| 1 Гбит/с |10 |50 |
| 2 Гбит/с |10 |60 |
| 5 Гбит/с |10 |75 |
| 10 Гбит/с |10 |100 |

