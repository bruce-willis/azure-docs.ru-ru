---
title: Краткое руководство по API компьютерного зрения для cURL. Создание эскиза | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как создать эскиз изображения, используя API компьютерного зрения с cURL в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: 4056f05f3f4bf97761f683b5f3a9053666d4ea26
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43771941"
---
# <a name="quickstart-generate-a-thumbnail---rest-curl"></a>Краткое руководство по созданию эскиза (REST, cURL)

В этом кратком руководстве описано, как создать эскиз изображения с помощью API компьютерного зрения.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="get-thumbnail-request"></a>Запрос Get Thumbnail

Метод [Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) позволяет создать эскиз изображения. Вы можете указать нужную высоту и ширину. При этом пропорции могут отличаться от пропорций исходного изображения. API компьютерного зрения использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки для этой области.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в редактор.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. Замените `<File>` на путь и имя файла для сохранения эскиза.
1. Вместо URL-адреса запроса (`https://westcentralus.api.cognitive.microsoft.com/vision/v2.0`) укажите расположение, в котором вы получили ключи подписки, если нужно.
1. Если нужно, измените анализируемое изображение (`{\"url\":\"...`).
1. Откройте окно командной строки на компьютере, где установлен язык cURL.
1. Вставьте код и выполните команду.

>[!NOTE]
>В вызове REST необходимо использовать то же расположение, что и для получения ключей подписки. Например, если вы получили ключи подписки в регионе "Западная часть США", в URL-адресе ниже замените westcentralus на westus.

```json
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" -o <File> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
```

## <a name="get-thumbnail-response"></a>Ответ Get Thumbnail

При успешном получении ответа файл эскиза изображения будет записан в `<File>`. Если запрос завершается сбоем, ответ будет содержать код ошибки и сообщение с описанием проблемы.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами компьютерного зрения, которые позволяют анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API-интерфейсами компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Сведения об API-интерфейсах компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
