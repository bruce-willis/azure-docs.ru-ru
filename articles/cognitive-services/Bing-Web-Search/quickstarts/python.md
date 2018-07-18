---
title: Вызов и ответ. Краткое руководство по Azure Cognitive Services и API Bing для поиска в Интернете для Python | Документация Майкрософт
description: Получите информацию и примеры кода, которые помогут вам приступить к работе с API Bing для поиска в Интернете с использованием Microsoft Cognitive Services в Azure.
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 9/18/2017
ms.author: v-jerkin
ms.openlocfilehash: 8d4df9db60c7a74a5b9e53d4622528c0054b4f19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380716"
---
# <a name="call-and-response-your-first-bing-web-search-query-in-python"></a>Вызов и ответ: ваш первый запрос Bing для поиска в Интернете на Python

Процесс работы с API Bing для поиска в Интернете напоминает сайт Bing.com/Search, который возвращает результаты поиска, определенные службой Bing, как соответствующие запросу пользователя. Результаты могут содержать веб-страницы, изображения, видео, новости и сущности, наряду со связанными поисковыми запросами, исправлениями орфографических ошибок, часовыми поясами, преобразованием единиц измерения, переводами и вычислениями. Виды получаемых результатов зависят от их релевантности и уровня API-интерфейсов поиска Bing, на которые вы подписаны.

Технические сведения об интерфейсах API приведены в [справочнике по API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).

Этот пример можно запустить как объект Jupyter Notebook в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder. 

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)


## <a name="prerequisites"></a>предварительным требованиям
Необходима [учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API-интерфейсами поиска Bing**. Для данного краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api). Требуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure.

## <a name="running-the-walkthrough"></a>Выполнение пошагового руководства

Задайте для `subscription_key` значение своего ключа API для службы API Bing.


```python
subscription_key = None
assert subscription_key
```

Затем проверьте правильность конечной точки `search_url`. На момент написания этой статьи для API-интерфейсов поиска Bing используется только одна конечная точка. Если вы столкнетесь с ошибками авторизации, внимательно сверьте это значение с конечной точкой поиска Bing на панели мониторинга Azure.


```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Задайте `search_term` для запроса Bing к Microsoft Cognitive Services.


```python
search_term = "Microsoft Cognitive Services"
```

В следующем блоке для вызова API-интерфейсов поиска Bing и возврата результатов в формате объекта JSON используется библиотека `requests` на Python. Обратите внимание на то, что ключ API передается через словарь `headers`, а условие поиска — через словарь `params`. Чтобы просмотреть весь список параметров, доступных для фильтрации результатов поиска, изучите документацию по [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).


```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

Объект `search_results` содержит результаты поиска наряду с обширными метаданными, такими как связанные запросы и страницы. Приведенные ниже строки кода позволяют отформатировать самые посещаемые страницы, возвращенные запросом.


```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для Поиска в Интернете](../tutorial-bing-web-search-single-page-app.md)

## <a name="see-also"></a>См. также 

[Общие сведения об API Bing для Поиска в Интернете](../overview.md)  
[Пробная версия](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/)  
[Получение ключа доступа к бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)
[Справочник по API Bing для поиска в Интернете](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)
