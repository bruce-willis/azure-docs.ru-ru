---
title: Распознавание речи с помощью REST API
description: Сведения об использовании преобразования речи в текст в службе речи
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 15db2d9b872c76d70fd531af07fb55c701e86494
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39331341"
---
# <a name="recognize-speech-by-using-the-rest-api"></a>Распознавание речи с помощью REST API

[!include[Selector](../../../includes/cognitive-services-speech-service-how-to-recognize-speech-selector.md)]

Распознавать короткие фрагменты речи можно с помощью запроса HTTP POST в REST API.

REST API является самым простым способом распознавания речи, если вы не используете язык, поддерживаемый пакетом SDK.
Вы отправляете запрос HTTP POST к конечной точке службы, передавая весь фрагмент речи в теле запроса.
Полученный ответ содержит распознанный текст.

> [!NOTE]
> При использовании интерфейса REST API ограничение длительности фрагмента речи составляет 15 секунд.
> Для распознавания более длительных фрагментов речи рекомендуем использовать [пакет SDK службы "Речь"](how-to-recognize-speech-csharp.md).

Дополнительные сведения об REST API **преобразования текста в речь** см. в разделе [REST APIs](rest-apis.md#speech-to-text) (Речевые службы REST API). Чтобы увидеть это в действии, загрузите [примеры REST API](https://github.com/Azure-Samples/SpeechToText-REST) из GitHub.

## <a name="next-steps"></a>Дополнительная информация

- [См. обзор REST API](rest-apis.md)
