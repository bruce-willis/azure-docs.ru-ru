---
title: Краткое руководство. Использование API компьютерного зрения с Python для анализа удаленного изображения | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: В этом кратком руководстве описано, как анализировать удаленное изображение с помощью API компьютерного зрения Cognitive Services и Python.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 65f9b0d4fb007a6a9b8ef489ca59f384e047a0dd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772342"
---
# <a name="quickstart-analyze-a-remote-image---rest-python"></a>Краткое руководство. Анализ удаленного изображения (REST, Python)

В этом кратком руководстве описано, как проанализировать удаленное изображение с помощью API компьютерного зрения. См. дополнительные сведения об [анализе локального изображения с помощью Python](python-disk.md).

Процедуры из этого руководства можно выполнять пошагово из записной книжки Jupyter с помощью [MyBinder](https://mybinder.org). Чтобы запустить модуль привязки, нажмите следующую кнопку:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="analyze-a-remote-image"></a>Анализ удаленного изображения

С помощью [метода Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) можно извлечь визуальные признаки на основе содержимого изображения. Вы можете передать изображение или указать его URL-адрес, а также настроить следующие возвращаемые компоненты:

* подробный список тегов, связанных с содержимым изображения;
* описание содержимого изображения в виде полного предложения;
* координаты, пол и возраст любого лица, показанного на изображении;
* значение ImageType (картинки или линейный рисунок);
* преобладающий цвет, контрастный цвет, наличие или отсутствие цветности;
* категория, определенная в этой [таксономии](../Category-Taxonomy.md);
* наличие на изображении содержимого для взрослых или содержимого сексуального характера.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в новый файл скрипта Python.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. При необходимости замените `vision_base_url` расположением, в котором вы получили ключи подписки.
1. При необходимости укажите в значении параметра `image_url` другое изображение.
1. Выполните скрипт.

В следующем коде используется библиотека `requests` для Python, которая обращается к API компьютерного зрения для анализа изображений. Результаты возвращаются в виде объектов JSON. Ключ API передается через словарь `headers`. Типы распознаваемых функций передаются через словарь `params`.

## <a name="analyze-image-request"></a>Запрос на анализ изображения

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

analyze_url = vision_base_url + "analyze"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/" + \
    "Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key }
params  = {'visualFeatures': 'Categories,Description,Color'}
data    = {'url': image_url}
response = requests.post(analyze_url, headers=headers, params=params, json=data)
response.raise_for_status()

# The 'analysis' object contains various fields that describe the image. The most
# relevant caption for the image is obtained from the 'description' property.
analysis = response.json()
print(analysis)
image_caption = analysis["description"]["captions"][0]["text"].capitalize()

# Display the image and overlay it with the caption.
image = Image.open(BytesIO(requests.get(image_url).content))
plt.imshow(image)
plt.axis("off")
_ = plt.title(image_caption, size="x-large", y=-0.1)
```

## <a name="analyze-image-response"></a>Результат анализа изображения

В случае успешного выполнения возвращается ответ в формате JSON, например:

```json
{
  "categories": [
    {
      "name": "outdoor_",
      "score": 0.00390625,
      "detail": {
        "landmarks": []
      }
    },
    {
      "name": "outdoor_street",
      "score": 0.33984375,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "building",
      "outdoor",
      "street",
      "city",
      "people",
      "busy",
      "table",
      "walking",
      "traffic",
      "filled",
      "large",
      "many",
      "group",
      "night",
      "light",
      "crowded",
      "bunch",
      "standing",
      "man",
      "sign",
      "crowd",
      "umbrella",
      "riding",
      "tall",
      "woman",
      "bus"
    ],
    "captions": [
      {
        "text": "a group of people on a city street at night",
        "confidence": 0.9122243847383961
      }
    ]
  },
  "color": {
    "dominantColorForeground": "Brown",
    "dominantColorBackground": "Brown",
    "dominantColors": [
      "Brown"
    ],
    "accentColor": "B54316",
    "isBwImg": false
  },
  "requestId": "c11894eb-de3e-451b-9257-7c8b168073d1",
  "metadata": {
    "height": 600,
    "width": 450,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с приложением Python, которое использует API компьютерного зрения для оптического распознавания символов (OCR) и создания интеллектуально обрезанных эскизов, а также для обнаружения, классификации, добавления тегов и описания визуальных признаков изображения, включая лица. Для быстрых экспериментов с API-интерфейсами компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Руководство по API компьютерного зрения для Python](../Tutorials/PythonTutorial.md)
