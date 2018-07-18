---
title: Краткое руководство по пакету SDK для поиска видео для Node | Документация Майкрософт
description: Установка компонентов консольного приложения пакета SDK для поиска видео.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 5718c750288e0a5605db3296d2911cca5e03375c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35382972"
---
# <a name="video-search-sdk-node-quickstart"></a>Краткое руководство по пакету SDK для поиска видео для Node

Пакет SDK Bing для поиска видео содержит функции REST API для обработки запросов видео и анализа результатов. 

[Исходный код примеров для пакета SDK Bing для поиска видео для Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Установка зависимостей

Чтобы установить консольное приложение, использующее пакет SDK Bing для поиска видео, выполните команду `npm install azure-cognitiveservices-videosearch` в среде разработки.

## <a name="video-search-client"></a>Клиент для поиска видео
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) в разделе *Поиск*. Создайте экземпляр `CognitiveServicesCredentials`.
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Затем создайте экземпляр клиента.
```
const VideoSearchAPIClient = require('azure-cognitiveservices-videosearch');
let client = new VideoSearchAPIClient(credentials);
```
Выполните поиск результатов.
```
client.videosOperations.search('Interstellar Trailer').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```

<!-- Remove until the response can be replace with a sanitized version.
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'VideoObjectElementType'

![Video results](media/video-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
