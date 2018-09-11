---
title: Краткое руководство по API распознавания лиц для Node.js | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как определять лица на изображениях с помощью API распознавания лиц для Node.js в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 06/08/2018
ms.author: nolachar
ms.openlocfilehash: 15e6db9c9d92e60f18bf2d7708403fd8b0b7a990
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/11/2018
ms.locfileid: "43770580"
---
# <a name="quickstart-detect-faces-in-an-image-using-nodejs"></a>Краткое руководство. Определение лиц на изображении с помощью Node.js

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
1. При необходимости замените `uriBase` расположением, в котором вы получили ключи подписки.
1. При необходимости задайте `imageUri` для анализируемого изображения.
1. Сохраните файл с расширением `.js`.
1. Откройте командную строку Node.js и запустите файл, например: `node myfile.js`.

```nodejs
'use strict';

const request = require('request');

// Replace <Subscription Key> with your valid subscription key.
const subscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to get your
// subscription keys. For example, if you got your subscription keys from
// westus, replace "westcentralus" in the URL below with "westus".
const uriBase = 'https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/3/37/Dagestani_man_and_woman.jpg';

// Request parameters.
const params = {
    'returnFaceId': 'true',
    'returnFaceLandmarks': 'false',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,' +
        'emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
  let jsonResponse = JSON.stringify(JSON.parse(body), null, '  ');
  console.log('JSON Response\n');
  console.log(jsonResponse);
});
```

## <a name="face---detect-response"></a>Результат распознавания лица

В случае успешного выполнения возвращается ответ в формате JSON, например:

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
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
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
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
> [API распознавания лиц](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
