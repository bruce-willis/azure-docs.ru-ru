---
title: Краткое руководство про пакету SDK для распознавания речи в C++ для Linux | Документы Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Сведения и примеры кода для быстрого начала использования пакета SDK для распознавания речи в Linux и C++ в Cognitive Services.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: cee70ba585f93dda3249fc5b39f25fb613b57a45
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753632"
---
# <a name="quickstart-for-c-and-linux"></a>Краткое руководство для C++ и Linux

Текущая версия пакета SDK для распознавания речи для Cognitive Services — `0.4.0`.

Пакет SDK для распознавания речи в Cognitive Services для Linux доступен для создания 64- и 32-разрядных приложений. Необходимые файлы можно скачать в виде TAR-файла здесь: https://aka.ms/csspeech/linuxbinary.

> [!NOTE]
> Если вам необходимо краткое руководство для C++ и Windows, см. [эту статью](quickstart-cpp-windows.md).
> Если вам необходимо краткое руководство для C# и Windows, см. [эту статью](quickstart-csharp-windows.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Эти инструкции предполагают, что на вашем ПК (x86 или x64) установлена ОС Ubuntu 16.04.
> В другой версии Ubuntu или другом дистрибутиве Linux вам потребуется адаптировать необходимые действия.

## <a name="prerequisites"></a>предварительным требованиям

[!include[Ubuntu Prerequisites](includes/ubuntu1604-prerequisites.md)]

## <a name="getting-the-binary-package"></a>Получение пакета двоичных файлов

[!include[License Notice](includes/license-notice.md)]

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

   [!include[Linux Binary Archive Content](includes/linuxbinary-content.md)]

## <a name="sample-code"></a>Пример кода

Приведенный далее код распознает английскую речь, произнесенную в микрофон.
Поместите его в файл `quickstart-linux.cpp`.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Linux/quickstart-linux/quickstart-linux.cpp#code)]

Замените ключ подписки в коде на полученный вами ключ.

## <a name="building"></a>Сборка

> [!NOTE]
> Скопируйте и вставьте приведенные ниже команды сборки в виде _одной строки_.

* Запустите сборку приложения на компьютере x64 с помощью следующей команды:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x64" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

* Запустите сборку приложения на компьютере x86 с помощью следующей команды:

  ```sh
  g++ quickstart-linux.cpp -o quickstart-linux -I "$SPEECHSDK_ROOT/include/cxx_api" -I "$SPEECHSDK_ROOT/include/c_api" --std=c++14 -lpthread -lMicrosoft.CognitiveServices.Speech.core -L "$SPEECHSDK_ROOT/lib/x86" -l:libssl.so.1.0.0 -l:libcurl.so.4 -l:libasound.so.2
  ```

## <a name="running"></a>Выполнение

Для запуска приложения потребуется настроить путь к библиотеке загрузчика так, чтобы он указывал на библиотеку пакета SDK для распознавания речи.

* На компьютере x64 выполните следующую команду:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x64"
  ```

* На компьютере x86 выполните следующую команду:

  ```sh
  export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:$SPEECHSDK_ROOT/lib/x86"
  ```

Запустите приложение следующим образом:

```sh
./quickstart-linux
```

Если все работает правильно, вы должны увидеть результат, аналогичный приведенному ниже.

```text
Say something...
We recognized: What's the weather
```

## <a name="downloading-the-sample"></a>Скачивание примера

Актуальный набор примеров доступен в [репозитории GitHub с примерами для пакета SDK для распознавания текста в Cognitive Services](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные примеры можно найти на [странице с примерами](samples.md).
