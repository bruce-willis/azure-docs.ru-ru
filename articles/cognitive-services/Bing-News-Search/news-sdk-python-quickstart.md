---
title: Краткое руководство по пакету SDK для API "Поиск новостей" для Python | Документация Майкрософт
description: Установка консольного приложения пакета SDK для API "Поиск новостей".
titleSuffix: Azure News Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 6d212d1477ecf583a038e33e72aab3d60f6aa050
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382605"
---
# <a name="news-search-sdk-python-quickstart"></a>Краткое руководство по пакету SDK для API "Поиск новостей" для Python

Пакет SDK для API "Поиск новостей" содержит функции REST API для обработки веб-запросов и анализа результатов. 

[Исходный код примеров для пакета SDK API "Поиск новостей Bing" для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Зависимости приложения
Если у вас не установлен язык Python, установите его. Пакет SDK совместим с Python 2.7, 3.3, 3.4, 3.5 и 3.6.

Общей рекомендацией для разработки на Python является использование [виртуальной среды](https://docs.python.org/3/tutorial/venv.html). Установите и инициализируйте виртуальную среду с помощью [модуля venv](https://pypi.python.org/pypi/virtualenv). Необходимо установить virtualenv для Python 2.7.
```
python -m venv mytestenv
```
Установите зависимости пакета SDK для API "Поиск новостей Bing":
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-newssearch
```
## <a name="news-search-client"></a>Клиент для API "Поиск новостей"
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) в разделе *Поиск*. Добавьте операции импорта:
```
from azure.cognitiveservices.search.newssearch import NewsSearchAPI
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Создайте экземпляр `CognitiveServicesCredentials`. Создайте экземпляр клиента:
```
client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Выполните поиск результатов и выведите первый результат из списка веб-страниц:
```
news_result = client.news.search(query="Quantum Computing", market="en-us", count=10)
print("Search news for query \"Quantum Computing\" with market and count")

if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")

```
Выполните поиск с фильтрами последних новостей для поискового запроса "Artificial Intelligence" (Искусственный интеллект) с параметрами `freshness` и `sortBy`. Проверьте число результатов и выведите значения параметров `totalEstimatedMatches`, `name`, `url`, `description`, `published time` и `name of provider` для первого результата из списка новостей.
```
def news_search_with_filtering(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.search(
            query="Artificial Intelligence",
            market="en-us",
            freshness="Week",
            sort_by="Date"
        )
        print("\r\nSearch most recent news for query \"Artificial Intelligence\" with freshness and sortBy")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Выполните поиск по категориям новостей "Фильмы" и "Развлекательные телепрограммы", используя безопасный поиск. Проверьте число результатов и выведите значения параметров `category`, `name`, `url`, `description`, `published time` и `name of provider` для первого результата из списка новостей.
```
def news_category(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        news_result = client.news.category(
            category="Entertainment_MovieAndTV",
            market="en-us",
            safe_search="strict"
        )
        print("\r\nSearch category news for movie and TV entertainment with safe search")

        if news_result.value:
            first_news_result = news_result.value[0]
            print("News result count: {}".format(len(news_result.value)))
            print("First news category: {}".format(first_news_result.category))
            print("First news name: {}".format(first_news_result.name))
            print("First news url: {}".format(first_news_result.url))
            print("First news description: {}".format(first_news_result.description))
            print("First published time: {}".format(first_news_result.date_published))
            print("First news provider: {}".format(first_news_result.provider[0].name))
        else:
            print("Didn't see any news result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))


```
Выполните в Bing поиск новостей по темам, набирающим популярность.  Проверьте число результатов и выведите значения параметров `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` и `image Url` для первого результата из списка новостей.
```
def news_trending(subscription_key):

    client = NewsSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        trending_topics = client.news.trending(market="en-us")
        print("\r\nSearch news trending topics in Bing")

        if trending_topics.value:
            first_topic = trending_topics.value[0]
            print("News result count: {}".format(len(trending_topics.value)))
            print("First topic name: {}".format(first_topic.name))
            print("First topic query: {}".format(first_topic.query.text))
            print("First topic image url: {}".format(first_topic.image.url))
            print("First topic webSearchUrl: {}".format(first_topic.web_search_url))
            print("First topic newsSearchUrl: {}".format(first_topic.news_search_url))
        else:
            print("Didn't see any topics result data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```

## <a name="next-steps"></a>Дополнительная информация

[Примеры пакета SDK для Python в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


