---
title: Краткое руководство. Использование пакета SDK Поиска в Интернете Bing для Python
description: Сведения об использовании пакета SDK Поиска в Интернете Bing для Python.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: ff8dc93693a5aec7b6efa3aefd05de8c90f517ed
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43186819"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-python"></a>Краткое руководство. Использование пакета SDK Поиска в Интернете Bing для Python

Пакет SDK Поиска в Интернете Bing позволяет интегрировать поиск Bing в любое приложение Python. В этом кратком руководстве описано, как отправлять запрос, получать ответ в формате JSON, фильтровать и анализировать результаты.

Хотите увидеть код прямо сейчас? [Примеры для пакета SDK Bing для поиска в Интернете для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) доступны в GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Предварительные требования

Пакет SDK Поиска в Интернете Bing совместим с Python версии 2.7, 3.3, 3.4, 3.5 и 3.6. Мы рекомендуем использовать для этого руководства виртуальное окружение.

* Python версии 2.7, 3.3, 3.4, 3.5 или 3.6
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) для Python 2.7
* [venv](https://pypi.python.org/pypi/virtualenv) для Python 3.x

## <a name="create-and-configure-your-virtual-environment"></a>Создание и настройка виртуального окружения

Инструкции по установке и настройке виртуального окружения будут разными для версий Python 2.x и Python 3.x. Выполните описанные ниже шаги, чтобы создать и инициализировать виртуальное окружение.

### <a name="python-2x"></a>Python 2.x

Создайте виртуальное окружение с `virtualenv` для Python 2.7:

```console
virtualenv mytestenv
```

Активируйте созданное окружение:

```console
cd mytestenv
source bin/activate
```

Установите зависимости пакета SDK Bing для поиска в Интернете:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Создайте виртуальное окружение с `venv` для Python 3.x:

```console
python -m venv mytestenv
```

Установите зависимости пакета SDK Bing для поиска в Интернете:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Создание клиента и вывод первых результатов

Итак, вы настроили виртуальное окружение и установили зависимости. Теперь давайте создадим клиент. Этот клиент будет обрабатывать запросы и ответы для API Поиска в Интернете Bing.

Если ответ содержит веб-страницы, изображения, новости или видео, будет выведен первый результат в каждой категории.

1. Создайте проект Python, используя любую IDE или любой текстовый редактор.
2. Скопируйте в приложение следующий пример кода:  
    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchAPI
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client.
    client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```
3. Замените значение `subscription_key` действительным ключом подписки.
4. Запустите программу. Например, `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Определение функций и фильтрация результатов

Теперь, когда вы выполнили первый вызов к API Поиска в Интернете Bing, давайте изучим несколько функций, которые демонстрируют возможности пакета SDK по уточнению запросов и фильтрации результатов. Каждую из этих функций можно добавить в программу Python, которую вы создали в предыдущем разделе.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Ограничение числа результатов, возвращаемых Bing

В этом примере используются параметры `count` и `offset`, которые позволяют ограничить число результатов, возвращаемых [методом `search`](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) пакета SDK. Для первого результата возвращаются `name` и `URL`.

1. Добавьте следующий код в проект Python:
    ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```
2. Запустите программу.

### <a name="filter-for-news-and-freshness"></a>Фильтрация по новостям и актуальности

В этом примере используются параметры `response_filter` и `freshness` для фильтрации результатов поиска в [методе `search`](https://docs.microsoft.com//api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) пакета SDK. Будут возвращаться только те результаты поиска, которые соответствуют новостям и страницам, обнаруженным Bing за последние 24 часа. Для первого результата возвращаются `name` и `URL`.

1. Добавьте следующий код в проект Python:
    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```
2. Запустите программу.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Использование безопасного поиска, счетчика ответов и фильтра повышения уровня

В этом примере используются параметры `answer_count`, `promote` и `safe_search` для фильтрации результатов поиска в [методе `search`](https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search) пакета SDK. Для первого результата возвращаются `name` и `URL`.

1. Добавьте следующий код в проект Python:
    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python#search.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
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
2. Запустите программу.

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ключ подписки из кода программы и отключите виртуальное окружение, когда завершите работу с этим проектом.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры пакета SDK для Python в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>См. также

* [Справочник по пакету SDK Azure для Python](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/websearch)
