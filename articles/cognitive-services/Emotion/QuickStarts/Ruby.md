---
title: Краткое руководство по API распознавания эмоций с использованием Ruby | Документация Майкрософт
description: Получение информации и примеров кода, которые помогут вам приступить к работе с API распознавания эмоций с использованием Ruby в Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 733127bb3656d86a7f3f57cd26c72909900f4899
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37021055"
---
# <a name="emotion-api-ruby-quick-start"></a>Краткое руководство по API распознавания эмоций с использованием Ruby

> [!IMPORTANT]
> Срок действия предварительной версии API для видео истекает 30 октября 2017 г. Оцените новую [предварительную версию API индексатора видео](https://azure.microsoft.com/services/cognitive-services/video-indexer/), которая позволяет легко извлекать аналитические сведения из видео и оптимизировать возможности обнаружения содержимого (например, результаты поиска) по обнаружению произнесенных слов, лиц, символов и эмоций. [Узнайте больше](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

В этой статье содержатся сведения и примеры кода, которые помогут вам быстро приступить к работе с помощью метода [API распознавания эмоций](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) с использованием Ruby для распознавания эмоций, выраженных одним или несколькими людьми на изображении.

## <a name="prerequisite"></a>Предварительные требования
* Получите бесплатный ключ подписки [здесь](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-ruby-example-request"></a>Пример запроса Ruby для распознавания эмоций

Измените URL-адрес REST и укажите расположение, в котором получены ключи подписки. Замените значение Ocp-Apim-Subscription-Key на допустимый ключ подписки и для `body` переменной добавьте URL-адрес фотографии.

```ruby
require 'net/http'

# NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
#   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
#   URL below with "westcentralus".
uri = URI('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize')
uri.query = URI.encode_www_form({
})

request = Net::HTTP::Post.new(uri.request_uri)
# Request headers
request['Content-Type'] = 'application/json'
# NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '13hc77781f7e4b19b5fcdd72a8df7156'
# Request body
request.body = "{\"url\":\"http://example.com/1.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts response.body

```

## <a name="recognize-emotions-sample-response"></a>Пример ответа на распознавание эмоций
При успешном вызове возвращается массив записей лиц и связанные с ними оценки эмоций, отсортированные по убыванию в зависимости от размера прямоугольника с лицом. Пустой ответ указывает на то, что лица не обнаружены. Запись об эмоции содержит следующие поля:
* faceRectangle — расположение прямоугольника с лицом на изображении;
* scores — результаты оценки эмоций для каждого лица на изображении. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
