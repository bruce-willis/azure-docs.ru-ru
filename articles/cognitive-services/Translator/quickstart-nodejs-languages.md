---
title: Получение списка поддерживаемых языков с помощью API перевода текстов и Node.js | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как получить список поддерживаемых языков перевода и транслитерации, результаты поиска по словарю и примеры с помощью API перевода текстов и Node.js в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: dd37aab3707c6f06b8cc2e942366e19746694252
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "43770870"
---
# <a name="quickstart-get-supported-languages-with-nodejs"></a>Краткое руководство по получению списка поддерживаемых языков с помощью Node.js

Из этого краткого руководства вы узнаете, как получить список поддерживаемых языков перевода и транслитерации, результаты поиска по словарю и примеры с помощью API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода вам потребуется [Node.js 6](https://nodejs.org/en/download/).

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="languages-request"></a>Запрос метода Languages

Приведенный ниже код возвращает список поддерживаемых языков перевода и транслитерации, а также результаты поиска по словарю и примеры использования с помощью метода [Languages](./reference/v3-0-languages.md).

1. Создайте проект Node.js в любом редакторе кода.
2. Добавьте указанный ниже код.
3. Замените значение `subscriptionKey` ключом доступа, допустимым для подписки.
4. Запустите программу.

```javascript
'use strict';

let fs = require ('fs');
let https = require ('https');

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'ENTER KEY HERE';

let host = 'api.cognitive.microsofttranslator.com';
let path = '/languages?api-version=3.0';

let output_path = 'output.txt';
let ws = fs.createWriteStream(output_path);

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        ws.write (json);
        ws.close ();
        console.log("File written.");

    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let GetLanguages = function () {
    let request_params = {
        method : 'GET',
        hostname : host,
        path : path,
        headers : {
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
        }
    };

    let req = https.request (request_params, response_handler);
    req.end ();
}

GetLanguages ();
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
> [Примеры для Node.js на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=javascript)
