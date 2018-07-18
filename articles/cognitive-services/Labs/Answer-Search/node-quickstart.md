---
title: Краткое руководство по службе поиска ответов в проектах с использованием Node — Microsoft Cognitive Services | Документация Майкрософт
description: Начало работы со службой поиска ответов в проектах, входящей в состав Microsoft Cognitive Services в Azure.
services: cognitive-services
author: mikedodaro
ms.service: cognitive-services
ms.technology: project-answer-search
ms.topic: article
ms.date: 04/13/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 36b2709d39230aae7929164ba4c9306f57043b43
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381740"
---
# <a name="project-answer-search-node-quickstart"></a>Краткое руководство по службе поиска ответов в проектах с использованием Node

С помощью приведенного здесь примера кода Node создается запрос на информацию о Йосемитском национальном парке.

## <a name="prerequisites"></a>предварительным требованиям

Получите ключ доступа для бесплатной пробной версии [Cognitive Services Labs](https://aka.ms/answersearchsubscription).

В этом примере используется Node версии 8.9.4.

## <a name="code-scenario"></a>Сценарий кода 

Приведенный ниже код возвращает ответы.
Он реализуется с помощью следующих действий:
1. Объявите переменные для указания конечной точки с помощью узла и пути.
2. Укажите URL-адрес запроса для предварительного просмотра и добавьте параметр запроса.  
3. Создайте функцию обработчика для ответа.
4. Определите функцию поиска, которая создает запрос и добавляет заголовок *Ocp-Apim-Subscription-Key*.
5. Запустите функцию поиска. 

Полный код этого примера выглядит так:

````
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'; 

let host = 'api.labs.cognitive.microsoft.com';
let path = '/answerSearch/v7.0/search';

let mkt = 'en-us';
let q = 'Yosemite National Park';

let params = '?q=' + encodeURI(q) + '&mkt=en-us';

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

````

## <a name="next-steps"></a>Дополнительная информация
- [Пример кода C#](c-sharp-quickstart.md)
- [Краткое руководство — Java](java-quickstart.md)
- [Краткое руководство — Python](python-quickstart.md)