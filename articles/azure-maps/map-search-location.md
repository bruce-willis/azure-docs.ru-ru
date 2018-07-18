---
title: Отображение результатов поиска с помощью службы "Карты Azure" | Документация Майкрософт
description: Как выполнять запрос на поиск с помощью службы "Карты Azure" и выводить результаты на карту в Javascript
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b77737b16b23ed00c8f12f84e6a8558a665a7d15
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34599887"
---
# <a name="show-search-results-on-the-map"></a>Отображение результатов поиска на карте

В этой статье объясняется, как выполнить запрос на поиск и вывести результаты поиска на карту. 

## <a name="understand-the-code"></a>Изучение кода

<iframe height='500' scrolling='no' title='Отображение результатов поиска на карте' src='//codepen.io/azuremaps/embed/KQbaeM/?height=519&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Просмотрите фрагмент кода для <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>отображения результатов поиска на карте</a> службы "Карты Azure" (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) в <a href='https://codepen.io'>CodePen</a>.
</iframe>

В первом блоке приведенного выше кода создается объект карты. См. инструкции по [созданию карты](./map-create.md).

Во втором блоке кода создается и добавляется на карту слой для закрепления результатов поиска. См. инструкции по [добавлению закреплений на карту](./map-add-pin.md).

В третьем блоке кода [XMLHttpRequest](https://xhr.spec.whatwg.org/) отправляется в [API поиска нечетких соответствий службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy).

В последнем блоке кода анализируется входящий ответ. При получении ответа собираются сведения о широте и долготе для каждого полученного расположения. Этот код добавляет на карту точки расположения в виде закреплений и настраивает границы карты для отображения всех закреплений.


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о классах и методах, которые используются в этой статье: 

* [API поиска нечетких соответствий службы "Карты Azure"](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins).
    
Дополнительные примеры кода для добавления в карты см. в следующих статьях: 
* [Получение сведений на основе координат](./map-get-information-from-coordinate.md)
* [Отображение направлений от точки А до точки Б](./map-route.md)
