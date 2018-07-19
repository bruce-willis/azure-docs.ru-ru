---
title: Основные понятия AudioInputStream | Документы Майкрософт
description: Общие сведения о возможностях API AudioInputStream.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: fmegen
manager: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: fmegen
ms.openlocfilehash: 0eafa7e88df5d00a67646ca7f82ca027602a40b3
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39071452"
---
# <a name="about-the-audio-input-stream-api"></a>Сведения об API входного аудиопотока

API **входного аудиопотока** обеспечивает способ потоковой передачи звука в распознаватели вместо использования API микрофона или входного файла.

## <a name="api-overview"></a>Обзор API

API использует два компонента `AudioInputStream` (необработанные аудиоданные) и `AudioInputStreamFormat`.

`AudioInputStreamFormat` определяет формат аудиоданных. Его можно сравнить со стандартной структурой `WAVEFORMAT` для WAVE-файлов в Windows.

  - `FormatTag`

    Формат аудио. Пакет SDK для службы "Речь" сейчас поддерживает только `format 1` (PCM, прямой порядок байтов).

  - `Channels`

    Число каналов. Сейчас служба речи поддерживает только один канал аудио (моно).

  - `SamplesPerSec`

    Частота дискретизации. Обычная запись с микрофона выполняется с частотой 16 000 выборок в секунду.

  - `AvgBytesPerSec`

    Среднее число байт в секунду, вычисляется как `SamplesPerSec * Channels * ceil(BitsPerSample, 8)`. Среднее число байт в секунду может отличаться для аудиопотоков, использующих переменные скорости.

  - `BlockAlign`

    Размер одного кадра, вычисляется как `Channels * ceil(wBitsPerSample, 8)`. Из-за заполнения фактическое значение может быть выше, чем это значение.

  - `BitsPerSample`

    Число бит на выборку. Обычно аудиопоток использует 16 бит на выборку (качество компакт-диска).

Базовый класс `AudioInputStream` будут переопределен настраиваемым адаптером потока. Этот адаптер должен реализовать следующие функции:

   - `GetFormat()`

     Эта функция вызывается для получения формата аудиопотока. Она получает указатель на буфер AudioInputStreamFormat.

   - `Read()`

     Эта функция вызывается для получения данных из аудиопотока. Один параметр является указателем на буфер для копирования аудиоданных. Второй параметр — размер буфера. Функция возвращает число байт, копируемых в буфер. Возвращаемое значение `0` обозначает конец потока.

   - `Close()`

     Эта функция вызывается для закрытия аудиопотока.

## <a name="usage-examples"></a>Примеры использования

Как правило, при использовании входных аудиопотоков выполняется следующая процедура.

  - Определите формат аудиопотока. Формат должен поддерживаться пакетом SDK и службой речи. Сейчас поддерживается следующая конфигурация:

    один тег формата аудио (PCM), один канал, 16 000 выборок в секунду, 32 000 байт в секунду, выравнивание по двум блокам (16 бит, включая заполнение для выборки), 16 бит на выборку

  - Убедитесь, что ваш код может предоставлять НЕОБРАБОТАННЫЕ аудиоданные, соответствующие указанным выше характеристикам. Если источник аудиоданных не соответствует поддерживаемым форматам, звук необходимо перекодировать в требуемый формат.

  - Создайте настраиваемый класс входного аудиопотока, производный от `AudioInputStream`. Реализуйте операции `GetFormat()`, `Read()` и `Close()`. Точная сигнатура функции зависит от языка, однако код будет выглядеть аналогично этому примеру кода:

    ```
     public class ContosoAudioStream : AudioInputStream {
        ContosoConfig config;

        public ContosoAudioStream(const ContosoConfig& config) {
            this.config = config;
        }

        public void GetFormat(AudioInputStreamFormat& format) {
            // returns format data to the caller.
            // e.g. format.FormatTag = config.XXX;
            // ...
        }

        public size_t Read(byte *buffer, size_t size) {
            // returns audio data to the caller.
            // e.g. return read(config.YYY, buffer, size);
        }

        public void Close() {
            // close and cleanup resources.
        }
     };
    ```

  - Используйте входной аудиопоток:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    var result = await recognizer.RecognizeAsync();

    var text = result.GetText();

    // In some languages you need to delete the stream explicitly.
    // delete contosoStream;
    ```

  - В некоторых языках `contosoStream` необходимо явно удалять после завершения распознавания. Нельзя освободить AudioStream до завершения считывания входных данных. В случае использования `StopContinuousRecognitionAsync` и `StopContinuousRecognitionAsync` требуется концепция, иллюстрируемая в этом примере:

    ```
    var contosoStream = new ContosoAudioStream(contosoConfig);

    var factory = SpeechFactory.FromSubscription(...);
    var recognizer = CreateSpeechRecognizerWithStream(contosoStream);

    // run stream through recognizer
    await recognizer.StartContinuousRecognitionAsync();

    // ERROR: do not delete the contosoStream before ending recognition!
    // delete contosoStream;

    await recognizer.StopContinuousRecognitionAsync();

    // OK: Safe to delete the contosoStream.
    // delete contosoStream;
    ```

## <a name="next-steps"></a>Дополнительная информация

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
