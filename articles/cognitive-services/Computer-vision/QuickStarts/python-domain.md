---
title: Краткое руководство по API компьютерного зрения для Python. Модель предметной области| Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: В этом кратком руководстве вы будете использовать модели предметной области для распознавания знаменитостей и достопримечательностей на изображении с помощью API компьютерного зрения и языка Python в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 357cab72c0a6c9a2254350c84cda91c366ac685a
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771936"
---
# <a name="quickstart-use-a-domain-model---rest-python"></a>Краткое руководство. Использование модели предметной области (REST, Python)

В этом кратком руководстве вы будете использовать модели предметной области для распознавания знаменитостей и достопримечательностей на изображении с помощью API компьютерного зрения.

Процедуры из этого руководства можно выполнять пошагово из записной книжки Jupyter с помощью [MyBinder](https://mybinder.org). Чтобы запустить модуль привязки, нажмите следующую кнопку:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="identify-celebrities-and-landmarks"></a>Распознавание знаменитостей и достопримечательностей

С помощью [метода Recognize Domain Specific Content](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) (распознавание содержимого по определенной предметной области) можно найти на изображении определенный набор объектов. Сейчас поддерживаются две модели предметной области: _знаменитости_ и _достопримечательности_.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в новый файл скрипта Python.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. При необходимости замените `vision_base_url` расположением, в котором вы получили ключи подписки.
1. При необходимости укажите в значении параметра `image_url` другое изображение.
1. Выполните скрипт.

В следующем коде используется библиотека `requests` для Python, которая обращается к API компьютерного зрения для анализа изображений. Результаты возвращаются в виде объектов JSON. Ключ API передается через словарь `headers`. Используемая модель передается через словарь `params`.

## <a name="landmark-identification"></a>Распознавание достопримечательностей

### <a name="recognize-landmark-request"></a>Запрос на распознание ориентиров

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the westcentralus region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

landmark_analyze_url = vision_base_url + "models/landmarks/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/f/f6/" + \
    "Bunker_Hill_Monument_2005.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'model': 'landmarks'}
data    = {'url': image_url}
response = requests.post(
    landmark_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant landmark for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["landmarks"] is not []
print(analysis)
landmark_name = analysis["result"]["landmarks"][0]["name"].capitalize()

# Display the image and overlay it with the landmark name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(landmark_name, size="x-large", y=-0.1)
```

### <a name="recognize-landmark-response"></a>Ответ метода распознания ориентиров

В случае успешного выполнения возвращается ответ в формате JSON, например:

```json
{
  "result": {
    "landmarks": [
      {
        "name": "Bunker Hill Monument",
        "confidence": 0.9768505096435547
      }
    ]
  },
  "requestId": "659a10cd-44bb-44db-9147-a295b853b2b8",
  "metadata": {
    "height": 1600,
    "width": 1200,
    "format": "Jpeg"
  }
}
```

## <a name="celebrity-identification"></a>Распознавание знаменитостей

### <a name="recognize-celebrity-request"></a>Запрос на распознавание знаменитостей

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
#%matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

celebrity_analyze_url = vision_base_url + "models/celebrities/analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/d/d9/" + \
    "Bill_gates_portrait.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params  = {'model': 'celebrities'}
data    = {'url': image_url}
response = requests.post(
    celebrity_analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The
# most relevant celebrity for the image is obtained from the 'result' property.
analysis = response.json()
assert analysis["result"]["celebrities"] is not []
print(analysis)
celebrity_name = analysis["result"]["celebrities"][0]["name"].capitalize()

# Display the image and overlay it with the celebrity name.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(celebrity_name, size="x-large", y=-0.1)
```

### <a name="recognize-celebrity-response"></a>Ответ метода распознавания знаменитостей

В случае успешного выполнения возвращается ответ в формате JSON, например:

```json
{
  "result": {
    "celebrities": [
      {
        "faceRectangle": {
          "top": 123,
          "left": 156,
          "width": 187,
          "height": 187
        },
        "name": "Bill Gates",
        "confidence": 0.9993845224380493
      }
    ]
  },
  "requestId": "f14ec1d0-62d4-4296-9ceb-6b5776dc2020",
  "metadata": {
    "height": 521,
    "width": 550,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с приложением Python, которое использует API компьютерного зрения для оптического распознавания символов (OCR) и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных признаков изображения, включая лица. Для быстрых экспериментов с API-интерфейсами компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Руководство по API компьютерного зрения для Python](../Tutorials/PythonTutorial.md)
