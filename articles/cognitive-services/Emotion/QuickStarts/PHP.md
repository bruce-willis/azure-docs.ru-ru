---
title: Краткое руководство по API распознавания эмоций с использованием PHP | Документация Майкрософт
description: Информация и примеры кода, которые помогут вам приступить к работе с API распознавания эмоций с использованием PHP в Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 987d5a3eedaa17f1127be34e5f90ec2456fab99b
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37019403"
---
# <a name="emotion-api-php-quick-start"></a>Краткое руководство по API распознавания эмоций с использованием PHP

> [!IMPORTANT]
> Срок действия предварительной версии API для видео истекает 30 октября 2017 г. Оцените новую [предварительную версию API индексатора видео](https://azure.microsoft.com/services/cognitive-services/video-indexer/), которая позволяет легко извлекать аналитические сведения из видео и оптимизировать возможности обнаружения содержимого (например, результаты поиска) по обнаружению произнесенных слов, лиц, символов и эмоций. [Узнайте больше](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

В этой статье содержатся сведения и примеры кода, которые помогут вам быстро приступить к распознаванию эмоций одного человека или нескольких людей на изображении с помощью PHP и [метода API распознавания эмоций](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa). 

## <a name="prerequisite"></a>Предварительные требования
* Получите бесплатный ключ подписки [здесь](https://azure.microsoft.com/try/cognitive-services/).

## <a name="recognize-emotions-php-example-request"></a>Пример запроса PHP для распознавания эмоций

Измените URL-адрес REST, указав расположение, из которого получены ключи подписки. Замените текст запроса на URL-адрес тестируемого изображения, а значение Ocp-Apim-Subscription-Key — на действующий ключ подписки.

```php
<?php
// This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
require_once 'HTTP/Request2.php';

// NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
//   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
//   URL below with "westcentralus".
$request = new Http_Request2('https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',

    // NOTE: Replace the "Ocp-Apim-Subscription-Key" value with a valid subscription key.
    'Ocp-Apim-Subscription-Key' => '13hc77781f7e4b19b5fcdd72a8df7156',
);

$request->setHeader($headers);

$parameters = array(
    // Request parameters
);

$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body
$request->setBody('{"url": "http://www.example.com/images/image.jpg"}');

try
{
    $response = $request->send();
    echo $response->getBody();
}
catch (HttpException $ex)
{
    echo $ex;
}

?>
```

## <a name="recognize-emotions-sample-response"></a>Пример ответа API распознавания эмоций
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

