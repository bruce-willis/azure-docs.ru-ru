---
title: Создание доступного приложения с помощью Azure Maps | Документация Майкрософт
description: Сведения о создании доступного приложения с помощью Azure Maps
services: azure-maps
keywords: ''
author: chgennar
ms.author: chgennar
ms.date: 05/18/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.openlocfilehash: 3fe0ba47e2e3529352ca8386dc7531a96a2689af
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746218"
---
# <a name="building-an-accessible-application"></a>Создание доступного приложения

В этой статье описывается, как создать приложение карты, которое может использовать устройство чтения с экрана.

## <a name="understand-the-code"></a>Изучение кода

<iframe height='500' scrolling='no' title='Создание доступного приложения' src='//codepen.io/azuremaps/embed/ZoVyZQ/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода <a href='https://codepen.io/azuremaps/pen/ZoVyZQ/'>Создание доступного приложения</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

Созданная карта имеет встроенные функции специальных возможностей. Пользователь может перемещаться по карте с помощью клавиатуры, и если запущено устройство чтения с экрана, карта будет уведомлять пользователя об изменениях в ее состоянии. Например, пользователь будет получать уведомления о новом значении широты, долготы, масштабе и местоположении на карте при ее сдвиге или увеличении масштаба. Дополнительные сведения, размещаемые на базовой карте, должны содержать соответствующую текстовую информацию для пользователей устройства чтения с экрана. Как вариант, для этого можно использовать класс [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest). В примере поиска выше всплывающее окно с текстовой информацией добавляется на карту для каждой размещенной на ней метки. При использовании метода attach для добавления [всплывающего окна](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest) устройство чтения с экрана может распознавать это окно без его визуального отображения на карте.

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классе Popup и методах работы со всплывающим окном, которые используются в этой статье:

* [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
    * [attach](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#attach)
    * [remove](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#remove)
    * [open](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#open)
    * [close](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest#close)

Чтобы узнать о других сценариях работы с картами, перейдите на эту [страницу с примерами кода](http://aka.ms/AzureMapsSamples).
