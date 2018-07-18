---
title: Краткое руководство по пакету SDK для поиска в Интернете для Python | Документация Майкрософт
description: Установка компонентов консольного приложения пакета SDK для поиска в Интернете.
titleSuffix: Azure Cognitive Services Web search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 2a5fed58be863b882b827dbed73862bc690bab1e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382621"
---
# <a name="web-search-sdk-python-quickstart"></a>Краткое руководство по пакету SDK для поиска в Интернете для Python

Пакет SDK Bing для поиска в Интернете содержит функции REST API для обработки веб-запросов и анализа результатов. 

[Исходный код примеров для пакета SDK Bing для поиска в Интернете для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/web_search_samples.py) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Установка зависимостей
Если у вас не установлен язык Python, установите его. Пакет SDK совместим с Python 2.7, 3.3, 3.4, 3.5 и 3.6.

Общей рекомендацией для разработки на Python является использование [виртуальной среды](https://docs.python.org/3/tutorial/venv.html). Установите и инициализируйте виртуальную среду с помощью [модуля venv](https://pypi.python.org/pypi/virtualenv). Необходимо установить virtualenv для Python 2.7.
```
python -m venv mytestenv
```
Установите зависимости пакета SDK Bing для поиска в Интернете:
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```
## <a name="web-search-client"></a>клиент для поиска в Интернете.
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) в разделе *Поиск*. Добавьте элементы import и создайте экземпляр `CognitiveServicesCredentials`.
```
from azure.cognitiveservices.search.websearch import WebSearchAPI
from azure.cognitiveservices.search.websearch.models import SafeSearch
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Затем создайте экземпляр клиента.
```
client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Выполните поиск результатов и выведите первый результат веб-страницы.
```
web_data = client.web.search(query="Yosemite")
print("\r\nSearched for Query# \" Yosemite \"")

# WebPages
if web_data.web_pages.value:

    print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

    first_web_page = web_data.web_pages.value[0]
    print("First web page name: {} ".format(first_web_page.name))
    print("First web page URL: {} ".format(first_web_page.url))

else:
    print("Didn't see any Web data..")
```
Выведите результаты других типов, включая изображения, новости и видео.
```
# Images
if web_data.images.value:

    print("\r\nImage Results#{}".format(len(web_data.images.value)))

    first_image = web_data.images.value[0]
    print("First Image name: {} ".format(first_image.name))
    print("First Image URL: {} ".format(first_image.url))

else:
    print("Didn't see any Image..")
        
# News
if web_data.news.value:

    print("\r\nNews Results#{}".format(len(web_data.news.value)))

    first_news = web_data.news.value[0]
    print("First News name: {} ".format(first_news.name))
    print("First News URL: {} ".format(first_news.url))

else:
    print("Didn't see any News..")
            
# Videos
if web_data.videos.value:

    print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

    first_video = web_data.videos.value[0]
    print("First Videos name: {} ".format(first_video.name))
    print("First Videos URL: {} ".format(first_video.url))

else:
    print("Didn't see any Videos..")

```
Выполните поиск "Best restaurants in Seattle", проверьте количество результатов и выведите `name` и `URL` для первого результата.
```
def web_results_with_count_and_offset(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
        print("\r\nSearched for Query# \" Best restaurants in Seattle \"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))```

```
Выполните поиск "xbox", задав для `response_filter` значение `News`.  Выведите сведения о найденных новостях.
```
def web_search_with_response_filter(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(query="xbox", response_filter=["News"])
        print("\r\nSearched for Query# \" xbox \" with response filters \"News\"")

        # News attribute since I filtered "News"
        if web_data.news.value:

            print("Webpage Results#{}".format(len(web_data.news.value)))

            first_web_page = web_data.news.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
Выполните поиск с помощью запроса "Niagara Falls", указав параметры `answerCount` и `promote`. Выведите сведения о результатах.
```
def web_search_with_answer_count_promote_and_safe_search(subscription_key):

    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    try:
        web_data = client.web.search(
            query="Niagara Falls",
            answer_count=2,
            promote=["videos"],
            safe_search=SafeSearch.strict  # or directly "Strict"
        )
        print("\r\nSearched for Query# \" Niagara Falls\"")

        if web_data.web_pages.value:

            print("Webpage Results#{}".format(len(web_data.web_pages.value)))

            first_web_page = web_data.web_pages.value[0]
            print("First web page name: {} ".format(first_web_page.name))
            print("First web page URL: {} ".format(first_web_page.url))

        else:
            print("Didn't see any Web data..")

    except Exception as err:
        print("Encountered exception. {}".format(err))

```
## <a name="next-steps"></a>Дополнительная информация

[Примеры для пакета SDK для Cognitive Services для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


