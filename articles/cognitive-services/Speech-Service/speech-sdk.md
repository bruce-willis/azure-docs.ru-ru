---
title: Пакет SDK службы "Речь" в Cognitive Services | Документация Майкрософт
description: Обзор пакетов SDK, доступных для службы "Речь".
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ed523493f456e65f7aa5d3ad33914e3e52cd7044
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113398"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Пакет SDK службы "Речь" в Cognitive Services

Пакет средств разработки программного обеспечения (SDK) для службы "Речь" в Cognitive Services предоставляет приложениям прямой доступ к функциям службы "Речь", что облегчает разработку программного обеспечения. Сейчас пакет SDK предоставляет доступ к **преобразованию речи в текст**, **переводу речи** и **распознаванию намерений**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-windows-sdk"></a>Получение пакета SDK для Windows

Версия пакета SDK службы "Речь" для Windows включает в себя 32-разрядные и 64-разрядные клиентские библиотеки C/C++, а также управляемые библиотеки (.NET) для использования с C#. Пакет SDK можно установить в Visual Studio с помощью NuGet: просто выполните поиск `Microsoft.CognitiveServices.Speech`.

## <a name="get-the-linux-sdk"></a>Получение пакета SDK для Linux

Убедитесь в наличии необходимого компилятора и библиотек, выполнив следующие команды оболочки:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

> [!NOTE]
> Эти инструкции предполагают, что на вашем компьютере (x86 или x64) установлена ОС Ubuntu 16.04. В другой версии Ubuntu или другом дистрибутиве Linux следует адаптировать эти действия к конкретной среде.

Затем [скачайте пакет SDK](https://aka.ms/csspeech/linuxbinary) и распакуйте файлы в папку по своему усмотрению. В этой таблице показана структура папок пакета SDK.

|Путь|ОПИСАНИЕ|
|-|-|
|`license.md`|Лицензия|
|`third-party-notices.md`|Уведомления сторонних производителей|
|`include`|Файлы заголовков для C и C++|
|`lib/x64`|Собственная библиотека x64 для связывания с приложением|
|`lib/x86`|Собственная библиотека x86 для связывания с приложением|

Чтобы создать приложение, скопируйте или переместите необходимые двоичные файлы (и библиотеки) в среду разработки, а также включите их как обязательные в процесс сборки.

## <a name="get-the-java-sdk"></a>Получение пакета SDK для Java

Пакет SDK Java для Android входит в состав [AAR (библиотека Android)](https://developer.android.com/studio/projects/android-library), которая содержит библиотеки и разрешения Android, необходимые для его использования.
Она размещена в репозитории Maven в `https://csspeechstorage.blob.core.windows.net/maven/` в виде пакета `com.microsoft.cognitiveservices.speech:client-sdk:0.5.0`.
Чтобы использовать этот пакет в проекте Android Studio, внесите следующие изменения.

* В файл `build.gradle` уровня проекта добавьте следующий текст в раздел `repository`:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* В файл `build.gradle` уровня модуля добавьте следующий текст в раздел `dependencies`:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.5.0'
  ```

Пакет SDK для Java также входит в [пакет SDK для устройств распознавания речи](speech-devices-sdk.md).

## <a name="next-steps"></a>Дополнительная информация

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
