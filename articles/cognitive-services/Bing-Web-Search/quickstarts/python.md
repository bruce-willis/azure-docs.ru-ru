---
title: Краткое руководство. Вызов API Bing для поиска в Интернете с использованием Python
description: Из этого краткого руководства вы узнаете, как вызвать API Bing для поиска в Интернете и получить ответ в формате JSON, используя Python.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: cd53a323a07617284e82004a6b3feed57b6e15e2
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42888613"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Краткое руководство. Вызов API Bing для поиска в Интернете с использованием Python  

Используйте это краткое руководство, чтобы за 10 минут вызвать API Bing для поиска в Интернете и получить ответ в формате JSON.  

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

Этот пример запускается как записная книжка Jupyter в [MyBinder](https://mybinder.org). Для этого щелкните эмблему запуска Binder:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="define-variables"></a>Определение переменных

Замените значение `subscription_key` действительным ключом подписки из своей учетной записи Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Объявите конечную точку API Bing для поиска в Интернете. Если вы столкнетесь с ошибками авторизации, внимательно сверьте это значение с конечной точкой поиска Bing на панели мониторинга Azure.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Вы можете настроить поисковый запрос, заменив значение для `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Выполнение запроса

В этом блоке для вызова API Bing для поиска в Интернете и возврата результатов в виде объекта JSON используется библиотека `requests`. Ключ API передается в словарь `headers`, а условие поиска и параметры запроса — в словарь `params`. Полный список вариантов и параметров см. в документации по [API Bing для поиска в Интернете версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Форматирование и отображение ответа

Объект `search_results` включает результаты поиска и метаданные, в частности связанные запросы и страницы. Этот код использует библиотеку `IPython.display`, чтобы форматировать и отображать ответ в браузере.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Пример кода в GitHub

Если вы хотите выполнить этот код локально, готовый [пример можно найти в GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingWebSearchv7.js).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для Поиска в Интернете](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
