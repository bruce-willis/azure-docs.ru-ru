---
title: Документация по Azure Cognitive Services и API пакета SDK для службы "Речь" в Cognitive Services | Документы Майкрософт
description: Сведения о создании и разработке приложений с помощью пакета SDK для службы "Речь" в Cognitive Services
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: fe171ba9f6f0ff36a7c23c47f145d83f7a94fb5d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069494"
---
# <a name="shipping-an-application"></a>Доставка приложения

При распространении пакета SDK для службы "Речь" в Cognitive Services соблюдайте требования [лицензии на пакет SDK для службы "Речь"](license.md), а также [уведомлений для стороннего ПО](third-party-notices.md). Ознакомьтесь с [заявлением о конфиденциальности Майкрософт](https://aka.ms/csspeech/privacy).

Для выполнения приложений на разных платформах имеются различные зависимости.

## <a name="windows"></a>Windows

Пакет SDK для службы "Речь" в Cognitive Services протестирован для Windows 10 и Windows Server 2016.

Для пакета SDK для службы "Речь" в Cognitive Services в системе должен быть установлен [Распространяемый компонент Visual C++ для Visual Studio 2017](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads). Установщики для последней версии `Microsoft Visual C++ Redistributable for Visual Studio 2017` можно скачать здесь:

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

Если приложение использует управляемый код, на целевом компьютере требуется `.Net Framework 4.6.1` или более поздней версии.

Для ввода с микрофона необходимо установить библиотеки Media Foundation. Эти библиотеки входят в Windows 10 и Windows Server 2016. Пакет SDK для службы "Речь" можно использовать без этих библиотек, если в качестве аудиоустройства ввода не используется микрофон.

Необходимые файлы пакета SDK для службы "Речь" можно развернуть в том же каталоге, что и приложение. Таким образом, приложение сможет получать прямой доступ к библиотекам. Убедитесь, что выбрана правильная версия (Win32/x64), соответствующая приложению.

| ИМЯ | Функция
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | основной пакет SDK, необходимый для разработки управляемого и машинного кода
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | требуется для разработки управляемого кода
| `Microsoft.CognitiveServices.Speech.csharp.dll` | требуется для разработки управляемого кода

## <a name="linux"></a>Linux

Для приложений в машинном коде необходимо доставить библиотеку пакета SDK для службы "Речь", `libMicrosoft.CognitiveServices.Speech.core.so`.
Убедитесь, что выбрана правильная версия (x86, x64), соответствующая приложению. В зависимости от версии Linux может потребоваться включить следующие зависимости:

* общие библиотеки в библиотеке GNU C (включая библиотеку программирования потоков POSIX, `libpthreads`);
* библиотеку OpenSSL (`libssl.so.1.0.0`);
* библиотеку cURL (`libcurl.so.4`);
* общую библиотеку для приложений ALSA (`libasound.so.2`)

В Ubuntu 16.04, например, библиотеки GNU C должны быть уже установлены по умолчанию. Последние три библиотеки можно установить с помощью следующих команд:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>Дополнительная информация

* [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/)
* [Распознавание речи в C#](quickstart-csharp-dotnet-windows.md)
