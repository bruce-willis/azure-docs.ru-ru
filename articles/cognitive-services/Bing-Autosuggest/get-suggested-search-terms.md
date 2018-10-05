---
title: Что такое API автозаполнения Bing? | Документация Майкрософт
description: Узнайте, как использовать API автозаполнения Bing.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 6F4AFEDA-71A7-48C1-B3E2-D0D430428CDC
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: 76e509289f495e09c367af926fd26e0581b6e7c6
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091112"
---
# <a name="what-is-bing-autosuggest"></a>Что такое API автозаполнения Bing?

Отправляя запросы на любой из API-интерфейсов поиска Bing, можно использовать API автозаполнения Bing для улучшения работы поля поиска. API автозаполнения Bing возвращает список предлагаемых запросов на основе частичной строки запроса, которую пользователь вводит в поле поиска. Варианты поиска отображаются в раскрывающимся списке поля поиска. Предложенные термины основаны на предлагаемых запросах, которые другие пользователи использовали для поиска, и намерениях пользователя.

Обычно этот API вызывается каждый раз, когда пользователь вводит новый символ в поле поиска. Полнота строки запроса влияет на релевантность предложений, возвращаемых API-интерфейсом. Чем ближе строка запроса к завершению, тем точнее будет список предложений для этого запроса. Например, возвращаемые API-интерфейсом предложения по строке *s* будут, скорее всего, менее значимыми, чем результаты для строки *sailing dinghies*.

## <a name="getting-suggested-search-terms"></a>Получение предлагаемых условий поиска

В следующем примере показано, как отправить запрос, который возвращает предлагаемые строки запроса для слова *sail*. Не забудьте закодировать в URL-адресе частичный запрос условия пользователя, когда устанавливаете параметр запроса [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query). Например, если пользователь вводит *sailing les*, установите параметр `q` в `sailing+les` или `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Этот ответ содержит список объектов [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction) с предлагаемыми условиями запроса.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

Каждое из предложений содержит поля `displayText`, `query` и `url`. Поле `displayText` содержит предлагаемые запросы, которые вы можете поместить в раскрывающийся список для поля поиска. Должны отображаться все предоставленные предложения и строго в указанном порядке.

На изображении ниже указан пример раскрывающегося поля поиска с предлагаемыми условиями запроса.

![Автозаполнение раскрывающегося списка поля поиска](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Если пользователь выбирает из раскрывающегося списка предложенный запрос, используйте условие запроса из поля `query`, чтобы вызвать раздел [Документация по API Bing для поиска в Интернете](../bing-web-search/search-the-web.md) и отобразить полученные результаты. Или вы можете использовать URL-адрес из поля `url`, чтобы переадресовать пользователя на страницу результатов поиска Bing.

## <a name="throttling-requests"></a>Запросы на регулирование

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как быстро создать первый запрос, см. в статье [Краткое руководство по API автозаполнения Bing с использованием C#](quickstarts/csharp.md).

Ознакомьтесь с руководством [API автозаполнения Bing версии 7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). Руководство содержит список конечных точек, заголовков и параметров запроса, которые будут использоваться для запроса предложенных терминов запроса, а также определения объектов ответа.

Узнайте, как выполнять поиск в Интернете с помощью статьи [Документация по API Bing для поиска в Интернете](../bing-web-search/search-the-web.md).

Обязательно прочтите [эту статью](./useanddisplayrequirements.md), чтобы не нарушать какие-либо правила использования результатов поиска.