---
title: Получение длины предложения с помощью API перевода текстов и Node.js | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: В этом кратком руководстве объясняется, как определить длину предложений в тексте, используя API перевода текстов и Node.js в Cognitive Services.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/21/2018
ms.author: nolachar
ms.openlocfilehash: 42fe27df2f0d6aacecfe2b9b01ad0061c2fea646
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2018
ms.locfileid: "43770957"
---
# <a name="quickstart-get-sentence-lengths-with-nodejs"></a>Краткое руководство. Определение длины предложения с помощью Node.js

В этом кратком руководстве объясняется, как определять длину предложений в тексте с помощью API перевода текстов.

## <a name="prerequisites"></a>Предварительные требования

Для выполнения этого кода вам потребуется [Node.js 6](https://nodejs.org/en/download/).

Чтобы использовать API перевода текстов, вам также потребуется ключ подписки. Сведения об этом см. в статье [Регистрация для использования API перевода текстов](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>Запрос метода BreakSentence

Приведенный ниже код позволяет разбить исходный текст на предложения с помощью метода [BreakSentence](./reference/v3-0-break-sentence.md).

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
let path = '/breaksentence?api-version=3.0';

let params = '';

let text = 'How are you? I am fine. What did you do today?';

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

let BreakSentences = function (content) {
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

BreakSentences (content);
```

## <a name="breaksentence-response"></a>Ответ метода BreakSentence

В случае успешного выполнения ответ возвращается в формате JSON, как показано в примере ниже:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>Дополнительная информация

Ознакомьтесь с примерами кода из этого и других кратких руководств, включая перевод и транслитерацию, а также с другими примерами проектов перевода текстов в GitHub.

> [!div class="nextstepaction"]
> [Примеры для Node.js на сайте GitHub](https://aka.ms/TranslatorGitHub?type=&language=javascript)
