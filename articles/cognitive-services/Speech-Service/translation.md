---
title: Пример для перевода речи | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: В этой статье приведен пример для перевода речи.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1151628ed695e74e2196c20b08e33fa5eaf33282
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028393"
---
# <a name="sample-for-translation"></a>Пример для перевода речи

> [!NOTE]
> Дополнительные сведения о скачивании этого и других примеров для SDK распознавания речи вы найдете [здесь](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Для всех приведенных ниже примеров нужны следующие объявления верхнего уровня:
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>Перевод речи с использованием микрофона

В фрагменте кода ниже показан перевод поступающей речи с английского на немецкий язык и вывод переведенного текста в формате аудио. Для этого используется микрофон.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>Перевод речи с использованием входного файла

В фрагменте кода ниже показан перевод поступающей речи с английского на немецкий язык и французский.
Для этого используется входной файл.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>Исходный код примера

Последние версии примеров и более сложные примеры можно найти в соответствующем [репозитории GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Дополнительная информация

- [Распознавание речи](./speech-to-text-sample.md)

- [Распознавание намерений говорящего](./intent.md)
