---
title: Регионы службы "Речь"
description: Справочник по регионам и конечным точкам службы "Речь".
services: cognitive-services
author: mahilleb-msft
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/28/2018
ms.author: mahilleb
ms.openlocfilehash: 082002b25b02e1e496221f4686d0e636630dd438
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324397"
---
# <a name="regions-of-the-speech-service"></a>Регионы и конечные точки службы "Речь"

Служба "Речь" доступна в нескольких регионах.
При создании подписки можно выбрать доступный регион в соответствии с требованиями.

При использовании подписки необходима учетная запись для выбранного региона.

## <a name="rest-api"></a>REST API

С помощью REST API выберите конечные точки в соответствующем регионе.
Дополнительные сведения приведены описании [интерфейсов REST API](rest-apis.md).

## <a name="speech-sdk"></a>Пакет SDK для распознавания речи

В [пакете SDK для службы "Речь"](speech-sdk.md) регионы указанное в виде строки (например, в качестве параметра [SpeechFactory.FromSubscription](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechfactory.fromsubscription) в пакете SDK службы "Речь" для C#).

### <a name="regions-for-speech-recognition-and-translation"></a>Регионы для распознавания и перевода речи

В следующей таблице перечислены доступные регионы для служб **распознавания речи** и **перевода**.

Регион| Значение параметра региона в пакете SDK для службы "Речь"
-|-
Запад США| `westus`
Восточная Азия| `eastasia`
Северная Европа| `northeurope`

### <a name="regions-for-intent-recognition"></a>Регионы для распознавания намерений

Доступные регионы для **распознавания намерений** с помощью пакета SDK для службы "Речь" перечислены на [странице регионов службы "Распознавание речи"](/azure/cognitive-services/luis/luis-reference-regions).
Для каждого указанного региона публикации в пакете SDK для службы "Речь" определен соответствующий параметр, представляющий собой первую часть доменного имени конечной точки.
Например, используйте `westus`, чтобы указать регион публикации "Западная часть США".
