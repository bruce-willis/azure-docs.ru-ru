---
title: Пакет SDK службы "Речь" в Cognitive Services
description: Обзор пакетов SDK, доступных для службы "Речь".
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 08/16/2018
ms.author: v-jerkin
ms.openlocfilehash: 6b5796bf4d049579dbdede2251f2ca67cc9c4bfd
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2018
ms.locfileid: "41929890"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Пакет SDK службы "Речь" в Cognitive Services

Пакет средств разработки программного обеспечения (SDK) для службы "Речь" в Cognitive Services предоставляет приложениям прямой доступ к функциям службы "Речь", что облегчает разработку программного обеспечения. Сейчас пакет SDK предоставляет доступ к **преобразованию речи в текст**, **переводу речи** и **распознаванию намерений**.

[!include[Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!include[License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Получение пакета SDK

### <a name="windows"></a>Windows

Для Windows поддерживаются следующие языки:

* C# (UWP и .NET), C++: можно ссылаться и использовать последнюю версию пакета средств разработки NuGet для распознавания речи.
  Пакет содержит 32-разрядные и 64-разрядные клиентские библиотеки, а также управляемые библиотеки (.NET).
  Пакет SDK можно установить в Visual Studio с помощью NuGet: просто выполните поиск `Microsoft.CognitiveServices.Speech`.

* Java: можно ссылаться и использовать последнюю версию пакета Speech SDK Maven, который поддерживает только Windows x64.
  В проект Maven добавьте `https://csspeechstorage.blob.core.windows.net/maven/` в качестве дополнительного репозитория и ссылку на `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` в качестве зависимости. 

### <a name="linux"></a>Linux

> [!NOTE]
> В настоящее время поддерживается только Ubuntu 16.04 на ПК (x86 или x64 для разработки приложений C++, x64 для .NET Core и Java).

Убедитесь, что у вас установлены необходимые компиляторы и библиотеки, выполнив следующие команды оболочки:

```sh
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2
```

* C#: можно ссылаться и использовать последнюю версию пакета средств разработки NuGet для распознавания речи.
  Чтобы ссылаться на пакет SDK, добавьте следующую ссылку на пакет в проект:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="0.6.0" />
  ```

* Java: можно ссылаться и использовать последнюю версию пакета SDK Maven для распознавания речи.
  В проект Maven добавьте `https://csspeechstorage.blob.core.windows.net/maven/` в качестве дополнительного репозитория и ссылку на `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0` в качестве зависимости. 

* C++: скачайте пакет SDK в виде [пакета TAR](https://aka.ms/csspeech/linuxbinary) и распакуйте файлы в папку по своему усмотрению. В таблице ниже показана структура папок пакета SDK.

  |Путь|ОПИСАНИЕ|
  |-|-|
  |`license.md`|Лицензия|
  |`ThirdPartyNotices.md`|Уведомления сторонних производителей|
  |`include`|Файлы заголовков для C и C++|
  |`lib/x64`|Собственная библиотека x64 для связывания с приложением|
  |`lib/x86`|Собственная библиотека x86 для связывания с приложением|

  Чтобы создать приложение, скопируйте или переместите необходимые двоичные файлы (и библиотеки) в среду разработки, а также включите их как обязательные в процесс сборки.

### <a name="android"></a>Android

Пакет SDK Java для Android входит в состав [AAR (библиотека Android)](https://developer.android.com/studio/projects/android-library), которая содержит библиотеки и разрешения Android, необходимые для его использования.
Она размещена в репозитории Maven в `https://csspeechstorage.blob.core.windows.net/maven/` в виде пакета `com.microsoft.cognitiveservices.speech:client-sdk:0.6.0`.
Чтобы использовать этот пакет из проекта Android Studio, внесите следующие изменения:

* В файл `build.gradle` уровня проекта добавьте следующий текст в раздел `repository`:

  ```text
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* В файл `build.gradle` уровня модуля добавьте следующий текст в раздел `dependencies`:

  ```text
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:0.6.0'
  ```

Пакет SDK для Java также входит в [пакет SDK для устройств распознавания речи](speech-devices-sdk.md).

[!include[Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Дополнительная информация

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
