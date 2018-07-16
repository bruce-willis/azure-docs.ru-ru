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
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: b9b7b8af5ce3d75788fd2c4f5e0309b5ca561a8f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382870"
---
# <a name="about-the-cognitive-services-speech-sdk"></a>Пакет SDK службы "Речь" в Cognitive Services

Пакет средств разработки программного обеспечения (SDK) для службы "Речь" в Cognitive Services предоставляет приложениям прямой доступ к функциям службы "Речь", что облегчает разработку программного обеспечения. Сейчас пакет SDK предоставляет доступ к **преобразованию речи в текст**, **переводу речи** и **распознаванию намерений**.

В таблице перечислены поддерживаемые сейчас языки программирования и операционные системы.

|Поддерживаемая операционная система|Язык программирования|
|-|-|
|Windows|C/C++, C#|
|Linux|C/C++|
|Устройства|Java\*|

\* *Пакет SDK для Java входит в [пакет SDK для речевых устройств](speech-devices-sdk.md).*

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

Пакет SDK для Java входит в [пакет SDK для речевых устройств](speech-devices-sdk.md).

## <a name="next-steps"></a>Дополнительная информация

* [Получение пробной подписки на службу "Речь"](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-windows.md)
