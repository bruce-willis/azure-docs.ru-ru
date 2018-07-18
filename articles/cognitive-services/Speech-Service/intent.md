---
title: Пример для распознавания намерений | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Ниже приведен пример для распознавания намерений.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045016"
---
# <a name="sample-for-intent-recognition"></a>Пример для распознавания намерений

> [!NOTE]
> Дополнительные сведения о скачивании этого и других примеров для SDK распознавания речи вы найдете [здесь](samples.md).

> [!NOTE]
> Но сначала получите ключ подписки. В отличие от других служб, поддерживаемых пакетом SDK Cognitive Service для распознавания речи, служба распознавания намерений требует наличия конкретного ключа подписки. [Здесь](https://www.luis.ai) вы найдете дополнительные сведения о технологии распознавания намерений, а также сведения о получении ключа подписки. В соответствующих местах этих примеров укажите свой ключ подписки, регион службы и идентификатор AppId для целевой модели.

> [!NOTE]
> Для всех приведенных ниже примеров нужны следующие объявления верхнего уровня:
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>Распознавание намерений с помощью микрофона

В фрагменте кода ниже показано распознавание намерений на языке по умолчанию (`en-US`) по данным, поступающим от микрофона.

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Распознавание намерений с помощью микрофона на указанном языке

Представленный ниже фрагмент кода демонстрирует распознавание намерений на выбранном языке (в нашем примере это немецкий) по данным, поступающим от микрофона (`de-de`).

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>Распознавание намерений из файла

В следующем фрагменте кода распознается намерения на языке по умолчанию (`en-US`) по звуковому файлу. Поддерживается одноканальный (моно) формат WAV или PCM с частотой дискретизации 16 кГц.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>Распознавание намерений с помощью событий

В этом фрагменте кода показано непрерывное распознавание намерений. Этот код позволяет получить доступ к дополнительной информации, например к промежуточным результатам. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Исходный код примера

Актуальный набор примеров доступен в [репозитории GitHub с примерами для пакета SDK для распознавания текста в Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Дополнительная информация

- [Распознавание речи](./speech-to-text-sample.md)

- [Перевод](./translation.md)
