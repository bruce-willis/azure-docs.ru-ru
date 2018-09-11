---
title: Краткое руководство. Использование пакета SDK Поиска в Интернете Bing для Node.js
description: Узнайте, как использовать пакет SDK Поиска в Интернете Bing для Node.js.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: 7c3003ab4ba40a9d0212e7c94b6dd3bfbc8f0ca2
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43186637"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-nodejs"></a>Краткое руководство. Использование пакета SDK Поиска в Интернете Bing для Node.js

Пакет SDK Поиска в Интернете Bing позволяет интегрировать Поиск в Интернете Bing в любое приложение Node.js. Из этого краткого руководства вы узнаете, как создать экземпляр клиента, отправить запрос и вывести ответ.

Хотите увидеть код прямо сейчас? [Примеры для пакета SDK Поиска в Интернете Bing для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) доступны в GitHub.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить это руководство, вам потребуется следующее:

* [Node.js 6](https://nodejs.org/en/download/) или более поздней версии;
* ключ подписки;  

## <a name="set-up-your-development-environment"></a>Настройка среды разработки

Давайте начнем с настройки среды разработки для проекта Node.js.

1. Создайте каталог для своего проекта:

    ```console
    mkdir YOUR_PROJECT
    ```

2. Создайте файл пакета:

    ```console
    cd YOUR_PROJECT
    npm init
    ```

3. Теперь установите несколько модулей Azure и добавьте их в `package.json`:

    ```console
    npm install --save azure-cognitiveservices-websearch
    npm install --save ms-rest-azure
    ```

## <a name="create-a-project-and-declare-required-modules"></a>Создание проекта и объявление обязательных модулей

В том же каталоге, где находится `package.json`, создайте проект Node.js с помощью любой среды IDE или редактора. Например, `sample.js`.

Скопируйте в приложение следующий код: Он загружает модули, установленные в предыдущем разделе.

```javascript
const CognitiveServicesCredentials = require('ms-rest-azure').CognitiveServicesCredentials;
const WebSearchAPIClient = require('azure-cognitiveservices-websearch');
```

## <a name="instantiate-the-client"></a>Создание экземпляра клиента

Этот код создает экземпляр клиента с помощью модуля `azure-cognitiveservices-websearch`. Не забудьте ввести действующий ключ подписки Azure, прежде чем продолжить работу.

```javascript
let credentials = new CognitiveServicesCredentials('YOUR-ACCESS-KEY');
let webSearchApiClient = new WebSearchAPIClient(credentials);
```

## <a name="make-a-request-and-print-the-results"></a>Выполнение запроса и вывод результатов

Используйте клиент для отправки запроса в Поиск в Интернете Bing. Если ответ содержит результаты для любого из элементов массива `properties`, в консоль выводится `result.value`.

```javascript
webSearchApiClient.web.search('seahawks').then((result) => {
    let properties = ["images", "webPages", "news", "videos"];
    for (let i = 0; i < properties.length; i++) {
        if (result[properties[i]]) {
            console.log(result[properties[i]].value);
        } else {
            console.log(`No ${properties[i]} data`);
        }
    }
}).catch((err) => {
    throw err;
})
```

## <a name="run-the-program"></a>Запуск программы

А теперь запустите программу!

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите ключ подписки из кода программы, когда завершите работу с этим проектом.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры использования пакета SDK Cognitive Services для Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)

## <a name="see-also"></a>См. также

* [Справочник по пакету SDK Azure для Node](https://docs.microsoft.com/javascript/api/azure-cognitiveservices-websearch/)
