---
title: 'Краткое руководство: поиск видео Bing, Python'
titlesuffix: Azure Cognitive Services
description: Сведения и примеры кода для быстрого начала работы с API Bing для поиска видео.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-video-search
ms.topic: quickstart
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: 797eb476aa3386949b08efb957edf48a97e40d6b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220022"
---
# <a name="quickstart-bing-video-search-api-with-python"></a>Краткое руководство: API Bing для поиска видео с использованием Python

В этом пошаговом руководстве показано, как использовать API Bing для поиска видео, входящий в состав служб Microsoft Cognitive Services в Azure. Технические сведения об интерфейсах API см. в [справочнике по API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).

Этот пример можно запустить как объект Jupyter Notebook в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder. 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingVideoSearchAPI.ipynb)


## <a name="prerequisites"></a>Предварительные требования
Необходима [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API-интерфейсами поиска Bing**. Для этого краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Потребуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure.

## <a name="running-the-walkthrough"></a>Работа с пошаговым руководством

Сначала задайте для `subscription_key` значение своего ключа API для службы API Bing.


```python
subscription_key = None
assert subscription_key
```

Затем проверьте правильность конечной точки `search_url`. На момент написания этой статьи для API-интерфейсов поиска Bing используется только одна конечная точка. Если вы столкнетесь с ошибками авторизации, то внимательно сверьте это значение с конечной точкой поиска Bing на панель мониторинга Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/videos/search"
```

Настройте `search_term` на поиск видео с котятами


```python
search_term = "kittens"
```

В следующем блоке для вызова API-интерфейсов поиска Bing и возврата результатов в формате объекта JSON используется библиотека `requests` в Python. Обратите внимание, что ключ API передается через словарь `headers`, а условие поиска — через словарь `params`. Чтобы просмотреть весь список параметров, доступных для фильтрации результатов поиска, обратитесь к документации по [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "count":5, "pricing": "free", "videoLength":"short"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Объект `search_results` содержит связанные видео вместе с подробными метаданными. Чтобы просмотреть любое видео, воспользуйтесь его свойством `embedHtml` и вставьте его в `IFrame`.


```python
from IPython.display import HTML
HTML(search_results["value"][0]["embedHtml"].replace("autoplay=1","autoplay=0"))
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Разбиение видео по страницам](paging-videos.md)
> [Изменение размера и обрезка эскизов изображений](resize-and-crop-thumbnails.md)

## <a name="see-also"></a>См. также 

 [Поиск видео в сети](search-the-web.md) [Пробная версия](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/)
