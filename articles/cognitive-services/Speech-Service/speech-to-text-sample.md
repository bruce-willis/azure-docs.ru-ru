---
title: Пример для преобразования речи в текст | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: В этой статье приведен пример для преобразования речи в текст.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030256"
---
# <a name="sample-for-speech-to-text"></a>Пример для преобразования речи в текст

> [!NOTE]
> Дополнительные сведения о скачивании этого и других примеров для SDK распознавания речи вы найдете [здесь](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Для всех приведенных ниже примеров нужны следующие объявления верхнего уровня:
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>Распознавание речи с помощью микрофона

В фрагменте кода ниже показано распознавание речи с микрофона на языке по умолчанию (`en-US`).

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>Распознавание речи из файла

В следующем фрагменте кода распознается речь из аудиофайла на языке по умолчанию (`en-US`). Поддерживается одноканальный (моно) формат WAV или PCM с частотой дискретизации 16 кГц.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>Распознавание речи с помощью настраиваемой модели

[Пользовательская служба распознавания речи (CRIS)](https://www.cris.ai/) позволяет настроить подсистему Майкрософт для преобразования речи в текст для конкретного приложения. В следующем фрагменте кода показано распознавание речи с микрофона при помощи модели CRIS. Для ее запуска необходимо указать ключ подписки и индивидуальный идентификатор развертывания.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>Непрерывное распознавание речи

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Исходный код примера

Последние версии примеров и более сложные примеры можно найти в соответствующем [репозитории GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk).

## <a name="next-steps"></a>Дополнительная информация

- [Распознавание намерений говорящего](./intent.md)

- [Перевод](./translation.md)
