---
title: Краткое руководство. Запрос и фильтрация изображений с помощью пакета SDK в Node.js
description: Из этого краткого руководства вы узнаете, как запрашивать и фильтровать изображения, возвращаемые Поиском изображений Bing, с помощью Node.js.
titleSuffix: Azure cognitive services
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/12/2018
ms.author: v-gedod
ms.openlocfilehash: e88c045b220192a617e6b8caf5d8d53f70a25b5e
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41937530"
---
# <a name="quickstart-request-and-filter-images-using-the-sdk-and-nodejs"></a>Краткое руководство. Запрос и фильтрация изображений с помощью пакета SDK и Node.js

Пакет SDK для API "Поиск изображений" содержит функции REST API для обработки запросов на поиск изображений и анализа результатов. 

[Исходный код примеров для пакета SDK для Поиска изображений Bing для Node](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/imageSearch.js) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Зависимости приложения

Чтобы установить консольное приложение, использующее пакет SDK для Поиска изображений Bing, выполните команду `npm install azure-cognitiveservices-imagesearch` в среде разработки.

## <a name="image-search-client"></a>Клиент API Bing для поиска изображений
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) с помощью *поиска*. Создайте экземпляр `CognitiveServicesCredentials`:
```
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
```
Затем создайте экземпляр клиента:
```
const ImageSearchAPIClient = require('azure-cognitiveservices-imagesearch');
let client = new ImageSearchAPIClient(credentials);
```
Используйте клиент для выполнения поиска с текстом запроса "El Capitan":
```
client.imagesOperations.search('El Capitan', function (err, result, request, response) {
    if (err) throw err;
    console.log(result.value);
});

```
<!-- Need to sanitize result
The code prints `result.value` items to the console without parsing any text. The results will be:
- _type: 'ImageObjectElementType'

![Imageresults](media/node-sdk-quickstart-image-results.png)
-->

## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)