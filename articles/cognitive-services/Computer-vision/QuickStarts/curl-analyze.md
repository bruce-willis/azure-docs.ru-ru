---
title: Краткое руководство по API компьютерного зрения для cURL. Анализ удаленного изображения | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как анализировать удаленные изображения с помощью API компьютерного зрения и cURL в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: c092432adbcba9cf7a131584131fd05991d1edef
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772277"
---
# <a name="quickstart-analyze-a-remote-image---rest-curl"></a>Краткое руководство по анализу удаленного изображения (REST, cURL)

Из этого краткого руководства вы узнаете, как анализировать удаленное изображение с помощью API компьютерного зрения, чтобы извлечь визуальные признаки. Анализ локального изображения описан в статье [Quickstart: Analyze a local image - REST, cURL - Computer Vision](curl-disk.md) (Краткое руководство по анализу локального изображения с помощью REST и cURL в Computer Vision).

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="analyze-a-remote-image"></a>Анализ удаленного изображения

С помощью [метода Analyze Image](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) можно извлечь визуальные признаки на основе содержимого изображения. Вы можете передать изображение или указать его URL-адрес, а также настроить следующие возвращаемые компоненты:

* подробный список тегов, связанных с содержимым изображения;
* описание содержимого изображения в виде полного предложения;
* координаты, пол и возраст любого лица, показанного на изображении;
* значение ImageType (картинки или линейный рисунок);
* преобладающий цвет, контрастный цвет, наличие или отсутствие цветности;
* категорию, определенную в этой [таксономии](../Category-Taxonomy.md);
* наличие на изображении содержимого для взрослых или содержимого сексуального характера.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в редактор.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. Вместо URL-адреса запроса (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) укажите расположение, в котором вы получили ключи подписки, если нужно.
1. Если нужно, измените анализируемое изображение (`{\"url\":\"...`).
1. При необходимости измените язык ответа (`language=en`).
1. Откройте окно командной строки на компьютере, где установлен язык cURL.
1. Вставьте код и выполните команду.

>[!NOTE]
>В вызове REST необходимо использовать то же расположение, что и для получения ключей подписки. Например, если вы получили ключи подписки в регионе "Западная часть США", в URL-адресе ниже замените westcentralus на westus.

## <a name="analyze-image-request"></a>Запрос на анализ изображения

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

## <a name="analyze-image-response"></a>Результат анализа изображения

В случае успешного выполнения возвращается ответ в формате JSON, например:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами компьютерного зрения, которые позволяют анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API-интерфейсами компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Сведения об API-интерфейсах компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
