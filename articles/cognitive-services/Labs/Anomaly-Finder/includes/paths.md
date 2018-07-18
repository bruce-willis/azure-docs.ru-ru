---
title: включение файла
description: включение файла
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a806cac410eb57e59dacb42da9be954b2f962956
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381312"
---
<a name="paths"></a>
## <a name="paths"></a>Пути

<a name="anomalydetection-post"></a>
### <a name="detect-anomaly-points-for-the-time-series-data-points-requested"></a>Обнаружение точек аномалий среди запрошенных точек данных временных рядов
```
POST /anomalydetection
```


#### <a name="parameters"></a>Параметры

|type|ИМЯ|ОПИСАНИЕ|Схема|
|---|---|---|---|
|**Текст**|**body**  <br>*обязательный параметр*|Точки данных временного ряда и период, если требуется.|[request](#request)|


#### <a name="responses"></a>Ответы

|Код HTTP|ОПИСАНИЕ|Схема|
|---|---|---|
|**200**|Успешная операция.|Массив объектов < [response](#response) >|
|**400**|Не удается проанализировать запрос JSON.|Нет содержимого|
|**403**|Сертификат, который вы указали, не принимается сервером.|Нет содержимого|
|**405**|Недопустимый метод.|Нет содержимого|
|**500**|Внутренняя ошибка сервера.|Нет содержимого|


#### <a name="consumes"></a>Использует

* `application/json`


#### <a name="produces"></a>Выдает

* `application/json`


#### <a name="tags"></a>Теги

* anomalydetection



