---
title: 'Краткое руководство: пакет SDK для поиска видео Bing, Node'
titleSuffix: Azure Cognitive Services
description: Настройка консольного приложения, использующего пакет SDK для поиска видео Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: rosh
ms.openlocfilehash: 4dcc9220d4d38bfe34514edd6a3ad47c7a7d4ba8
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225622"
---
# <a name="quickstart-bing-video-search-sdk-with-node"></a>Краткое руководство: пакет SDK для поиска видео Bing с использованием Node

Пакет SDK Bing для поиска видео содержит функции REST API для обработки запросов видео и анализа результатов. 

[Исходный код примеров для пакета SDK Bing для поиска видео для Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/videoSearch.js) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Установка зависимостей

Чтобы установить консольное приложение, использующее пакет SDK Bing для поиска видео, выполните команду `npm install azure-cognitiveservices-videosearch` в среде разработки.

## <a name="video-search-client"></a>Клиент для поиска видео
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) в разделе *Поиск*. Создайте экземпляр `CognitiveServicesCredentials`:
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
