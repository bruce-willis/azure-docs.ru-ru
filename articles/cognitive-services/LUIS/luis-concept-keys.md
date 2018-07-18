---
title: Сведения о ключах LUIS — Azure | Документы Майкрософт
description: Используйте ключи Language Understanding (LUIS) для разработки приложений и отправки запросов к конечным точкам.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: v-geberr
ms.openlocfilehash: 70bca3b181e02f42da50e827154193936544131a
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36263824"
---
# <a name="keys-in-luis"></a>Ключи в LUIS
В LUIS используются два ключа: [разработки](#programmatic-key) и [конечных точек](#endpoint-key). Ключ разработки формируется автоматически при создании учетной записи LUIS. Когда вы будете готовы опубликовать свое приложение LUIS, потребуется [создать ключ конечной точки](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [назначить его](Manage-keys.md#assign-endpoint-key) приложению LUIS и [использовать его в запросе к конечной точке](#use-endpoint-key-in-query). 

|Ключ|Назначение|
|--|--|
|[Ключ разработки](#programmatic-key)|Разработка, публикация, управление участниками совместной работы, управление версиями|
|[Ключ конечной точки](#endpoint-key)| Выполнение запроса|

Важно разрабатывать приложение LUIS в [том же регионе](luis-reference-regions.md#publishing-regions), в котором вы хотите опубликовать его и выполнять запросы.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Ключ разработки

Ключ разработки, который также называется начальным ключом, создается автоматически при создании учетной записи LUIS и является бесплатным. У вас есть по одному ключу разработки для всех приложений LUIS в каждом [регионе](luis-reference-regions.md) разработки. Ключ разработки предоставляется для разработки приложения LUIS или для проверки отправки запросов к конечным точкам. 

Чтобы найти ключ разработки, войдите в [LUIS][LUIS] и щелкните имя учетной записи на панели навигации справа вверху, чтобы открыть **Параметры учетной записи**.

![Ключ разработки](./media/luis-concept-keys/programatic-key.png)

Если вы хотите отправлять запросы к **рабочей конечной точке**, создайте [подписку LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) Azure. 

> [!CAUTION]
> Для удобства во многих примерах используется ключ разработки, так как в [квоту](luis-boundaries.md#key-limits) этого ключа входят несколько вызовов к конечным точкам.  

## <a name="endpoint-key"></a>Ключ конечной точки
 Если вы хотите отправлять запросы к **рабочей конечной точке**, создайте [ключ LUIS](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) на портале Azure. Запомните имя, использованное при создании ключа. Оно потребуется при добавлении ключа в приложение.

Создав подписку LUIS, [добавьте ключ](Manage-keys.md#assign-endpoint-key) в приложение на странице **Публикация**. 

Ключ конечной точки предоставляет квоту обращений к конечной точке, которая зависит от плана использования, указанного при создании ключа. Сведения о ценах см. в разделе [Цены на Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h).

Ключ конечной точки можно использовать для всех приложений LUIS или для конкретных приложений LUIS. 

Не используйте ключ конечной точки для разработки приложений LUIS. 

## <a name="use-endpoint-key-in-query"></a>Использование ключа конечной точки в запросе
Конечная точка LUIS принимает два стиля запроса. Ключ конечной точки используется в каждом из них, но в различных местах:

|Команда|Пример URL-адреса и расположение ключа|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn on the lights<br><br>значение строки запроса для `subscription-key`<br><br>Чтобы использовать квоту конечной точки LUIS, измените значение запроса конечной точки для `subscription-key` с ключа разработки (начального ключа) на новый ключ конечной точки. Если создать и назначать ключ, но не изменять значение запроса конечной точки для ключа подписки, вы не будете использовать квоту ключа конечной точки.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a<br><br> значение заголовка для `Ocp-Apim-Subscription-Key`<br><br>Чтобы использовать квоту конечной точки LUIS, измените значение запроса конечной точки для `Ocp-Apim-Subscription-Key` с ключа разработки (начального ключа) на новый ключ конечной точки. Если создать и назначать ключ, но не изменять значение запроса конечной точки для `Ocp-Apim-Subscription-Key`, вы не будете использовать квоту ключа конечной точки.|

## <a name="api-usage-of-ocp-apim-subscription-key"></a>Использование API Ocp-Apim-Subscription-Key
API-интерфейсы LUIS используют заголовок `Ocp-Apim-Subscription-Key`. Имя заголовка не изменяется в зависимости от ключа и набора API, которые вы используете. Для API разработки установите ключ разработки в качестве заголовка. Если вы используете конечную точку, установите ключ конечной точки в качестве заголовка. 

Вы не можете использовать ключ конечной точки для API разработки. Если вы попробуете это сделать, то получите сообщение об ошибке 401 ("Доступ запрещен, указан недопустимый ключ подписки"). 

## <a name="key-limits"></a>Ограничения ключа
См. разделы [Ограничения ключа](luis-boundaries.md#key-limits) и [Регионы Azure](luis-reference-regions.md). Ключ разработки бесплатен и используется для разработки. Для ключа подписки LUIS есть уровень "Бесплатный", но этот ключ должен создаваться вами, и его необходимо связать с приложением LUIS на странице **Публикация**. Он не может использоваться для разработки, а только для запросов к конечным точкам.

Регионы публикации отличаются от регионов разработки. Убедитесь, что вы разрабатываете приложение в регионе разработки, соответствующем желаемому региону публикации.

## <a name="key-limit-errors"></a>Ошибки ограничений ключей
При превышении заданного значения квоты в секунду вы получите ошибку HTTP 429. При превышении заданного значения квоты в месяц вы получите ошибку HTTP 403. Чтобы исправить эти ошибки, получите ключ [конечной точки](#endpoint-key) LUIS и [назначьте](Manage-keys.md#assign-endpoint-key) его приложению на странице **Публикация** веб-сайта [LUIS][LUIS].

## <a name="next-steps"></a>Дополнительная информация

* Ознакомьтесь с [понятиями](Manage-Keys.md#assign-endpoint-key), связанными с ключами разработки и конечных точек.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
