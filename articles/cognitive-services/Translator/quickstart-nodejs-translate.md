---
title: Перевод текста с помощью API перевода текстов и Node.js | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Из этого краткого руководства вы узнаете, как перевести текст с одного языка на другой, используя API перевода текстов с Node.js в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 6d862125dc6c248afa8d8322ec1b008959abb7cd
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "43770845"
---
# <a name="quickstart-translate-text-with-nodejs"></a>Краткое руководство по переводу текста с использованием Node.js

Из этого краткого руководства вы узнаете, как перевести текст с одного языка на другой, используя API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода вам потребуется [Node.js 6](https://nodejs.org/en/download/).

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="translate-request"></a>Запрос метода Translate

В приведенном ниже коде исходный текст переводится с одного языка на другой с помощью метода [Translate](./reference/v3-0-translate.md).

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
let path = '/translate?api-version=3.0';

// Translate to German and Italian.
let params = '&to=de&to=it';

let text = 'Hello, world!';

let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let json = JSON.stringify(JSON.parse(body), null, 4);
        console.log(json);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};

let get_guid = function () {
  return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
    var r = Math.random() * 16 | 0, v = c == 'x' ? r : (r & 0x3 | 0x8);
    return v.toString(16);
  });
}

let Translate = function (content) {
    let request_params = {
        method : 'POST',
        hostname : host,
        path : path + params,
        headers : {
            'Content-Type' : 'application/json',
            'Ocp-Apim-Subscription-Key' : subscriptionKey,
            'X-ClientTraceId' : get_guid (),
        }
    };

    let req = https.request (request_params, response_handler);
    req.write (content);
    req.end ();
}

let content = JSON.stringify ([{'Text' : text}]);

Translate (content);
```

## <a name="translate-response"></a>Ответ метода Translate

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "translations": [
      {
        "text": "Hallo Welt!",
        "to": "de"
      },
      {
        "text": "Salve, mondo!",
        "to": "it"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, включая примеры для транслитерации и определения языка, а также с другими примерами проектов для API перевода текстов на сайте GitHub.

> [!div class="nextstepaction"]
> [Примеры для Node.js на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=javascript)
