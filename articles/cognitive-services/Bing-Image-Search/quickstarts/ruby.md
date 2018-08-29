---
title: Краткое руководство. Отправка поисковых запросов с помощью REST API для API Bing для поиска изображений и Ruby
description: Из этого краткого руководства можно узнать, как с помощью Ruby отправлять поисковые запросы в API Bing для поиска, чтобы получить список подходящих изображений.
services: cognitive-services
documentationcenter: ''
author: v-jerkin
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 9/21/2017
ms.author: v-jerkin
ms.openlocfilehash: bbe154f22557fb357edfb6b981eb1024f0a81d38
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "41937531"
---
# <a name="quickstart-send-search-queries-using-the-rest-api-and-ruby"></a>Краткое руководство. Отправка поисковых запросов с помощью REST API и Ruby

API Bing для поиска изображений предоставляет примерно такие же возможности, как просмотр сведений об изображении на Bing.com/images. API позволяет отправлять поисковый запрос пользователя в Bing и получать в ответ список соответствующих изображений.

В этой статье используется простое консольное приложение, которое выполняет запрос API Bing для поиска изображений и отображает возвращенные необработанные результаты поиска в формате JSON. Хотя это приложение написано на Ruby, API является веб-службой RESTful, совместимой с любым языком программирования, который может выполнять HTTP-запросы и анализировать JSON. 

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода потребуется [Ruby 2.4 или более поздней версии](https://www.ruby-lang.org/en/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="running-the-application"></a>Запуск приложения

Чтобы запустить это приложение, сделайте следующее.

1. Создайте проект Ruby в используемой вами интегрированной среде разработки или редакторе.
2. Добавьте в него предоставленный код.
3. Замените значение `accessKey` ключом доступа, допустимым для подписки.
4. Запустите программу.

```ruby
require 'net/https'
require 'uri'
require 'json'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the accessKey string value with your valid access key.
accessKey = "enter key here"

# Verify the endpoint URI.  At this writing, only one endpoint is used for Bing
# search APIs.  In the future, regional endpoints may be available.  If you
# encounter unexpected authorization errors, double-check this value against
# the endpoint for your Bing Search instance in your Azure dashboard.

uri  = "https://api.cognitive.microsoft.com"
path = "/bing/v7.0/images/search"

term = "puppies"

if accessKey.length != 32 then
    puts "Invalid Bing Search API subscription key!"
    puts "Please paste yours into the source code."
    abort
end

uri = URI(uri + path + "?q=" + URI.escape(term))

puts "Searching images for: " + term

request = Net::HTTP::Get.new(uri)
request['Ocp-Apim-Subscription-Key'] = accessKey

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request(request)
end

puts "\nRelevant Headers:\n\n"
response.each_header do |key, value|
    # header names are coerced to lowercase
    if key.start_with?("bingapis-") or key.start_with?("x-msedge-") then
        puts key + ": " + value
    end
end

puts "\nJSON Response:\n\n"
puts JSON::pretty_generate(JSON(response.body))
```

## <a name="json-response"></a>Ответ JSON

Пример ответа приведен ниже. Для ограничения длины JSON показан только один результат, а остальные части ответа усечены. 

```json
{
  "_type": "Images",
  "instrumentation": {},
  "readLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=puppies",
  "webSearchUrl": "https://www.bing.com/images/search?q=puppies&FORM=OIIARP",
  "totalEstimatedMatches": 955,
  "nextOffset": 1,
  "value": [
    {
      "webSearchUrl": "https://www.bing.com/images/search?view=detailv...",
      "name": "So cute - Puppies Wallpaper",
      "thumbnailUrl": "https://tse3.mm.bing.net/th?id=OIP.jHrihoDNkXGS1t...",
      "datePublished": "2014-02-01T21:55:00.0000000Z",
      "contentUrl": "http://images4.contoso.com/image/photos/14700000/So-cute-puppies...",
      "hostPageUrl": "http://www.contoso.com/clubs/puppies/images/14749028/...",
      "contentSize": "394455 B",
      "encodingFormat": "jpeg",
      "hostPageDisplayUrl": "www.contoso.com/clubs/puppies/images/14749...",
      "width": 1600,
      "height": 1200,
      "thumbnail": {
        "width": 300,
        "height": 225
      },
      "imageInsightsToken": "ccid_jHrihoDN*mid_F68CC526226E163FD1EA659747AD...",
      "insightsMetadata": {
        "recipeSourcesCount": 0
      },
      "imageId": "F68CC526226E163FD1EA659747ADCB8F9FA36",
      "accentColor": "8D613E"
    }
  ],
  "queryExpansions": [
    {
      "text": "Shih Tzu Puppies",
      "displayText": "Shih Tzu",
      "webSearchUrl": "https://www.bing.com/images/search?q=Shih+Tzu+Puppies...",
      "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=Shih...",
      "thumbnail": {
        "thumbnailUrl": "https://tse2.mm.bing.net/th?q=Shih+Tzu+Puppies&pid=Api..."
      }
    }
  ],
  "pivotSuggestions": [
    {
      "pivot": "puppies",
      "suggestions": [
        {
          "text": "Dog",
          "displayText": "Dog",
          "webSearchUrl": "https://www.bing.com/images/search?q=Dog&tq=%7b%22pq%...",
          "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/search?q=Dog...",
          "thumbnail": {
            "thumbnailUrl": "https://tse1.mm.bing.net/th?q=Dog&pid=Api&mkt=en-US..."
          }
        }
      ]
    }
  ],
  "similarTerms": [
    {
      "text": "cute",
      "displayText": "cute",
      "webSearchUrl": "https://www.bing.com/images/search?q=cute&FORM=...",
      "thumbnail": {
        "url": "https://tse2.mm.bing.net/th?q=cute&pid=Api&mkt=en-US..."
      }
    }
  ],
  "relatedSearches": [
    {
      "text": "Cute Puppies",
      "displayText": "Cute Puppies",
      "webSearchUrl": "https://www.bing.com/images/search?q=Cute+Puppies",
      "searchLink": "https://api.cognitive.microsoft.com/api/v7/images/sear...",
      "thumbnail": {
        "thumbnailUrl": "https://tse4.mm.bing.net/th?q=Cute+Puppies&pid=..."
      }
    }
  ]
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по одностраничным приложениям для API Bing для поиска изображений](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>См. также 

[Обзор API Bing для поиска изображений](../overview.md)  
[Пробная версия](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
[Получение ключа доступа для бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
[Справочник по API Bing для поиска изображений](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
