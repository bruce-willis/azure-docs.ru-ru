---
title: Отображение маршрутов с помощью службы "Карты Azure" | Документация Майкрософт
description: В этой статье объясняется, как отобразить маршрут между двумя расположениями на карте в JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: codepen
ms.openlocfilehash: 52462c1c5a2a1a9698a2b51708e63b1bb1664f6e
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42745542"
---
# <a name="show-directions-from-a-to-b"></a>Отображение направлений от точки А до точки Б 

В этой статье объясняется, как выполнить запрос маршрута и отобразить маршрут на карте. 

## <a name="understand-the-code"></a>Изучение кода

<iframe height='500' scrolling='no' title='Отображение направлений от точки А до точки Б на карте' src='//codepen.io/azuremaps/embed/zRyNmP/?height=469&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/zRyNmP/'>Отображение направлений от точки А до точки Б на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создаются и добавляются на карту маркеры, представляющие начальную и конечную точку маршрута. См. инструкции по [добавлению маркеров на карту](map-add-pin.md).

В третьем блоке кода используется функция [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds) класса Map, чтобы задать координаты ограничивающего прямоугольника карты на основе начальной и конечной точек маршрута.

В четвертом блоке кода [XMLHttpRequest](https://xhr.spec.whatwg.org/) отправляется в [API маршрутов службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).

В последнем блоке кода анализируется входящий ответ. При получении ответа собираются сведения о широте и долготе для каждой точки маршрута. Затем путем соединения каждой точки маршрута со следующей точкой создается массив линий. Все эти линии добавляются на карту для отображения маршрута. См. инструкции по [добавлению линий на карту](./map-add-shape.md#addALine).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 

* класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setcamerabounds);
    * [addLinestrings](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addlinestrings);
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).

Дополнительные примеры кода для добавления в карты см. в следующих статьях: 
* [Отображение данных дорожного движения на карте](./map-show-traffic.md)
* [Взаимодействие с картой — события мыши](./map-events.md)
