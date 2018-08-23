---
title: Краткое руководство. Распознавание речи в приложении C++ для Linux с помощью пакета SDK службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
description: Узнайте, как распознавать речь в приложении C++ для Linux с помощью пакета SDK службы "Речь" в Cognitive Services.
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: b8bc24123fe75e876f607b07e37423ae68d92ee4
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41936190"
---
# <a name="quickstart-recognize-speech-in-c-on-linux-using-the-speech-sdk"></a>Краткое руководство. Распознавание речи в приложении C++ для Linux с помощью пакета SDK для службы "Речь"

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Из этой статьи вы узнаете, как в Linux (Ubuntu 16.04) с помощью пакета SDK службы "Речь" в Cognitive Services создать консольное приложение C++, которое будет преобразовывать речь в текст.

## <a name="prerequisites"></a>Предварительные требования

* Ключ подписки для службы распознавания речи. См. статью [Try the Speech service for free](get-started.md) (Пробное использование службы распознавания речи бесплатно).
* Компьютер Ubuntu 16.04 с рабочим микрофоном.
* Чтобы установить пакеты, необходимые для сборки и запуска этого примера, выполните следующую команду.

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

## <a name="get-the-speech-sdk"></a>Получение пакета SDK для службы "Речь"

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `0.6.0`.

Пакет SDK для распознавания речи в Cognitive Services для Linux доступен для создания 64- и 32-разрядных приложений.
Необходимые файлы можно скачать в виде TAR-файла здесь: https://aka.ms/csspeech/linuxbinary.
Скачайте и установите пакет SDK, как описано ниже.

1. Выберите каталог (абсолютный путь) для размещения двоичных файлов и заголовков пакета SDK для распознавания речи.
   Например, выберите путь `speechsdk` в домашнем каталоге.

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Если каталога нет, создайте его.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Скачайте и извлеките архив `.tar.gz` с двоичными файлами пакета SDK для распознавания речи.

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Проверьте содержимое каталога верхнего уровня извлеченного пакета.

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   В нем должно находиться уведомление сторонних разработчиков и файлы лицензий, а также каталог `include` для заголовков и каталог `lib` для библиотек.

   [!include[Linux Binary Archive Content](../../../includes/cognitive-services-speech-service-linuxbinary-content.md)]

## <a name="add-the-sample-code"></a>Добавление примеров кода

1. Добавьте приведенный ниже код в файл `helloworld.cpp`.

  [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-linux/helloworld.cpp#code)]

1. Замените строку `YourSubscriptionKey` своим ключом подписки.

1. Замените строку `YourServiceRegion` значением [региона](regions.md), связанного с подпиской (например, `westus` для бесплатной пробной подписки).

## <a name="building"></a>Сборка

> [!NOTE]
> Скопируйте и вставьте приведенные ниже команды сборки в виде _одной строки_.

* На компьютере **x64** выполните сборку приложения с помощью следующей команды.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* На компьютере **x86** выполните сборку приложения с помощью следующей команды.

  ```sh
  g++ helloworld.cpp -o helloworld -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="run-the-sample"></a>Запуск примера

1. Настройте путь к библиотеке загрузчика так, чтобы он указывал на библиотеку пакета SDK для службы "Речь".

   * На компьютере **x64** выполните следующую команду.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
     ```

   * На компьютере **x86** выполните следующую команду.

     ```sh
     export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
     ```

1. Запустите приложение следующим образом:

   ```sh
   ./helloworld
   ```

1. Должен отобразиться примерной такой результат:

   ```text
   Say something...
   We recognized: What's the weather
   ```

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Этот пример можно найти в папке `quickstart/cpp-linux`.

## <a name="next-steps"></a>Дополнительная информация

* [Получить примеры](speech-sdk.md#get-the-samples)
