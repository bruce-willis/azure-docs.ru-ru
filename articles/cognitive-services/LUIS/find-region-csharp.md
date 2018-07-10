---
title: Поиск региона LUIS с помощью C# в границах Language Understanding (LUIS) | Документы Майкрософт
description: Программный поиск региона публикации с помощью ключа подписки и идентификатора приложения для LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/31/2018
ms.author: v-geberr
ms.openlocfilehash: c8d2024567255083aec470adfebff0d1706fd472
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2018
ms.locfileid: "35383191"
---
# <a name="region-can-be-determined-from-api-call"></a>Регион можно определить из вызова API 
При наличии идентификатора приложения LUIS и идентификатора подписки LUIS можно определить, какой регион будет использоваться для запросов конечной точки.

> [!NOTE] 
> Полное решение C# доступно в разделе [**LUIS-Samples** репозитория Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/find-region/csharp/).

## <a name="luis-endpoint-query-strategy"></a>Стратегия запросов конечной точки LUIS
Для каждого запроса конечной точки LUIS требуется:

* ключ подписки;
* идентификатор приложения;
* регион.

Если запрос конечной точки LUIS использует правильный ключ подписки и идентификатор приложения, но неправильный регион, будет получен код ответа 401. Запрос 401 не учитывается в квоте подписки. Включите этот запрос в стратегию для опроса всех регионов при поиске нужного региона. Правильным регионом будет только тот запрос, который возвращает код состояния 2xx. 

|Код ответа|Параметры|
|--|--|
|"2xx"|правильный ключ подписки<br>правильный идентификатор приложения<br>правильный регион размещения|
|401|правильный ключ подписки<br>правильный идентификатор приложения<br>_неправильный_ регион размещения|

## <a name="c-class-code-to-find-region"></a>Код класса C# для поиска региона
Консольное приложение принимает идентификатор приложения и ключ подписки LUIS и возвращает все регионы, связанные с ними. В настоящее время ключи подписки создаются по регионам, поэтому запрос должен возвращать только один регион.

Включите следующие зависимости библиотеки .NET:

[!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=1-6 "Add the dependencies")]

Добавьте этот пользовательский класс LUIS для нахождения региона. Замените значения переменных `luisAppId` и `luisSubscriptionKey` собственными. Все регионы, для которых возвращается ответ 401, будут записаны в консоли отладки. 

[!code-csharp[Add the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=10-83 "Add the LUIS class")]

Ниже приведен пример вызова пользовательского класса LUIS в методе Main консольного приложения:

[!code-csharp[Call the LUIS class](~/samples-luis/documentation-samples/find-region/csharp/ConsoleAppLUISRegion/Program.cs?range=85-101 "Call the LUIS class")]

При запуске приложения консоль показывает регион для идентификатора приложения и ключа подписки.

![Снимок экрана: консольное приложение с регионом LUIS](./media/find-region-csharp/console.png)

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о [регионах](luis-reference-regions.md) LUIS.
