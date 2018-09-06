---
title: Добавление маркера с помощью службы "Карты Azure" | Документация Майкрософт
description: Как добавить маркер на карту в JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 0dafb09e1704e8e446b034975f0c25a740050599
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382590"
---
# <a name="add-pins-to-the-map"></a>Добавление маркеров на карту

В этой статье объясняется, как добавить маркер на карту.  

## <a name="understand-the-code"></a>Изучение кода

<iframe height='500' scrolling='no' title='Добавление маркера на карту' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Добавление маркера на карту</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. Инструкции см. в статье о [создании карты](./map-create.md).

Во втором блоке кода создается маркер, который добавляется на карту. Маркер — это класс [Feature](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.feature?view=azure-iot-typescript-latest) класса [Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest) со свойством компонента [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinproperties?view=azure-iot-typescript-latest). Чтобы создать маркер и определить его свойства, используйте `new atlas.data.Feature(new atlas.data.Point())`. Слой маркеров представляет собой массив маркеров. С помощью функции [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins) класса карты добавьте на карту слой маркеров и определите его свойства. Просмотрите свойства слоя маркеров в [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/models.pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 
* класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).
    
Дополнительные примеры кода для добавления в карты см. в следующих статьях: 
* [Добавление всплывающего окна](./map-add-popup.md)
* [Добавление фигуры](./map-add-shape.md)

