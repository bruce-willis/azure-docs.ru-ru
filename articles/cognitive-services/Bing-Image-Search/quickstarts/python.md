---
title: Краткое руководство. Отправка поисковых запросов с помощью Python и REST API для API Bing для поиска изображений
description: Из этого краткого руководства вы узнаете, как отправлять поисковые запросы в API Поиска Bing, чтобы получить список соответствующих изображений, используя Python.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bc527ba39b580935f113f56aa63f7bdd283ba304
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41929752"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-python"></a>Краткое руководство. Отправка поисковых запросов с помощью REST API и Python

API Bing для поиска изображений предоставляет примерно такие же возможности, как просмотр сведений об изображении на Bing.com/images. API позволяет отправлять поисковый запрос пользователя в Bing и получать в ответ список соответствующих изображений.

В этом пошаговом руководстве демонстрируется простой пример вызова API Bing для поиска изображений и последующей обработки полученного в результате объекта JSON. Дополнительные сведения см. в [документации по API Bing для поиска изображений](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

Этот пример можно запустить как записную книжку Jupyter в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder: 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-walkthrough"></a>Выполнение пошагового руководства
Чтобы продолжить выполнение этого пошагового руководства, задайте в качестве `subscription_key` ключ API для соответствующей службы API Bing.


```python
subscription_key = None
assert subscription_key
```

Затем проверьте правильность конечной точки `search_url`. На момент написания этой статьи для API-интерфейсов поиска Bing используется только одна конечная точка. Если вы столкнетесь с ошибками авторизации, внимательно сверьте это значение с конечной точкой поиска Bing на панели мониторинга Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
```

Задайте `search_term` для поиска изображений щенков.


```python
search_term = "puppies"
```

В следующем блоке для вызова API-интерфейсов поиска Bing и возврата результатов в формате объекта JSON используется библиотека `requests` на Python. Обратите внимание, что ключ API передается через словарь `headers`, а условие поиска — через словарь `params`. Чтобы просмотреть весь список параметров, доступных для фильтрации результатов поиска, обратитесь к документации по [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "license": "public", "imageType": "photo"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Объект `search_results` содержит фактические рисунки вместе с обширными метаданными, такими как связанные элементы. Например, следующая строка кода позволяет извлечь URL-адреса эскизов для первых 16 результатов.


```python
thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
```

Затем мы с помощью библиотеки `PIL` можем скачать эскизы изображений, а с помощью библиотеки `matplotlib` — преобразовать их для просмотра в сетке $4 \times 4$.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

f, axes = plt.subplots(4, 4)
for i in range(4):
    for j in range(4):
        image_data = requests.get(thumbnail_urls[i+4*j])
        image_data.raise_for_status()
        image = Image.open(BytesIO(image_data.content))        
        axes[i][j].imshow(image)
        axes[i][j].axis("off")
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для поиска изображений](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>См. также 

[Обзор API Bing для поиска изображений](../overview.md)  
[Пробная версия](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Получение ключа доступа для бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Справочник по API Bing для поиска изображений](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
