---
title: Документация по пакету SDK для службы "Речь" в Cognitive Services | Документы Майкрософт
description: Заметки о выпуске — что изменилось в последних версиях
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 0b1559d288380cf3d0c180a225278cc13d22a5d0
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2018
ms.locfileid: "35383431"
---
# <a name="release-notes"></a>Заметки о выпуске

## <a name="cognitive-services-speech-sdk-040-2018-june-release"></a>Пакет SDK для службы речи в Cognitive Services 0.4.0: выпуск за июнь 2018 г.

**Функциональные изменения**

- AudioInputStream

  Распознаватель теперь может использовать поток в качестве источника звука. Дополнительные сведения см. в соответствующем [практическом руководстве](how-to-use-audio-input-streams.md).

- Подробный формат выходных данных

  При создании `SpeechRecognizer` можно запросить формат выходных данных `Detailed` или `Simple`. `DetailedSpeechRecognitionResult` содержит оценку достоверности, распознанный текст, необработанную лексическую форму, нормализованную форму и нормализованную форму с маской ненормативной лексики.

**Критические изменения**

- В C# `SpeechRecognitionResult.RecognizedText` заменено на `SpeechRecognitionResult.Text`.

**Исправления ошибок**

- Устранена проблема с возможным обратным вызовом в слое USP во время завершения работы.

- Если распознаватель использует входной аудиофайл, он удерживает дескриптор файла дольше, чем необходимо.

- Устранены различные взаимоблокировки между генератором сообщений и распознавателем.

- Срабатывает результат `NoMatch` по истечении времени ожидания ответа от службы.

- Библиотеки Media Foundation в Windows загружаются с задержкой. Эта библиотека требуется только для ввода с микрофона.

- Скорость передачи для аудиоданных ограничена примерно в два раза относительно исходной скорости.

- В Windows сборки .NET C# теперь имеют строгие имена.

- Исправление документации: `Region` — обязательная информация для создания распознавателя.

Добавлены дополнительные примеры, которые постоянно обновляются. Актуальный набор примеров доступен в [репозитории GitHub с примерами пакета SDK для службы речи](https://aka.ms/csspeech/samples).

## <a name="cognitive-services-speech-sdk-0212733-2018-may-release"></a>Пакет SDK для службы речи в Cognitive Services 0.2.12733: выпуск за май 2018 г.

Первый выпуск общедоступной предварительной версии пакета SDK для службы речи в Cognitive Services.
