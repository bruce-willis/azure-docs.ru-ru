---
title: Пакет SDK для API "Поиск Bing" | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Пакет SDK для службы "Поиск Bing" для приложений, которые выполняют поиск в Интернете.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.assetid: ''
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 1/24/2018
ms.author: v-gedod
ms.openlocfilehash: 4a40ea665e153536d2322706b455598902ce41eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382604"
---
# <a name="bing-search-sdk"></a>Пакет SDK для службы "Поиск Bing"
Примеры использования API Bing для поиска новостей включают в себя следующие сценарии:
1. Запрос новостей по условиям поиска с параметрами `market` и `count`, проверка количества результатов, а также вывод `totalEstimatedMatches`, имени, URL-адреса, описания, времени публикации и имени поставщика первого результата из списка новостей.
2. Запрос последних новостей по условиям поиска с параметрами `freshness` и `sortBy`, проверка количества результатов, а также вывод `totalEstimatedMatches`, URL-адреса, описания, времени публикации и имени поставщика первого результата из списка новостей.
3. Запрос новостей по категориям `movie` и `TV entertainment` с использованием безопасного поиска, проверка количества результатов, а также вывод категории, имени, URL-адреса, описания, времени публикации и имени поставщика первого результата из списка новостей.
4. Запрос новостей по набирающим популярность темам в Bing, проверка количества результатов и вывод имени, текста запроса, `webSearchUrl`, `newsSearchUrl` и URL-адреса изображения первого результата из списка новостей.

Пакеты SDK для службы "Поиск Bing" позволяют использовать функцию поиска в Интернете на указанных ниже языках программирования.
* Начало работы с [примерами для .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7).
    * [Пакет NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0).
    * См. также определения и зависимости в [библиотеках .NET](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingNewsSearch).
* Начало работы с [примерами для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples). 
    * См. также определения и зависимости в [библиотеках Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/newsSearch).
* Начало работы с [примерами для Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples). 
    * См. также определения и зависимости в [библиотеках Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).
* Начало работы с [примерами для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples). 
    * См. также определения и зависимости в [библиотеках Python](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-newssearch).

Примеры пакетов SDK для каждого языка содержат файл сведений, в котором указаны предварительные требования и инструкции по установке и запуску этих примеров.