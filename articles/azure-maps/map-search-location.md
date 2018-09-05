---
title: Отображение результатов поиска с помощью службы "Карты Azure" | Документация Майкрософт
description: Как выполнять запрос на поиск с помощью службы "Карты Azure" и выводить результаты на карту в Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 540faa04573f7fa66b574f6743d42adfe855c571
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43095261"
---
# <a name="show-search-results-on-the-map"></a>Отображение результатов поиска на карте

В этой статье объясняется, как выполнить поиск расположения и вывести результаты поиска на карту. 

## <a name="understand-the-code"></a>Изучение кода

<iframe height='305' scrolling='no' title='Отображение результатов поиска на карте (модуль службы)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>отображения результатов поиска на карте (служебный модуль)</a> службы Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты и реализуется служба клиента. См. инструкции по [созданию карты](./map-create.md).

Второй блок кода использует поиск нечетких соответствий с помощью [API поиска нечетких соответствий службы Azure Maps](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) для точек интереса. API нечеткого поиска может обрабатывать любую комбинацию нечетких входных данных. Затем ответ службы поиска нечетких соответствий обрабатывается в формате GeoJSON, а на карту добавляются контакты для отображения точек интереса. 

Последний блок кода добавляет границы камеры для карты с использованием свойства [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 

* [API поиска нечетких соответствий службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins).
    
Дополнительные примеры кода для добавления в карты см. в следующих статьях: 
* [Получение сведений на основе координат](./map-get-information-from-coordinate.md)
* [Отображение направлений от точки А до точки Б](./map-route.md)
