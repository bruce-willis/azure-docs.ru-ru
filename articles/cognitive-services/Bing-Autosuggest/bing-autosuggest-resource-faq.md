---
title: Часто задаваемые вопросы об API автозаполнения Azure | Документация Майкрософт
description: Получите ответы на распространенные вопросы об API автозаполнения служб Azure Cognitive Services в Azure.
services: cognitive-services
author: HeidiSteen
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 07/26/2017
ms.author: heidist
ms.openlocfilehash: 00b91728bcfec52ff30697f080d5c2619bab79a8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382073"
---
# <a name="frequently-asked-questions-faq-about-autosuggest-api-cognitive-services"></a>Часто задаваемые вопросы об API автозаполнения (Cognitive Services)
 
 Найдите ответы на часто задаваемые вопросы о понятиях, коде и сценариях, связанных с API автозаполнения для служб Azure Cognitive Services.

### <a name="how-do-i-get-the-optional-client-headers-when-calling-the-bing-autosuggest-api-from-javascript"></a>Как получить необязательные заголовки клиента при вызове API автозаполнения Bing из JavaScript?

Приведенные ниже заголовки являются необязательными, но мы рекомендуем рассматривать их как обязательные. Эти заголовки помогают API автозаполнения Bing возвращать более точные результаты.

- X-Search-Location
- X-MSEdge-ClientID
- X-MSEdge-ClientIP

Тем не менее, при вызове API автозаполнения Bing из JavaScript встроенные средства обеспечения безопасности браузера могут блокировать доступ к значениям этих заголовков.

Для устранения этой проблемы запрос API автозаполнения Bing можно выполнить через прокси-сервер CORS. Ответ с такого прокси-сервера содержит заголовок `Access-Control-Expose-Headers`, который вносит заголовки ответов в список разрешений и делает их доступными для JavaScript.

Установить прокси-сервер CORS довольно просто. Это позволит нашему [учебному приложению](tutorials/autosuggest.md) иметь доступ к необязательным заголовкам клиента. Для начала, если у вас не установлен компонент Node.js, [установите его](https://nodejs.org/en/download/). В командной строке введите следующую команду:

    npm install -g cors-proxy-server

Затем в HTML-файле измените конечную точку API автозаполнения Bing на следующую:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

И наконец, запустите прокси-сервер CORS с помощью следующей команды:

    cors-proxy-server

Не закрывайте командное окно, пока используете учебное приложение. Закрытие этого окна приводит к остановке прокси-сервера. Теперь в развертываемом разделе заголовков HTTP под результатами поиска можно увидеть заголовок `X-MSEdge-ClientID` (среди прочих) и убедиться, что он одинаковый для всех запросов.

## <a name="next-steps"></a>Дополнительная информация

У вас есть вопрос об отсутствующей функции или компоненте? Вы можете отправить запрос или проголосовать за них на нашем [веб-сайте User Voice](https://cognitive.uservoice.com/).

## <a name="see-also"></a>См. также

- [Stack Overflow: Cognitive Services](http://stackoverflow.com/questions/tagged/microsoft-cognitive)
