---
title: Использование API средства для поиска аномалий с Python. Microsoft Cognitive Services | Документация Майкрософт
description: Информация и примеры кода, которые помогут вам приступить к работе с API средства для поиска аномалий с использованием Python в Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: c14916b0644edab613b298d6e71f8bbb9a6bb804
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381297"
---
# <a name="use-the-anomaly-finder-api-with-python"></a>Использование API средства для поиска аномалий с Python

В этой статье содержатся сведения и примеры кода, которые помогут вам быстро приступить к работе с API средства для поиска аномалий с Python для получения аномальных результатов в данных временных рядов.

## <a name="prerequisites"></a>предварительным требованиям

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-python"></a>Получение точек аномалий с помощью API средства для поиска аномалий с использованием Python 

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Пример данных временных рядов

Ниже приведен пример точек данных по временным рядам.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-python-example"></a>Пример анализа данных и получения точек аномалий с помощью Python

Убедитесь, что вы установили Python 3, затем создайте исполняемый файл Python с именем detect.py. В файл detect.py следует включить приведенный ниже код. Перед выполнением кода не забудьте заменить значение `[YOUR_SUBSCRIPTION_KEY]` на действующий ключ подписки.
Замените `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` точками данных.

```python
import requests
import json


def detect(url, subscription_key, request_data):
    headers = {'Content-Type': 'application/json', 'Ocp-Apim-Subscription-Key': subscription_key}
    response = requests.post(url, data=json.dumps(request_data), headers=headers)
    if response.status_code == 200:
        return json.loads(response.content.decode("utf-8"))
    else:
        print(response.status_code)
        raise Exception(response.text)


sample_data = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]"
endpont = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection"
subscription_key = "[YOUR_SUBSCRIPTION_KEY]"

result = detect(endpont, subscription_key, sample_data)
print(result)

```

### <a name="example-response"></a>Пример ответа

Успешный ответ будет возвращен в формате JSON. Пример ответа приведен ниже.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Приложение Python](../tutorials/python-tutorial.md)
