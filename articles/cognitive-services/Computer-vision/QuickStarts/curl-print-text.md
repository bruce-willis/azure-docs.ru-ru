---
title: Краткое руководство по API компьютерного зрения для cURL. Распознавание текста | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как извлекать печатный текст из изображения с помощью API компьютерного зрения и cURL в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 3a9ebafa5e367c4fcd7dce63c54c9fdc14eb3812
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771997"
---
# <a name="quickstart-extract-printed-text-ocr---rest-curl"></a>Краткое руководство по извлечению печатного текста (OCR) — REST, cURL

Из этого краткого руководства вы узнаете, как извлекать печатный текст из изображения (распознавать текст) с помощью API компьютерного зрения.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="ocr-request"></a>Запрос на распознавание текста (OCR)

С помощью [метода OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) можно определить печатный текст на изображении и извлечь распознанные символы в поток символов, пригодный для машинной обработки.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в редактор.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. Вместо URL-адреса запроса (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) укажите расположение, в котором вы получили ключи подписки, если нужно.
1. Если нужно, измените анализируемое изображение (`{\"url\":\"...`).
1. Откройте окно командной строки на компьютере, где установлен язык cURL.
1. Вставьте код и выполните команду.

>[!NOTE]
>В вызове REST необходимо использовать то же расположение, что и для получения ключей подписки. Например, если вы получили ключи подписки в регионе "Западная часть США", в URL-адресе ниже замените westcentralus на westus.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/ocr?language=unk&detectOrientation=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
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
