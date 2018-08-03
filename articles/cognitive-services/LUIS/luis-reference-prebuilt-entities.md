---
title: Справочник по предварительно созданным сущностям LUIS в Azure | Документация Майкрософт
titleSuffix: Azure
description: В этой статье содержатся списки предварительно созданных сущностей, включенных в функцию распознавания речи (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/11/2018
ms.author: diberry
ms.openlocfilehash: 731ac279b4b0c162809d8e0638b9337924859b3d
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238835"
---
# <a name="entities-per-culture"></a>Сущности по языкам и региональным параметрам

Интеллектуальная служба распознавания речи (LUIS) предоставляет предварительно созданные сущности. При включении предварительно созданной сущности в приложение LUIS включает соответствующий прогноз сущности в ответ конечной точки. Все примеры фраз также помечаются с помощью сущности. Поведение предварительно созданных сущностей **нельзя** изменить. Если не указано иное, предварительно созданные сущности доступны во всех языковых стандартах (языках и региональных параметрах) приложений LUIS. В следующей таблице показаны предварительно созданные сущности, которые поддерживаются для каждого языка и региональных параметров.

Предварительно созданная сущность   |   Английский (США)<br>```En-us```   |   Французский (Франция)<br>```fr-FR```   |   Итальянский (Италия)<br>```it-IT```   |   Испанский (Испания)<br>```es-ES```   |   Китайский<br>```zh-CN```   |   Немецкий<br>```de-DE```   |   Португальский (Бразилия)<br>```pt-BR```   |   японский (Япония)<br>```ja-JP```   |   Корейский (Корея)<br>```ko-kr```   | французский (Канада)<br>```fr-CA```   |   Испанский (Мексика)<br>```es-MX```   |   нидерландский (Нидерланды)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Currency](luis-reference-prebuilt-currency.md):<br>доллар<br>дробная единица (например, пенни)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>дата<br>daterange<br>Twitter в режиме реального<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>том<br>площадь<br>вес<br>информация (например, бит/байт)<br>длина (например, метр)<br>скорость (например, миль в час)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Электронная почта](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   |   ✔   |   ✔   |   ✔   |   -   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |   ✔   |
[Число](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>Фаренгейта<br>Кельвина<br>Ранкина<br>Делиля<br>Цельсия   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[URL-адрес](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

См. примечания об [устаревших предварительно созданных сущностях](luis-reference-prebuilt-deprecated.md).

KeyPhrase недоступна во всех подкатегориях португальского языка и региональных параметров (Бразилия) — ```pt-BR```.

<!--
## Examples of prebuilt entities in en-us culture
The following table lists prebuilt entities with example data and the return values.

Prebuilt entity   |   Example utterance   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | See [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) | See [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) |
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.email```     |  ```patti.owens@microsoft.com```   |```{ "type": "builtin.email", "entity": "patti.owens@microsoft.com" }```|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.phonenumber```   |   ```my mobile is 00 44 161 1234567```   |```{ "type": "builtin.phonenumber", "entity": "00 44 161 1234567" }```|
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.url```     |   ```http://www.luis.ai is a great cognitive service```   |```{ "type": "builtin.url", "entity": "http://www.luis.ai" }```|
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Участие в создании предварительно созданных сущностей для языков и региональных параметров
Предварительно созданные сущности разрабатываются в проекте с открытым кодом Recognizers-Text. Приглашаем к [участию](https://github.com/Microsoft/Recognizers-Text) в проекте. Этот проект содержит примеры единиц валюты для различных языков и региональных параметров. 

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сущностях [number](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md) и [currency](luis-reference-prebuilt-currency.md). 