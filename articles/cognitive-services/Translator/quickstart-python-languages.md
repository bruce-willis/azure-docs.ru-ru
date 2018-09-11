---
title: Получение списка поддерживаемых языков с помощью API перевода текстов и Python | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как получить список поддерживаемых языков перевода и транслитерации, результаты поиска по словарю и примеры с помощью API перевода текстов и Java в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 3ae3c6be814f79541e39eddd3be137b71cc1a494
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "43771005"
---
# <a name="quickstart-get-supported-languages-with-python"></a>Краткое руководство по получению списка поддерживаемых языков с помощью Python

Из этого краткого руководства вы узнаете, как получить список поддерживаемых языков перевода и транслитерации, результаты поиска по словарю и примеры с помощью API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода вам потребуется [Python 3.x](https://www.python.org/downloads/).

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Запрос метода Languages

Приведенный ниже код возвращает список поддерживаемых языков перевода и транслитерации, а также результаты поиска по словарю и примеры использования с помощью метода [Languages](./reference/v3-0-languages.md).

1. Создайте проект Python в любом удобном редакторе кода.
2. Добавьте указанный ниже код.
3. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
4. Запустите программу.

```python
# -*- coding: utf-8 -*-

import http.client, urllib.parse, json

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'api.cognitive.microsofttranslator.com'
path = '/languages?api-version=3.0'

output_path = 'output.txt'

def get_languages ():

    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}

    conn = http.client.HTTPSConnection(host)
    conn.request ("GET", path, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_languages ()

# Note: We convert result, which is JSON, to and from an object so we can pretty-print it.
# We want to avoid escaping any Unicode characters that result contains. See:
# https://stackoverflow.com/questions/18337407/saving-utf-8-texts-in-json-dumps-as-utf8-not-as-u-escape-sequence
json = json.dumps(json.loads(result), indent=4, ensure_ascii=False).encode('utf-8')

f = open(output_path, 'wb')
f.write (json)
f.close
```

## <a name="languages-response"></a>Ответ метода Languages

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
  },
  "transliteration": {
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "scripts": [
        {
          "code": "Arab",
          "name": "Arabic",
          "nativeName": "العربية",
          "dir": "rtl",
          "toScripts": [
            {
              "code": "Latn",
              "name": "Latin",
              "nativeName": "اللاتينية",
              "dir": "ltr"
            }
          ]
        },
        {
          "code": "Latn",
          "name": "Latin",
          "nativeName": "اللاتينية",
          "dir": "ltr",
          "toScripts": [
            {
              "code": "Arab",
              "name": "Arabic",
              "nativeName": "العربية",
              "dir": "rtl"
            }
          ]
        }
      ]
    },
...
  },
  "dictionary": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl",
      "translations": [
        {
          "name": "English",
          "nativeName": "English",
          "dir": "ltr",
          "code": "en"
        }
      ]
    },
...
  }
}
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, включая перевод и транслитерацию, а также с другими примерами проектов перевода текстов в GitHub.

> [!div class="nextstepaction"]
> [Ознакомиться с примерами на Python на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=python)
