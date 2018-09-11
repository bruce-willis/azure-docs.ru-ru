---
title: Краткое руководство по API компьютерного зрения для Ruby | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как создать эскиз изображения, используя API компьютерного зрения с Ruby в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: v-deken
ms.openlocfilehash: a5f6c345b3e1f5e4dab923d43dd239344c66aab1
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43772146"
---
# <a name="quickstart-generate-a-thumbnail---rest-ruby"></a>Краткое руководство по созданию эскиза (REST, Ruby)

В этом кратком руководстве описано, как создать эскиз изображения с помощью API компьютерного зрения.

## <a name="prerequisites"></a>Предварительные требования

Чтобы использовать API компьютерного зрения, требуется ключ подписки. Его получение описано в статье [Obtaining Subscription Keys](../Vision-API-How-to-Topics/HowToSubscribe.md) (Получение ключей подписки).

## <a name="get-thumbnail-request"></a>Запрос Get Thumbnail

Метод [Get Thumbnail](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) позволяет создать эскиз изображения. Вы можете указать нужную высоту и ширину. При этом пропорции могут отличаться от пропорций исходного изображения. API компьютерного зрения использует интеллектуальную обрезку для идентификации интересующей области и создания координат обрезки для этой области.

Чтобы выполнить наш пример, сделайте следующее:

1. Скопируйте приведенный ниже код в редактор.
1. Замените `<Subscription Key>` действительным ключом подписки.
1. При необходимости замените `uri` расположением, в котором вы получили ключи подписки.
1. Если нужно, измените анализируемое изображение (`{\"url\":\"...`).
1. Сохраните файл с расширением `.rb`.
1. Откройте командную строку Ruby и запустите файл, например `ruby myfile.rb`.

```ruby
require 'net/http'

# You must use the same location in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from westus,
# replace "westcentralus" in the URL below with "westus".
uri = URI('https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/generateThumbnail')
uri.query = URI.encode_www_form({
    # Request parameters
    'width' => '100',
    'height' => '100',
    'smartCropping' => 'true'
})

request = Net::HTTP::Post.new(uri.request_uri)

# Request headers
# Replace <Subscription Key> with your valid subscription key.
request['Ocp-Apim-Subscription-Key'] = '<Subscription Key>'
request['Content-Type'] = 'application/json'

request.body =
    "{\"url\": \"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/" +
    "Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

#puts response.body
```

## <a name="get-thumbnail-response"></a>Ответ Get Thumbnail

В случае успешного выполнения ответ будет содержать двоичный файл эскиза изображения. Если запрос завершается сбоем, ответ будет содержать код ошибки и сообщение с описанием проблемы.

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с API-интерфейсами компьютерного зрения, которые позволяют анализировать изображения, обнаруживать знаменитостей и достопримечательности, создавать эскизы, извлекать печатный и рукописный текст. Для быстрых экспериментов с API-интерфейсами компьютерного зрения можно использовать [открытую консоль тестирования API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Сведения об API-интерфейсах компьютерного зрения](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
