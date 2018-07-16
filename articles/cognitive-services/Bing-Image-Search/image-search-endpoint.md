---
title: Конечные точки для поиска изображений | Документация Майкрософт
description: Сводные сведения о конечной точке API для поиска изображений.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 11/30/2017
ms.author: v-gedod
ms.openlocfilehash: 0d5f28bfdb45b27b04df068f75e8a20d0235d12b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380024"
---
# <a name="image-search-endpoints"></a>Конечные точки для поиска изображений
**API для поиска изображений** включает три конечные точки.  Конечная точка 1 возвращает изображения из Интернета на основе запроса. Конечная точка 2 возвращает [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imageinsightsresponse) (аналитические сведения об изображениях).  Конечная точка 3 возвращает популярные изображения.
## <a name="endpoints"></a>Конечные точки
Чтобы получить результаты поиска изображений с помощью API Bing, отправьте запрос на одну из конечных точек, приведенных ниже. Для определения дополнительных спецификаций используйте заголовки и параметры URL-адреса.

**Конечная точка 1** возвращает результаты поиска изображений, соответствующие поисковому запросу пользователя, который определен с помощью `?q=""`.
``` 
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search
```

**Конечная точка 2** возвращает аналитические сведения об изображении, используя `GET` или `POST`.
``` 
 GET or POST https://api.cognitive.microsoft.com/bing/v7.0/images/details
```
Запрос GET возвращает аналитические сведения об изображении, например веб-страницы, содержащие данное изображение. Включите в запрос `GET` параметр [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#insightstoken).

Вы также можете включить в текст запроса `POST` двоичный файл изображения и задать для параметра [modules](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#modulesrequested) значение `RecognizedEntities`. При этом будет возвращено значение [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v5-reference#insightstoken), используемое в качестве параметра в следующем запросе `GET`, который возвращает сведения о людях на данном изображении.  Задайте для параметра `modules` значение `All`, чтобы получить в результатах `POST` все аналитические сведения, кроме `RecognizedEntities`. При этом не потребуется выполнять второй вызов, используя параметр `insightsToken`. 


**Конечная точка 3** возвращает изображения, которые набирают популярность. За основу берутся результаты поисковых запросов, выполненных другими пользователями. Изображения разделяются на различные категории, например, если в них фигурируют примечательные люди или события.
```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending
```

Список рынков, для которых поддерживается поиск популярных изображений, см. в статье [Получение изображений, набирающих популярность](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images).

Дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API Bing для поиска изображений версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
## <a name="response-json"></a>Ответ в формате JSON
Ответ на запрос на поиск изображений содержит результаты в виде объектов JSON. Примеры синтаксического анализа результатов см. в этом [руководстве](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app) и [исходном коде](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/tutorial-bing-image-search-single-page-app-source).

## <a name="next-steps"></a>Дополнительная информация
Интерфейсы API **Bing** поддерживают действия поиска, которые возвращают результаты определенного типа. Все конечные точки поиска возвращают результаты в виде объектов ответа JSON.  Все конечные точки поддерживают запросы, которые возвращают результаты с учетом языка и (или) местоположения по значениям долготы, широты и радиуса поиска.

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, приведены в справочной документации по каждому типу.
Простые примеры запросов к API для поиска изображений см. в [кратких руководствах по поиску изображений](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/search-the-web).