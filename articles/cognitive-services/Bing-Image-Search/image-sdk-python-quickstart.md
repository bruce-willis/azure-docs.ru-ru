---
title: Краткое руководство. Запрос и фильтрация изображений с помощью пакета SDK в Python
description: Из этого краткого руководства вы узнаете, как запрашивать и фильтровать изображения, возвращаемые Поиском изображений Bing, с помощью Python.
titleSuffix: Azure Image Search SDK Python quickstart
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 02/14/2018
ms.author: v-gedod
ms.openlocfilehash: 4729f103bb9b50d4ff039907db8eb677f3dc290a
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929703"
---
 # <a name="quickstart-request-and-filter-images-using-the-sdk-and-python"></a>Краткое руководство. Запрос и фильтрация изображений с помощью пакета SDK и Python

Пакет SDK для API "Поиск изображений" содержит функции REST API для обработки веб-запросов и анализа результатов. 

[Исходный код примеров для пакета SDK для API "Поиск изображений Bing" для Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image_search_samples.py) доступен на сайте GitHub.

## <a name="application-dependencies"></a>Зависимости приложения
Если у вас не установлен язык Python, установите его. Пакет SDK совместим с Python 2.7, 3.3, 3.4, 3.5 и 3.6.

Общей рекомендацией для разработки на Python является использование [виртуального окружения](https://docs.python.org/3/tutorial/venv.html). Установите и инициализируйте виртуальное окружение с помощью [модуля venv](https://pypi.python.org/pypi/virtualenv). Необходимо установить virtualenv для Python 2.7.
```
python -m venv mytestenv
```
Установите зависимости пакета SDK для API "Поиск изображений Bing":
```
cd mytestenv
python -m pip install azure-cognitiveservices-search-imagesearch
```
## <a name="image-search-client"></a>Клиент API Bing для поиска изображений
Получите [ключ доступа Cognitive Services](https://azure.microsoft.com/try/cognitive-services/) с помощью *поиска*. Добавьте операции импорта:
```
from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
from azure.cognitiveservices.search.imagesearch.models import ImageType, ImageAspect, ImageInsightModule
from msrest.authentication import CognitiveServicesCredentials

subscription_key = "YOUR-SUBSCRIPTION-KEY"
```
Создайте экземпляр `CognitiveServicesCredentials`, а затем создайте экземпляр клиента:
```
client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
```
Выполните поиск изображений по запросу (Yosemite), отфильтровав их по типу (анимированные GIF-изображения) и пропорциям (широкоформатные). Проверьте число результатов и выведите на экран значения параметров insightsToken, thumbnail URL и web URL для первого результата из списка.
```
image_results = client.images.search(
        query="Yosemite",
        image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
        aspect=ImageAspect.wide # Could be the str "Wide"
    )
    print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

    if image_results.value:
        first_image_result = image_results.value[0]
        print("Image result count: {}".format(len(image_results.value)))
        print("First image insights token: {}".format(first_image_result.image_insights_token))
        print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
        print("First image web search url: {}".format(first_image_result.web_search_url))
    else:
        print("Couldn't find image results!")

```
Выполните поиск изображений по запросу (Yosemite), отфильтровав их по типу (анимированные GIF-изображения) и пропорциям (широкоформатные).  Проверьте число результатов.  Выведите на экран значения параметров `insightsToken`, `thumbnail url` и `web url` для первого результата из списка.
```
image_results = client.images.search(
    query="Yosemite",
    image_type=ImageType.animated_gif, # Could be the str "AnimatedGif"
    aspect=ImageAspect.wide # Could be the str "Wide"
)
print("\r\nSearch images for \"Yosemite\" results that are animated gifs and wide aspect")

if image_results.value:
    first_image_result = image_results.value[0]
    print("Image result count: {}".format(len(image_results.value)))
    print("First image insights token: {}".format(first_image_result.image_insights_token))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image web search url: {}".format(first_image_result.web_search_url))
else:
    print("Couldn't find image results!")

```

Найдите изображения, набирающие популярность:
```
trending_result = client.images.trending()
print("\r\nSearch trending images")

# Categorires
if trending_result.categories:
    first_category = trending_result.categories[0]
    print("Category count: {}".format(len(trending_result.categories)))
    print("First category title: {}".format(first_category.title))
    if first_category.tiles:
        first_tile = first_category.tiles[0]
        print("Subcategory tile count: {}".format(len(first_category.tiles)))
        print("First tile text: {}".format(first_tile.query.text))
        print("First tile url: {}".format(first_tile.query.web_search_url))
    else:
        print("Couldn't find subcategory tiles!")
    else:
        print("Couldn't find categories!")

```

## <a name="next-steps"></a>Дополнительная информация

[Примеры пакета SDK для Python в Cognitive Services](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


