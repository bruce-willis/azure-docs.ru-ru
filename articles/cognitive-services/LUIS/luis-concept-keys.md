---
title: Сведения о ключах LUIS
titleSuffix: Azure Cognitive Services
description: 'В LUIS используются два ключа: ключ разработки и ключ конечной точки. Ключ разработки формируется автоматически при создании учетной записи LUIS. Когда вы будете готовы опубликовать приложение LUIS, потребуется создать ключ конечной точки, назначить его приложению LUIS и использовать его в запросе к конечной точке.'
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: a4bd20f9f8a6a8317e161b1d84e948391cd08140
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034932"
---
# <a name="keys-in-luis"></a>Ключи в LUIS
В LUIS используются два ключа: [разработки](#programmatic-key) и [конечных точек](#endpoint-key). Ключ разработки формируется автоматически при создании учетной записи LUIS. Когда вы будете готовы опубликовать свое приложение LUIS, потребуется [создать ключ конечной точки](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [назначить его](luis-how-to-manage-keys.md#assign-endpoint-key) приложению LUIS и [использовать его в запросе к конечной точке](#use-endpoint-key-in-query). 

|Ключ|Назначение|
|--|--|
|[Ключ разработки](#programmatic-key)|Разработка, публикация, управление участниками совместной работы, управление версиями|
|[Ключ конечной точки](#endpoint-key)| Выполнение запроса|

Важно разрабатывать приложение LUIS в [том же регионе](luis-reference-regions.md#publishing-regions), в котором вы хотите опубликовать его и выполнять запросы.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Ключ разработки

Ключ разработки, который также называется начальным ключом, создается автоматически при создании учетной записи LUIS и является бесплатным. У вас есть по одному ключу разработки для всех приложений LUIS в каждом [регионе](luis-reference-regions.md) разработки. Ключ разработки предоставляется для разработки приложения LUIS или для проверки отправки запросов к конечным точкам. 

Чтобы найти ключ разработки, войдите в [LUIS](luis-reference-regions.md#luis-website) и щелкните имя учетной записи на панели навигации справа вверху, чтобы открыть **Параметры учетной записи**.

![Ключ разработки](./media/luis-concept-keys/programatic-key.png)

Если вы хотите отправлять запросы к **рабочей конечной точке**, создайте [подписку LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) в Azure. 

> [!CAUTION]
> Для удобства во многих примерах используется ключ разработки, так как в [квоту](luis-boundaries.md#key-limits) этого ключа входят несколько вызовов к конечным точкам.  

## <a name="endpoint-key"></a>Ключ конечной точки
 Если вы хотите отправлять запросы к **рабочей конечной точке**, создайте [ключ LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) на портале Azure. Запомните имя, которое использовалось при создании ключа. Оно потребуется при добавлении ключа в приложение.

Создав подписку LUIS, [назначьте ключ](luis-how-to-manage-keys.md#assign-endpoint-key) приложению. 

Ключ конечной точки предоставляет квоту обращений к конечной точке, которая зависит от плана использования, указанного при создании ключа. Сведения о ценах см. в разделе [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h).

Ключ конечной точки можно использовать для всех приложений LUIS или для конкретных приложений LUIS. 

Не используйте ключ конечной точки для разработки приложений LUIS. 

## <a name="use-endpoint-key-in-query"></a>Использование ключа конечной точки в запросе
Конечная точка LUIS принимает два стиля запроса. Ключ конечной точки используется в каждом из них, но в различных местах:

|Команда|Пример URL-адреса и расположение ключа|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>значение строки запроса для `subscription-key`<br><br>Чтобы использовать квоту конечной точки LUIS, измените значение запроса конечной точки для `subscription-key` с ключа разработки (начального ключа) на новый ключ конечной точки. Если создать и назначать ключ, но не изменять значение запроса конечной точки для ключа подписки, вы не будете использовать квоту ключа конечной точки.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> значение заголовка для `Ocp-Apim-Subscription-Key`<br><br>Чтобы использовать квоту конечной точки LUIS, измените значение запроса конечной точки для `Ocp-Apim-Subscription-Key` с ключа разработки (начального ключа) на новый ключ конечной точки. Если создать и назначать ключ, но не изменять значение запроса конечной точки для `Ocp-Apim-Subscription-Key`, вы не будете использовать квоту ключа конечной точки.|

Идентификатор приложения, используемый в предыдущих URL-адресах, `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, является общедоступным приложением Интернета вещей, используемым для [интерактивной демонстрации](https://azure.microsoft.com/en-us/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Использование API Ocp-Apim-Subscription-Key
API-интерфейсы LUIS используют заголовок `Ocp-Apim-Subscription-Key`. Имя заголовка не изменяется в зависимости от ключа и набора API, которые вы используете. Для API разработки установите ключ разработки в качестве заголовка. Если вы используете конечную точку, установите ключ конечной точки в качестве заголовка. 

Вы не можете использовать ключ конечной точки для API разработки. При попытке выполнить это действие, появится сообщение об ошибке 401 ("Доступ запрещен, указан недопустимый ключ конечной точки"). 

## <a name="key-limits"></a>Ограничения ключа
См. разделы [Ограничения ключа](luis-boundaries.md#key-limits) и [Регионы Azure](luis-reference-regions.md). Ключ разработки бесплатен и используется для разработки. Для ключа конечной точки LUIS есть уровень "Бесплатный", но этот ключ должен создаваться вами, и его необходимо связать с приложением LUIS на странице **Публикация**. Он не может использоваться для разработки, а только для запросов к конечным точкам.

Регионы публикации отличаются от регионов разработки. Убедитесь, что вы разрабатываете приложение в регионе разработки, соответствующем желаемому региону публикации.

## <a name="key-limit-errors"></a>Ошибки ограничений ключей
При превышении заданного значения квоты в секунду вы получите ошибку HTTP 429. При превышении заданного значения квоты в месяц вы получите ошибку HTTP 403. Чтобы исправить эти ошибки, получите ключ [конечной точки](#endpoint-key) LUIS и [назначьте](luis-how-to-manage-keys.md#assign-endpoint-key) его приложению на странице **Публикация** веб-сайта [LUIS](luis-reference-regions.md#luis-website).

## <a name="automating-assignment-of-the-endpoint-key"></a>Автоматическое назначение ключа конечной точки

Чтобы назначить ключ конечной точки приложению LUIS, необходимо использовать веб-сайт LUIS для правильного указания [регионов](luis-reference-regions.md) разработки и публикации. Сделать это автоматически **нельзя**, какой механизм вы бы ни использовали — скрипт Azure Resource Manager, Azure CLI, программный пакет SDK или API-интерфейсы.

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь с [понятиями](luis-how-to-manage-keys.md#assign-endpoint-key), связанными с ключами разработки и конечных точек.