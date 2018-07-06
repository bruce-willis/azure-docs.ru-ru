---
title: Регионы службы "Речь" | Документация Майкрософт
description: Справочник по регионам и конечным точкам службы "Речь".
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/27/2018
ms.author: mahilleb
ms.openlocfilehash: a201cc043f673e2285ea48950804d97b96f881ed
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054942"
---
# <a name="regions-and-endpoints-of-the-speech-service"></a>Регионы и конечные точки службы "Речь"

> [!NOTE]
> Имена регионов в [пакете SDK для распознавания речи](speech-sdk.md) совпадают с первой частью доменных имен конечных точек, как показано ниже.
> Например, используйте `westus`, чтобы указать регион "Западная часть США" в пакете SDK для распознавания речи.

## <a name="speech-to-text"></a>Преобразование речи в текст

[!include[](includes/endpoints-speech-to-text.md)]

## <a name="text-to-speech"></a>Преобразование текста в речь

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="authentication"></a>Authentication

[!include[](includes/endpoints-token-service.md)]

Сведения о том, как получить и обновить маркеры авторизации см. [здесь](rest-apis.md#authentication).

## <a name="language-understanding-speech-sdk-only"></a>Language Understanding (только пакет SDK для распознавания речи)

Список регионов, в которых поддерживается служба Language Understanding, приведен [здесь](/azure/cognitive-services/luis/luis-reference-regions).
В пакете SDK для распознавания речи укажите эти регионы в соответствии с первой частью доменного имени конечной точки (например, `westus`).
