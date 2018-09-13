---
title: Функции стиля карт в службе Azure Maps | Документация Майкрософт
description: Сведения о функциях стиля в службе Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 08/31/2018
ms.topic: how-to-guides
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: b95e3c325160d1c0b775320e25fdd40514b02b9f
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666877"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Выбор стиля карты в службе Azure Maps
В службе Azure Maps доступно четыре разных стиля карт. Дополнительные сведения см. в статье [Стили карт, поддерживаемые в службе Azure Maps](./supported-map-styles.md). В этой статье показано, как с помощью связанных со стилем функций установить стиль при загрузке карты, установить новый стиль и использовать элемент выбора стиля.

## <a name="setting-style-on-map-load"></a>Установка стиля при загрузке карты

<iframe height='500' scrolling='no' title='Установка стиля при загрузке карты' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Установка стиля при загрузке карты</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

Код, приведенный выше, создает объект карты со стилем Grayscale (оттенки серого). См. инструкции по [созданию карты](./map-create.md).

## <a name="updating-the-style"></a>Обновление стиля

<iframe height='500' scrolling='no' title='Обновление стиля' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Обновление стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

Первый блок приведенного выше кода создает объект карты без предварительно заданного стиля. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода использует метод карты [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle), чтобы установить стиль карты в качестве вспомогательного.

## <a name="adding-the-style-picker"></a>Добавление элемента выбора стиля

<iframe height='500' scrolling='no' title='Добавление элемента выбора стиля' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>См. фрагмент кода <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Добавление элемента выбора стиля</a> в службе Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) на сайте <a href='https://codepen.io'>CodePen</a>.
</iframe>

Первый блок приведенного выше кода создает объект карты без предварительно заданного стиля. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода создает селектор стиля с помощью атласа [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol).

Элемент выбора стиля позволяет выбрать стиль для карты. Третий блок кода добавляет элемент выбора стиля на карту с помощью метода карты [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 
* класс [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest);
    * метод [setStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#setstyle);
    * метод [addControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addcontrol).

* [Атлас](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest):
    * [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol?view=azure-iot-typescript-latest#stylecontrol).
    
Дополнительные примеры кода для добавления в карты см. в следующих статьях:
* [Добавление элементов управления картой в службе Azure Maps](./map-add-controls.md)
* [Добавление маркера](./map-add-pin.md)
