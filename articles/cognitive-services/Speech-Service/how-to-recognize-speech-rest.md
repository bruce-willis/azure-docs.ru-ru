---
title: Распознавание речи с помощью REST API
description: Узнайте, как использовать API преобразования речи в текст в службе речи Azure Cognitive Services.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: eafec2dd262098bc4b7e485293818b79debe3d27
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126852"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Распознавание речи с помощью REST API

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

Распознать короткие фрагменты речи можно с помощью запроса HTTP POST в REST API.

REST API является самым простым способом распознавания речи, если вы не используете язык, поддерживаемый пакетом [SDK](speech-sdk.md). Вы отправляете запрос HTTP POST к конечной точке службы и передаете весь фрагмент речи в тексте запроса. Вы получаете ответ с распознанным текстом.

> [!NOTE]
> При использовании интерфейса REST API ограничение длительности фрагмента речи составляет 15 секунд.
> Для распознавания более длительных фрагментов речи рекомендуем использовать [пакет SDK службы "Речь"](how-to-recognize-speech-csharp.md).

Дополнительные сведения о REST API **преобразования текста в речь** см. в статье о [REST API](rest-apis.md#speech-to-text). Чтобы увидеть API в действии, скачайте [примеры REST API](https://github.com/Azure-Samples/SpeechToText-REST) с GitHub.

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь со статьей [REST API службы распознавания речи](rest-apis.md).
