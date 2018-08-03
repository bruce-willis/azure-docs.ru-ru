---
title: Поиск региона LUIS с помощью Node.js в границах Language Understanding (LUIS) | Документы Майкрософт
description: Программный поиск региона публикации с помощью ключа конечной точки и идентификатора приложения для LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/01/2018
ms.author: v-geberr
ms.openlocfilehash: 6d85e6007b3e85a1b55997541e721ad57c22dddf
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111751"
---
# <a name="region-can-be-determined-from-api-call"></a>Регион можно определить из вызова API 
При наличии идентификатора приложения LUIS и идентификатора подписки LUIS можно определить, какой регион будет использоваться для запросов конечной точки.

> [!NOTE] 
> Полное решение Node.js доступно в разделе [**LUIS-Samples** репозитория Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/nodejs/).

## <a name="luis-endpoint-query-strategy"></a>Стратегия запросов конечной точки LUIS
Для каждого запроса конечной точки LUIS требуется:

* ключ конечной точки;
* идентификатор приложения;
* регион.

Если запрос конечной точки LUIS использует правильный ключ конечной точки и идентификатор приложения, но неправильный регион, будет получен код ответа 401. Запрос 401 не учитывается в квоте подписки. Включите этот запрос в стратегию для опроса всех регионов при поиске нужного региона. Правильным регионом будет только тот запрос, который возвращает код состояния 2xx. 

|Код ответа|Параметры|
|--|--|
|"2xx"|правильный ключ конечной точки<br>правильный идентификатор приложения<br>правильный регион размещения|
|401|правильный ключ конечной точки<br>правильный идентификатор приложения<br>_неправильный_ регион размещения|

## <a name="nodejs-code-to-find-region"></a>Код Node.js для поиска региона
Консольное приложение принимает идентификатор приложения LUIS и ключ конечной точки и возвращает все регионы, связанные с ними. В настоящее время ключи конечной точки создаются по регионам, поэтому запрос должен возвращать только один регион.

Включите зависимости NPM:

[!code-javascript[Add the dependencies](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=5-6 "Add the dependencies")]

Добавьте константы. Замените значения переменных `subscriptionKey` и `appId` собственными.  

[!code-javascript[Add constants](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=8-25 "Add constants")]

Добавьте функцию `searchRegions` для поиска региона области. Все неверные регионы возвращают ответ 401, которой перехватывается и пропускается.

[!code-javascript[Find region](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=27-37 "Find region")]

Вызовите функцию `searchRegions`, чтобы вернуть один регион:

[!code-javascript[Call the function](~/samples-luis/documentation-samples/find-region/nodejs/index.js?range=39-43 "Call the function")]

При запуске приложения терминал показывает регион для идентификатора приложения.

![Снимок экрана: консольное приложение с регионом LUIS](./media/find-region-nodejs/console.png)


## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о [регионах](luis-reference-regions.md) LUIS.
