---
title: Краткое руководство по API компьютерного зрения для cURL. Анализ локального изображения | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как, используя API компьютерного зрения, анализировать локальное изображение с помощью cURL в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 93ca3ea6eee3743dfd0c25c9514375ae63a531ee
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772412"
---
# <a name="quickstart-analyze-a-local-image---rest-curl"></a>Краткое руководство. Анализ локального изображения — REST, cURL

Из этого краткого руководства вы узнаете, как анализировать локальное изображение с помощью API компьютерного зрения, чтобы извлечь визуальные признаки. Чтобы проанализировать удаленное изображение, см. статью [Analyze a remote image with cURL](curl-analyze.md) (Анализ удаленного изображения с помощью cURL).

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="analyze-a-local-image"></a>Анализ локального изображения

Пример аналогичен [анализу удаленного изображения с помощью cURL](curl-analyze.md), за исключением того, что анализируемое изображение считывается с локального диска. Требуются следующие изменения:

- Измените тип содержимого на `"Content-Type: application/octet-stream"`.
- Измените параметр `-d` на `--data-binary`.
- Укажите изображение для анализа, используя следующий синтаксис: `@C:/Pictures/ImageToAnalyze.jpg`.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в редактор.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. Вместо URL-адреса запроса (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) укажите расположение, в котором вы получили ключи подписки, если нужно.
1. Замените `<Image To Analyze>` на локальное изображение для анализа.
1. При необходимости измените язык ответа (`language=en`).
1. Откройте окно командной строки на компьютере, где установлен язык cURL.
1. Вставьте код и выполните команду.

>[!NOTE]
>В вызове REST необходимо использовать то же расположение, что и для получения ключей подписки. Например, если вы получили ключи подписки в регионе "Западная часть США", в URL-адресе ниже замените westcentralus на westus.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -H "Content-Type: application/octet-stream" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/analyze?visualFeatures=Categories,Description&details=Landmarks&language=en" --data-binary <Image To Analyze>
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
