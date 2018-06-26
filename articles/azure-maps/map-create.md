---
title: Создание карты с помощью службы "Карты Azure" | Документация Майкрософт
description: Создание карты на JavaScript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: ec85854e5d9b7ee0d5e2c54881a417ba6cbb366e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599819"
---
# <a name="create-a-map"></a>Создание карты

В этой статье показано, как создать карту.  

## <a name="understand-the-code"></a>Изучение кода

Карту можно создать двумя способами. Вы можете навести камеру на карту, указав центральную точку и масштаб изображения, или определить границы камеры на карте, указав ее крайние точки на юго-западе и на северо-востоке.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Настройка камеры

<iframe height='310' scrolling='no' title='Создание карты с помощью CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Создание карты с помощью CameraOptions</a> от Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В представленном выше примере кода создается [объект map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) с помощью `new atlas.Map()`. Свойства карты, такие как положение центральной точки и уровень масштабирования, входят в [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest). CameraOptions можно определить через конструктор карты или с помощью функции [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) класса Map.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Настройка границ камеры

<iframe height='310' scrolling='no' title='Создание карты с помощью CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Создание карты через CameraBoundsOptions</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В представленном выше примере кода создается [объект map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) с помощью конструктора `new atlas.Map()`. Свойства карты, например расположение ограничивающего прямоугольника, входят в [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest). CameraBoundsOptions можно определить с помощью функции [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) класса Map.

## <a name="try-out-the-code"></a>Тестирование кода 

Давайте рассмотрим подробнее приведенный выше пример. Вы можете редактировать код JavaScript на вкладке JS в левой части экрана и сразу видеть изменения в представлении карты на вкладке "Результат" справа. Кроме того, вы можете изменить код в CodePen с помощью кнопки "Редактировать в CodePen". 

<a id="relatedReference"></a>

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)
    
Дополнительные примеры кода для добавления в карты см. в следующих статьях: 
* [Добавление маркера](./map-add-pin.md)
* [Добавление всплывающего окна](map-add-popup.md)
    

