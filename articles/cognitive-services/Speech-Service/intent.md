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
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213211"
---
# <a name="sample-for-intent-recognition"></a>Пример для распознавания намерений

Но сначала получите ключ подписки. В отличие от других служб, поддерживаемых пакетом SDK Cognitive Service для распознавания речи, служба распознавания намерений требует наличия конкретного ключа подписки. [Здесь](https://www.luis.ai) вы найдете дополнительные сведения о технологии распознавания намерений, а также сведения о получении ключа подписки. В соответствующих местах этих примеров укажите свой ключ подписки на службу "Распознавание речи", [регион подписки](regions.md) и идентификатор AppId для модели намерений.

## <a name="top-level-declarations"></a>Объявления верхнего уровня

Для всех приведенных ниже примеров нужны следующие объявления верхнего уровня:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>Распознавание намерений с помощью микрофона

В фрагменте кода ниже показано распознавание намерений на языке по умолчанию (`en-US`) по данным, поступающим от микрофона.

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Распознавание намерений с помощью микрофона на указанном языке

Представленный ниже фрагмент кода демонстрирует распознавание намерений на выбранном языке (в нашем примере это немецкий) по данным, поступающим от микрофона (`de-de`).

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>Распознавание намерений из файла с помощью событий

В этом фрагменте кода показано непрерывное распознавание намерений на языке по умолчанию (`en-US`). Этот код позволяет получить доступ к дополнительной информации, например к промежуточным результатам. Входные данные извлекаются из звукового файла. Поддерживаемый формат — одноканальный (монофонический) WAV- или PCM-файл с частотой дискретизации 16 кГц.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Дополнительная информация

- [Распознавание речи](./speech-to-text-sample.md)

- [Перевод](./translation.md)
