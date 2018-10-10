---
title: Приступая к работе с API распознавания речи Bing в JavaScript | Документация Майкрософт
titlesuffix: Azure Cognitive Services
description: Используйте API распознавания речи Bing в Cognitive Services для разработки приложений, которые непрерывно преобразовывают устную речь в текст.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: c4fb331076f88d4f14f56201aa31a00160f395aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995352"
---
# <a name="get-started-with-the-speech-recognition-api-in-javascript"></a>Приступите к работе с API распознавания речи в JavaScript

Вы можете разрабатывать приложения, которые преобразуют разговорное аудио в текст с помощью API распознавания речи. Клиентская библиотека JavaScript использует [Протокол службы распознавания речи WebSocket](../API-Reference-REST/websocketprotocol.md), который позволяет вам одновременно разговаривать и получать транскрибированный текст. Эта статья поможет вам начать работу с API распознавания речи в JavaScript.

## <a name="prerequisites"></a>Предварительные требования

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Подпишитесь на API распознавания речи и получите ключ бесплатной пробной версии подписки

SAPI — элемент Cognitive Services. Вы можете получить ключи бесплатной пробной версии подписки на странице [подписки на Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Чтобы получить ключ, после выбора SAPI выберите **Получить API-ключ**. Страница возвращает первичный и вторичный ключ. Оба ключа привязаны к одной и той же квоте, поэтому вы можете использовать любой из них.

> [!IMPORTANT]
> Получить ключ подписки. Прежде чем использовать клиентские библиотеки службы "Речь", вам необходимо иметь [ключ подписки](https://azure.microsoft.com/try/cognitive-services/).

## <a name="get-started"></a>Начало работы

В этом разделе мы рассмотрим необходимые шаги для загрузки образца HTML-страницы. Образец находится в нашем [репозитории Github](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript). Вы можете **открыть образец напрямую** из репозитория или **открыть образец из локальной копии** репозитория. 

> [!NOTE]
> Некоторые браузеры блокируют доступ к микрофону в незащищенном источнике. Поэтому рекомендуется размещать "sample" или "your app" на https, чтобы заставить его работать со всеми поддерживаемыми браузерами. 

### <a name="open-the-sample-directly"></a>Открытие образца напрямую

Получите ключ подписки, как описано выше. Затем откройте [ссылку на образец](https://htmlpreview.github.io/? https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript/blob/preview/samples/browser/Sample.html). Это загрузит страницу в ваш браузер по умолчанию (отображается с помощью [htmlPreview](https://github.com/htmlpreview/htmlpreview.github.com)).

### <a name="open-the-sample-from-a-local-copy"></a>Открытие образца из локальной копии

Чтобы оценить образец локально, клонируйте этот репозиторий:

```
git clone https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript
```

Скомпилируйте исходные файлы TypeScript и объедините их в один файл JavaScript (на компьютер необходимо установить [npm](https://www.npmjs.com/)). Перейдите в корень клонированного репозитория и выполните команды:

```
cd SpeechToText-WebSockets-Javascript && npm run bundle
```

Откройте `samples\browser\Sample.html` в предпочитаемом браузере.

## <a name="next-steps"></a>Дополнительная информация

Более подробную информацию о том, как включить SDK на своей собственной веб-странице, можно найти [здесь](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript).

## <a name="remarks"></a>Примечания

- API распознавания речи поддерживает три [режима распознавания](../concepts.md#recognition-modes). Режим можно переключить, обновив функцию **Setup()**,которую можно найти в файле Sample.html. Образец устанавливает режим `Interactive` по умолчанию. Чтобы изменить режим, обновите параметр `SR.RecognitionMode.Interactive` до другого режима. Например, измените параметр на  `SR.RecognitionMode.Conversation`.
- Полный список поддерживаемых языков см. в разделе [ Поддерживаемые языки ](../API-Reference-REST/supportedlanguages.md).

## <a name="related-topics"></a>Связанные разделы

- [Репозитории образцов API распознавания речи JavaScript](https://github.com/Azure-Samples/SpeechToText-WebSockets-Javascript)
- [Начало работы с REST API](GetStartedREST.md)
