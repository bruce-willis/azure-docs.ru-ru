---
title: Краткое руководство по API распознавания лиц для Ruby | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как обнаруживать лица на изображениях, используя API распознавания лиц с Ruby в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 05/30/2018
ms.author: nolachar
ms.openlocfilehash: b7fc23259475b7d165171ce42f1d0dff90d1069c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "43770552"
---
# <a name="quickstart-detect-faces-in-an-image-using-ruby"></a>Краткое руководство по обнаружению лиц на изображении с помощью Ruby

Из этого краткого руководства вы узнаете, как определять лица людей на изображениях с помощью API распознавания лиц.

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить пример, нужен ключ подписки. Вы можете получить ключи бесплатной пробной подписки на странице [Пробная версия Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api).

## <a name="face---detect-request"></a>Запрос на определение лица

Используйте метод [определения лица](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), чтобы распознать лица на изображении и вернуть их атрибуты, в том числе:

* Идентификатор лица: уникальный идентификатор, используемый в различных сценариях API распознавания лиц.
* Границы лица: отступ слева, сверху, а также ширина и высота лица, определяющие его место на изображении.
* Ориентиры: массив из 27-точечных ориентиров, указывающий на важные позиции компонентов лица.
* Атрибуты лица, в т. ч. возраст, пол, интенсивность улыбки, положение головы и наличие усов и бороды.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в редактор.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. При необходимости замените `uri` расположением, в котором вы получили ключи подписки.
1. При необходимости задайте `imageUri` для анализируемого изображения.
1. Сохраните файл с расширением `.rb`.
1. Откройте командную строку Ruby и запустите файл, например `ruby myfile.rb`.

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from  westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect')
uri.query = URI.encode_www_form({
    # Request parameters
    'returnFaceId' => 'true',
    'returnFaceLandmarks' => 'false',
    'returnFaceAttributes' => 'age,gender,headPose,smile,facialHair,glasses,' +
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

imageUri = "https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg"
request.body = "{\"url\": \"" + imageUri + "\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body
```

## <a name="face---detect-response"></a>Результат распознавания лица

В случае успешного выполнения возвращается ответ в формате JSON, например:

```json
[
  {
    "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "37c7c4bc-fda3-4d8d-94e8-b85b8deaf878",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами распознавания лиц, которые используются для обнаружения лиц, обозначения их границ и возврата таких атрибутов, как возраст и пол.

> [!div class="nextstepaction"]
> [API-интерфейсы распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
