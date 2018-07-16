---
title: Краткое руководство по работе с API распознавания эмоций с использованием Python | Документация Майкрософт
description: Информация и примеры кода, которые помогут вам приступить к работе с API распознавания эмоций с использованием Python в Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/05/2018
ms.author: anroth
ms.openlocfilehash: ff1f6b2ddc872d0ee63d9885b04b1f007bc86e33
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380177"
---
# <a name="emotion-api-python-quickstart"></a>Краткое руководство по работе с API распознавания эмоций с использованием Python

> [!IMPORTANT]
> Срок действия предварительной версии API для поиска видео истек 30 октября 2017 года. Оцените новую [предварительную версию API индексатора видео](https://azure.microsoft.com/services/cognitive-services/video-indexer/), которая позволяет легко извлекать аналитические сведения из видео и оптимизировать возможности обнаружения содержимого (например, результаты поиска) по обнаружению произнесенных слов, лиц, символов и эмоций. [Узнайте больше](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

В этом пошаговом руководстве содержатся сведения и примеры кода, которые помогут вам быстро приступить к распознаванию эмоций одного человека или нескольких людей на изображении с помощью Python и [метода API распознавания эмоций](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa). 

Этот пример можно запустить как объект Jupyter Notebook в [MyBinder](https://mybinder.org), щелкнув эмблему запуска Binder: [![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=EmotionAPI.ipynb)


## <a name="prerequisite"></a>Предварительные требования
Получите бесплатный ключ подписки [здесь](https://azure.microsoft.com/try/cognitive-services/).

## <a name="running-the-walkthrough"></a>Выполнение пошагового руководства
Чтобы продолжить работу с этим пошаговым руководством, замените `subscription_key` ключом API, полученным ранее.


```python
subscription_key = None
assert subscription_key
```

Затем убедитесь, что URL-адрес службы соответствует региону, который вы указали при настройке ключа API. Если вы используете ключ пробной версии, ничего изменять не нужно.


```python
emotion_recognition_url = "https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize"
```

В этом пошаговом руководстве используются изображения, которые хранятся на диске. Можно также использовать изображения, предоставляемые с помощью общедоступного URL-адреса. Дополнительные сведения см. в [документации по REST API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa).

Так как данные изображений передаются в тексте запроса, для заголовка `Content-Type` необходимо задать значение `application/octet-stream`. В случае передачи изображения по URL-адресу не забудьте задать заголовок, как показано ниже.
```python
header = {'Ocp-Apim-Subscription-Key': subscription_key }
```
Создайте словарь, содержащий URL-адрес.
```python
data = {'url': image_url}
```
И передайте его в библиотеку `requests`.
```python
requests.post(emotion_recognition_url, headers=headers, json=image_data)
```

Сначала скачайте несколько примеров изображений с сайта [API распознавания эмоций](https://azure.microsoft.com/services/cognitive-services/emotion/).


```bash
%%bash
mkdir -p images
curl -Ls https://aka.ms/csnb-emotion-1 -o images/emotion_1.jpg
curl -Ls https://aka.ms/csnb-emotion-2 -o images/emotion_2.jpg
```


```python
image_path = "images/emotion_1.jpg"
image_data = open(image_path, "rb").read()
```


```python
import requests
headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
response.raise_for_status()
analysis = response.json()
analysis
```




    [{'faceRectangle': {'height': 162, 'left': 130, 'top': 141, 'width': 162},
      'scores': {'anger': 9.29041e-06,
       'contempt': 0.000118981574,
       'disgust': 3.15619363e-05,
       'fear': 0.000589638,
       'happiness': 0.06630674,
       'neutral': 0.00555004273,
       'sadness': 7.44669524e-06,
       'surprise': 0.9273863}}]



Возвращаемый объект JSON содержит ограничивающие прямоугольники вокруг лиц, которые были распознаны вместе с эмоциями. Каждой эмоции присваивается оценка от 0 до 1. Чем выше оценка, тем выразительнее эмоция. 

Ниже приведены строки кода для обнаружения эмоций на лицах на изображении с помощью библиотеки `matplotlib`. Чтобы избежать нагромождения, отображаются только три первые эмоции.


```python
%matplotlib inline
import matplotlib.pyplot as plt
from matplotlib.patches import Rectangle
from io import BytesIO
from PIL import Image

plt.figure(figsize=(5,5))

image  = Image.open(BytesIO(image_data))
ax     = plt.imshow(image, alpha=0.6)

for face in analysis:
    fr = face["faceRectangle"]
    em = face["scores"]
    origin = (fr["left"], fr["top"])
    p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
    ax.axes.add_patch(p)
    ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
    plt.text(origin[0], origin[1], ct, fontsize=20)    
_ = plt.axis("off")
```

С помощью показанной далее функции `annotate_image` можно наложить эмоции на изображение, указав путь к его файлу в файловой системе. Она основана на коде вызова API распознавания эмоций, показанном выше.


```python
def annotate_image(image_path):    
    image_data = open(image_path, "rb").read()
    headers  = {'Ocp-Apim-Subscription-Key': subscription_key, "Content-Type": "application/octet-stream" }
    response = requests.post(emotion_recognition_url, headers=headers, data=image_data)
    response.raise_for_status()
    analysis = response.json()

    plt.figure()

    image  = Image.open(image_path)
    ax     = plt.imshow(image, alpha=0.6)

    for face in analysis:
        fr = face["faceRectangle"]
        em = face["scores"]
        origin = (fr["left"], fr["top"])
        p = Rectangle(origin, fr["width"], fr["height"], fill=False, linewidth=2, color='b')
        ax.axes.add_patch(p)
        ct = "\n".join(["{0:<10s}{1:>.4f}".format(k,v) for k, v in sorted(list(em.items()),key=lambda r: r[1], reverse=True)][:3])
        plt.text(origin[0], origin[1], ct, fontsize=20, va="bottom")    
    _ = plt.axis("off")
```

Наконец, для файла изображения можно вызвать функцию `annotate_image`, как показано в следующей строке.


```python
annotate_image("images/emotion_2.jpg")
```
