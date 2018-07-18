---
title: Часто задаваемые вопросы об API Bing для проверки орфографии в Azure Cognitive Services | Документация Майкрософт
description: Ответы на распространенные вопросы об API Bing для проверки орфографии в Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: conceptual
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 87b1f3ed3e0aaa9f3c3c804dc9eac3ee60b4a565
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382389"
---
# <a name="frequently-asked-questions-about-the-bing-spell-check-api"></a>Часто задаваемые вопросы об API Bing для проверки орфографии

 Найдите ответы на часто задаваемые вопросы о понятиях, коде и сценариях, связанных с API Bing для проверки орфографии для служб Microsoft Cognitive Services в Azure.

## <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-spell-check-api-from-javascript"></a>Как получить необязательные заголовки клиента при вызове API Bing для проверки орфографии из JavaScript?

Приведенные ниже заголовки являются необязательными, но мы рекомендуем рассматривать их как обязательные. Эти заголовки помогают API Bing для проверки орфографии возвращать более точные результаты.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Тем не менее при вызове API Bing для проверки орфографии из JavaScript встроенные средства обеспечения безопасности браузера могут блокировать доступ к значениям этих заголовков.

Для решения этой проблемы запрос API Bing для проверки орфографии можно выполнить через прокси-сервер CORS. Ответ с такого прокси-сервера содержит заголовок `Access-Control-Expose-Headers`, который вносит заголовки ответов в список разрешений и делает их доступными для JavaScript.

Установить прокси-сервер CORS довольно просто. Это позволит [учебному приложению](tutorials/spellcheck.md) получить доступ к необязательным заголовкам клиента. Для начала [установите платформу Node.js](https://nodejs.org/en/download/), если она еще не установлена. В командной строке введите следующую команду:

    npm install -g cors-proxy-server

Затем в HTML-файле измените конечную точку API Bing для проверки орфографии на следующую:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/

И наконец, запустите прокси-сервер CORS с помощью следующей команды:

    cors-proxy-server

Не закрывайте командное окно, пока используете учебное приложение. Это приведет к остановке прокси-сервера. Теперь в развертываемом разделе заголовков HTTP под результатами поиска можно увидеть заголовок `X-MSEdge-ClientID` (среди прочих) и убедиться, что он одинаковый для всех запросов.

## <a name="next-steps"></a>Дополнительная информация

У вас есть вопрос об отсутствующей функции или компоненте? Вы можете отправить запрос или проголосовать за него на [веб-сайте User Voice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>См. также

 [StackOverflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
