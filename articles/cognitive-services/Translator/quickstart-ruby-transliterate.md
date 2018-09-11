---
title: Скрипт для преобразования текста с помощью API перевода текстов и Ruby | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как преобразовать текст на одном языке из одного скрипта в другой, используя API перевода текстов с Ruby в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/22/2018
ms.author: nolachar
ms.openlocfilehash: 56872ed6b6d7168fb1bf2c45de8104a592af34e3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "43771168"
---
# <a name="quickstart-transliterate-text-with-ruby"></a>Краткое руководство по транслитерации текста с помощью Ruby

Из этого краткого руководства вы узнаете, как с помощью API перевода текстов преобразовать текст на одном языке из одного набора символов в другой.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода потребуется [Ruby 2.4](https://www.ruby-lang.org/en/downloads/) или более поздней версии.

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Запрос на транслитерацию

Приведенный ниже код преобразует текст на одном языке из одного набора символов в другой с помощью метода [Transliterate](./reference/v3-0-transliterate.md).

1. Создайте новый проект Ruby в используемом редакторе.
2. Добавьте указанный ниже код.
3. Замените значение `key` ключом доступа, допустимым для подписки.
4. Запустите программу.

```ruby
require 'net/https'
require 'uri'
require 'cgi'
require 'json'
require 'securerandom'

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the key string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'https://api.cognitive.microsofttranslator.com'
path = '/transliterate?api-version=3.0'

# Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
params = "&language=ja&fromScript=jpan&toScript=latn";

uri = URI (host + path + params)

# Transliterate "good afternoon".
text = 'こんにちは'

content = '[{"Text" : "' + text + '"}]'

request = Net::HTTP::Post.new(uri)
request['Content-type'] = 'application/json'
request['Content-length'] = content.length
request['Ocp-Apim-Subscription-Key'] = key
request['X-ClientTraceId'] = SecureRandom.uuid
request.body = content

response = Net::HTTP.start(uri.host, uri.port, :use_ssl => uri.scheme == 'https') do |http|
    http.request (request)
end

result = response.body.force_encoding("utf-8")

json = JSON.pretty_generate(JSON.parse(result))
puts json
```

## <a name="transliterate-response"></a>Результат транслитерации

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, которые демонстрируют перевод и определение языка, а также с другими примерами проектов для API перевода текстов на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры на Ruby на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=ruby)
