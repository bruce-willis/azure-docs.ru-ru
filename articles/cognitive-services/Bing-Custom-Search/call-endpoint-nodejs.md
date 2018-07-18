---
title: Вызов конечной точки службы пользовательского поиска Bing с помощью Node.js (Microsoft Cognitive Services)
description: В этом кратком руководстве показано, как для запрашивать результаты поиска из экземпляра службы пользовательского поиска с помощью Node.js для вызова конечной точки службы пользовательского поиска Bing.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 48fc234e15ce3b9172d766f6fae11b51a017ce70
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35382954"
---
# <a name="call-bing-custom-search-endpoint-nodejs"></a>Конечная точка службы пользовательского поиска Bing (Node.js)

В этом кратком руководстве показано, как для запрашивать результаты поиска из экземпляра службы пользовательского поиска с помощью Node.js для вызова конечной точки службы пользовательского поиска Bing. 

## <a name="prerequisites"></a>предварительным требованиям
Для работы с этим кратким руководством вам понадобится:

- Экземпляр службы пользовательского поиска. Ознакомьтесь с разделом [Create your first Bing Custom Search instance](quick-start.md) (Создание первого экземпляра службы пользовательского поиска Bing).

- Установленный компонент [Node.js](https://www.nodejs.org/).

-  [Учетная запись API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) с **API-интерфейсами поиска Bing**. Для данного краткого руководства достаточно [бесплатной пробной версии](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search). Требуется ключ доступа, предоставляемый при активации бесплатной пробной версии. Можно также использовать ключ платной подписки, указанный на панели мониторинга Azure.

## <a name="run-the-code"></a>Выполнение кода

Чтобы вызвать конечную точку службы пользовательского поиска Bing, выполните следующие действия.

1. Создайте каталог для своего кода.
2. Из командной строки или терминала перейдите в эту папку.
3. Установите модуль Node **request**.
    <pre>
    npm install request
    </pre>
4. Создайте файл BingCustomSearch.js и скопируйте в него приведенный ниже код.
5. Замените **YOUR-SUBSCRIPTION-KEY** и **YOUR-CUSTOM-CONFIG-ID** своими ключом и идентификатором конфигурации (см. шаг 1).

    ``` javascript
    var request = require("request");
    
    var subscriptionKey = 'YOUR-SUBSCRIPTION-KEY';
    var customConfigId = 'YOUR-CUSTOM-CONFIG-ID';
    var searchTerm = 'microsoft';
    
    var options = {
        url: 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 
          'q=' + searchTerm + 
          '&customconfig=' + customConfigId,
        headers: {
            'Ocp-Apim-Subscription-Key' : subscriptionKey
        }
    }
    
    request(options, function(error, response, body){
        var searchResponse = JSON.parse(body);
        for(var i = 0; i < searchResponse.webPages.value.length; ++i){
            var webPage = searchResponse.webPages.value[i];
            console.log('name: ' + webPage.name);
            console.log('url: ' + webPage.url);
            console.log('displayUrl: ' + webPage.displayUrl);
            console.log('snippet: ' + webPage.snippet);
            console.log('dateLastCrawled: ' + webPage.dateLastCrawled);
            console.log();
        }
    })
    ```
6. Выполните код с помощью приведенной ниже команды.
    ```    
    node BingCustomSearch.js
   ``` 

## <a name="next-steps"></a>Дополнительная информация
- [Настройка размещенного на серверах пользовательского интерфейса](./hosted-ui.md)
- [Использование маркеров оформления для выделения текста](./hit-highlighting.md)
- [Разбивка веб-страниц на страницы](./page-webpages.md)