---
title: Начало работы с Microsoft API распознавания речи с помощью клиентских библиотек | Документация Майкрософт
description: Используйте клиентские библиотеки для работы с речью Microsoft в Microsoft Cognitive Services для разработки приложений, которые преобразуют аудиозапись речи в текст.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 5abe5bc48c2bd73d0facf33e41a8076df2972153
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380544"
---
# <a name="get-started-with-speech-service-client-libraries"></a>Знакомство с клиентскими библиотеками для работы с речью

Помимо перенаправления HTTP-запросов через REST API, служба для работы с речью предоставляет разработчикам клиентские библиотеки для работы с речью на разных языках. Клиентские библиотеки для работы с речью:

- Поддерживают дополнительные возможности при распознавании речи, такие как промежуточные результаты в режиме реального времени, длительный аудиопоток (до 10 минут) и непрерывное распознавание.
- Предоставляют простой и идиоматический программный API на выбранном языке.
- Скрывают подробности сообщения.

В настоящее время доступны следующие клиентские библиотеки для работы с речью:

- [Библиотека для классических приложений C#](GetStartedCSharpDesktop.md)
- [Библиотека для служб C#](GetStartedCSharpServiceLibrary.md)
- [Библиотека для JavaScript](GetStartedJSWebsockets.md)
- [Библиотека Java для Android](GetStartedJavaAndroid.md)
- [Библиотека Objective-C для iOS](Get-Started-ObjectiveC-iOS.md)

## <a name="additional-resources"></a>Дополнительные ресурсы

- Страница [образцы](../samples.md) содержит полные образцы использования клиентских библиотек для работы с речью.
- Если вам нужна клиентская библиотека, которая еще не поддерживается, вы можете создать свой собственный пакет SDK. Реализуйте [протокол речи WebSocket ](../API-Reference-REST/websocketprotocol.md) на этой платформе и используйте нужный вам язык.

## <a name="license"></a>Лицензия

Все пакеты SDK и образцы Cognitive Services лицензированы лицензией MIT. Дополнительные сведения можно найти в разделе [Лицензия](https://github.com/Microsoft/Cognitive-Speech-STT-JavaScript/blob/master/LICENSE.md).
