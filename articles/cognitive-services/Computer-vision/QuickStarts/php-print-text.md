---
title: Краткое руководство по API компьютерного зрения для PHP. Распознавание текста | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как извлекать печатный текст из изображения с помощью API компьютерного зрения и PHP в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 85df021357c76330be21ceff77d79491edcc23b0
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771983"
---
# <a name="quickstart-extract-printed-text-ocr---rest-php"></a>Краткое руководство по извлечению печатного текста (OCR) — REST, PHP

Из этого краткого руководства вы узнаете, как извлекать печатный текст из изображения (распознавать текст) с помощью API компьютерного зрения.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="ocr-request"></a>Запрос на распознавание текста (OCR)

С помощью [метода OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) можно определить печатный текст на изображении и извлечь распознанные символы в поток символов, пригодный для машинной обработки.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в редактор.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. Вместо `uriBase` укажите расположение, в котором вы получили ключи подписки, если нужно.
1. При необходимости укажите в параметре `imageUrl` изображение для анализа.
1. Сохраните файл с расширением `.php`.
1. Откройте файл в окне браузера с поддержкой PHP.

В этом примере используется пакет [HTTP_Request2](http://pear.php.net/package/HTTP_Request2) для PHP5.

```php
<?php
<html>
<head>
    <title>OCR Sample</title>
</head>
<body>
<?php
// Replace <Subscription Key> with a valid subscription key.
$ocpApimSubscriptionKey = '<Subscription Key>';

// You must use the same location in your REST call as you used to obtain
// your subscription keys. For example, if you obtained your subscription keys
// from westus, replace "westcentralus" in the URL below with "westus".
$uriBase = 'https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/';

$imageUrl = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/' .
    'Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png';

require_once 'HTTP/Request2.php';

$request = new Http_Request2($uriBase . 'ocr');
$url = $request->getUrl();

$headers = array(
    // Request headers
    'Content-Type' => 'application/json',
    'Ocp-Apim-Subscription-Key' => $ocpApimSubscriptionKey
);
$request->setHeader($headers);

$parameters = array(
    // Request parameters
    'language' => 'unk',
    'detectOrientation' => 'true'
);
$url->setQueryVariables($parameters);

$request->setMethod(HTTP_Request2::METHOD_POST);

// Request body parameters
$body = json_encode(array('url' => $imageUrl));

// Request body
$request->setBody($body);

try
{
    $response = $request->send();
    echo "<pre>" .
        json_encode(json_decode($response->getBody()), JSON_PRETTY_PRINT) . "</pre>";
}
catch (HttpException $ex)
{
    echo "<pre>" . $ex . "</pre>";
}
?>
</body>
</html>
```

## <a name="ocr-response"></a>Результат распознавания текста

Если операция выполнена успешно, результат OCR будет содержать текст и ограничивающие прямоугольники для областей, строк и слов, например:

```json
{
    "language": "en",
    "orientation": "Up",
    "textAngle": 0,
    "regions": [
        {
            "boundingBox": "21,16,304,451",
            "lines": [
                {
                    "boundingBox": "28,16,288,41",
                    "words": [
                        {
                            "boundingBox": "28,16,288,41",
                            "text": "NOTHING"
                        }
                    ]
                },
                {
                    "boundingBox": "27,66,283,52",
                    "words": [
                        {
                            "boundingBox": "27,66,283,52",
                            "text": "EXISTS"
                        }
                    ]
                },
                {
                    "boundingBox": "27,128,292,49",
                    "words": [
                        {
                            "boundingBox": "27,128,292,49",
                            "text": "EXCEPT"
                        }
                    ]
                },
                {
                    "boundingBox": "24,188,292,54",
                    "words": [
                        {
                            "boundingBox": "24,188,292,54",
                            "text": "ATOMS"
                        }
                    ]
                },
                {
                    "boundingBox": "22,253,297,32",
                    "words": [
                        {
                            "boundingBox": "22,253,105,32",
                            "text": "AND"
                        },
                        {
                            "boundingBox": "144,253,175,32",
                            "text": "EMPTY"
                        }
                    ]
                },
                {
                    "boundingBox": "21,298,304,60",
                    "words": [
                        {
                            "boundingBox": "21,298,304,60",
                            "text": "SPACE."
                        }
                    ]
                },
                {
                    "boundingBox": "26,387,294,37",
                    "words": [
                        {
                            "boundingBox": "26,387,210,37",
                            "text": "Everything"
                        },
                        {
                            "boundingBox": "249,389,71,27",
                            "text": "else"
                        }
                    ]
                },
                {
                    "boundingBox": "127,431,198,36",
                    "words": [
                        {
                            "boundingBox": "127,431,31,29",
                            "text": "is"
                        },
                        {
                            "boundingBox": "172,431,153,36",
                            "text": "opinion."
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами компьютерного зрения, которые позволяют анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API-интерфейсами компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Сведения об API-интерфейсах компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
