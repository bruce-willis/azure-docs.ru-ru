---
title: Краткое руководство по использованию пакета SDK для API "Поиск новостей Bing" для Node | Документация Майкрософт
description: Настройка консольного приложения пакета SDK для Поиска новостей.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: 4ae99aa100b697a0dd75863c6f0c3c556dfa3d21
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382613"
---
# <a name="news-search-sdk-node-quickstart"></a>Краткое руководство по использованию пакета SDK для Поиска новостей для Node

Пакет SDK для API Bing для поиска новостей содержит функции REST API для обработки запросов новостей и анализа результатов. 

[Исходный код примеров для пакета SDK для Поиска новостей Bing для Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/newsSearch.js) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Зависимости приложения

Чтобы настроить консольное приложение, использующее пакет SDK для Поиска новостей Bing, выполните команду `npm install azure-cognitiveservices-newssearch` в среде разработки.

## <a name="news-search-client"></a>Клиент API Bing для поиска новостей
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) с помощью *поиска*. Создайте экземпляр `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Затем создайте экземпляр клиента:
```
const NewsSearchAPIClient = require('azure-cognitiveservices-newssearch');
let client = new NewsSearchAPIClient(credentials);
```
Используйте клиент, чтобы выполнить поиск по тексту запроса, как, например, по "Winter Olympics" в следующем примере:
```
client.newsOperations.search('Winter Olympics').then((result) => {
    console.log(result.value);
}).catch((err) => {
    throw err;
});

```
Код выводит элементы `result.value` в консоль без анализа какого-либо текста. Если будут получены результаты для какой-либо категории, они будут включать в себя следующее:
- _type: 'NewsArticle'
- _type: 'WebPage'
- _type: 'VideoObject'
- _type: 'ImageObject'

<!-- Remove until we can replace with santized version
![News results](media/node-sdk-quickstart-results.png)
-->

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
