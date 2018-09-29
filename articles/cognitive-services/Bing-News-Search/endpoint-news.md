---
title: Конечные точки для поиска новостей Bing | Документация Майкрософт
description: Сводные сведения о конечной точке API для поиска новостей.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 11/28/2017
ms.author: v-gedod
ms.openlocfilehash: ab892e947566adf025499382b213a52ed3e96e35
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433690"
---
# <a name="news-search-endpoints"></a>Конечные точки для поиска новостей
**API для поиска новостей** возвращает новостные статьи, веб-страницы, изображения, видео и [сущности](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web). Сущности содержат сводные данные о человеке, месте или теме.
## <a name="endpoints"></a>Конечные точки
Чтобы получить результаты поиска новостей с помощью API Bing, отправьте запрос `GET` на одну из конечных точек, приведенных ниже. Заголовки и параметры URL-адреса определяют дополнительные спецификации.

**Конечная точка 1** возвращает новости на основе поискового запроса пользователя. Если отправить пустой поисковый запрос, то вызов вернет самые популярные новостные статьи. Параметр запроса `?q=""` также можно использовать с URL-адресом `/news`. Сведения о поддержке см. в разделе [Поддерживаемые страны и рынки](language-support.md#supported-markets-for-news-search-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search
```

**Конечная точка 2** возвращает самые популярные новости по категориям. Например, можно запросить главные статьи на тему бизнеса, спорта или развлечений, используя значения `category=business`, `category=sports` или `category=entertainment`.  Параметр `category` можно использовать только с URL-адресом `/news`. Существуют некоторые формальные требования для указания категорий. Ознакомьтесь с разделом `category` в документации по [параметрам запроса](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference#query-parameters). Сведения о поддержке см. в разделе [Поддерживаемые страны и рынки](language-support.md#supported-markets-for-news-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news  
```

**Конечная точка 3** возвращает темы новостей, которые в данный момент набирают популярности в социальных сетях. Когда параметр `/trendingtopics` включен, поиск Bing игнорирует некоторые другие параметры, такие как `freshness` и `?q=""`. Сведения о поддержке см. в разделе [Поддерживаемые страны и рынки](language-support.md#supported-markets-for-news-trending-endpoint).
```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics
```

Дополнительные сведения о заголовках, параметрах, кодах рынков, объектах ответов, ошибках и т. п. вы найдете в справочнике [по API Bing для поиска новостей версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).
## <a name="response-json"></a>Ответ в формате JSON
Ответ на запрос на поиск новостей содержит результаты в виде объектов JSON. Для анализа результатов требуются процедуры обработки элементов каждого типа. Чтобы ознакомиться с примерами, изучите это [руководство](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app) и [исходный код](https://docs.microsoft.com/azure/cognitive-services/bing-news-search/tutorial-bing-news-search-single-page-app-source).

## <a name="next-steps"></a>Дополнительная информация
Интерфейсы API **Bing** поддерживают действия поиска, которые возвращают результаты определенного типа. Все конечные точки поиска возвращают результаты в виде объектов ответа JSON.  Все конечные точки поддерживают запросы, которые возвращают результаты с учетом языка и (или) местоположения по значениям долготы, широты и радиуса поиска.

Полные сведения о параметрах, поддерживаемых каждой конечной точкой, приведены в справочной документации по каждому типу.
Простые примеры запросов к API для поиска новостей см. в [кратких руководствах по поиску новостей Bing](https://docs.microsoft.com/azure/cognitive-services/bing-news-search).
