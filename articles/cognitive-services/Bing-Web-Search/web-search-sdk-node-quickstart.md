---
title: Краткое руководство по пакету SDK для поиска в Интернете для Node | Документация Майкрософт
description: Установка компонентов консольного приложения пакета SDK для поиска в Интернете.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 44f7f97f6c442df3fbb1e5e08189b8db7d4b9db0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382624"
---
# <a name="web-search-sdk-node-quickstart"></a>Краткое руководство по пакету SDK для поиска в Интернете для Node

Пакет SDK Bing для поиска в Интернете содержит функции REST API для обработки веб-запросов и анализа результатов.

[Исходный код примеров для пакета SDK Bing для поиска в Интернете для Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/webSearch.js) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Установка зависимостей

Чтобы установить консольное приложение, использующее пакет SDK Bing для поиска в Интернете, выполните команду `npm install azure-cognitiveservices-websearch` в среде разработки.

## <a name="web-search-client"></a>клиент для поиска в Интернете.
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) в разделе *Поиск*. Создайте экземпляр `CognitiveServicesCredentials`.
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Затем создайте экземпляр клиента.
```
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```
Выполните поиск результатов.
```
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})

```
Код выводит элементы `result.value` в консоль без анализа какого-либо текста.  Если будут получены результаты для какой-либо категории, они будут включать в себя следующее:
- _type: 'ImageObject'
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObjectElementType'

<!-- Remove until this can be replaced with a sanitized version.
![Video results](media/web-search-sdk-node-results.png)
-->

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
