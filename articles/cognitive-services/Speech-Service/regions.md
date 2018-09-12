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
ms.openlocfilehash: 1cb00035dc8f1cdeabd1beb22ca69f47bf4bd89e
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379576"
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

Регион| Значение параметра региона в пакете SDK для службы "Речь"| Microsoft Azure
-|-
Запад США| `westus`| https://westus.cris.ai
Западная часть США 2| `westus2`| https://westus2.cris.ai
Восточная часть США| `eastus`| https://eastus.cris.ai
Восток США 2| `eastus2`| https://eastus2.cris.ai
Восточная Азия| `eastasia`| https://eastasia.cris.ai
Юго-Восточная Азия| `southeastasia`| https://southeastasia.cris.ai
Северная Европа| `northeurope`| https://northeurope.cris.ai
Западная Европа|  `westeurope`| https://westeurope.cris.ai

### <a name="regions-for-intent-recognition"></a>Регионы для распознавания намерений

Доступные регионы для **распознавания намерений** с помощью пакета SDK для службы "Речь" перечислены на [странице регионов службы "Распознавание речи"](/azure/cognitive-services/luis/luis-reference-regions).
Для каждого указанного региона публикации в пакете SDK для службы "Речь" определен соответствующий параметр, представляющий собой первую часть доменного имени конечной точки.
Например, используйте `westus`, чтобы указать регион публикации "Западная часть США".
