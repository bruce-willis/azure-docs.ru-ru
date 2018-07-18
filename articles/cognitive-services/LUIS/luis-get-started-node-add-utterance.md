---
title: Руководство по добавлению фраз в приложение LUIS с использованием Node.js | Документация Майкрософт
description: В этом руководстве вы узнаете, как вызвать приложение LUIS с помощью Node.js.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: a1e028395ce4b1679a367ce110c2ba120128c501
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264232"
---
# <a name="tutorial-add-utterances-app-using-nodejs"></a>Руководство по добавлению фраз в приложение с использованием Node.js 
В этом руководстве рассматривается, как написать программу, чтобы добавить фразу в намерение, используя программные интерфейсы (API) разработки на языке Node.js.

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание консольного проекта Visual Studio. 
> * Добавление метода для вызова API LUIS, чтобы добавить фразу и обучить приложение.
> * Добавление JSON-файла с примерами фраз для намерения "BookFlight".
> * Запуск консоли и просмотр состояния обучения фразам.

Дополнительные сведения см. в разделах технической документации по API-интерфейсам [добавления примера фразы в намерение](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) и [просмотра состояния обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="prerequisites"></a>предварительным требованиям

* [**Node.js**](https://nodejs.org/en/download/) последней версии с NPM.
* Зависимости NPM, необходимые в этой статье: [**request**](https://www.npmjs.com/package/request), [**request-promise**](https://www.npmjs.com/package/request-promise), [**fs-extra**](https://www.npmjs.com/package/fs-extra).  
* **(Рекомендуется.)** [Visual Studio Code](https://code.visualstudio.com/) для применения функции IntelliSense и отладки.
* **[Ключ разработки](luis-concept-keys.md#authoring-key)** LUIS. Его можно найти в разделе параметров учетной записи на веб-сайте [LUIS](luis-reference-regions.md).
* [**Идентификатор существующего приложения LUIS**](./luis-get-started-create-app.md). Идентификатор приложения отображается на панели мониторинга приложения. Приложение LUIS с намерениями и сущностями, используемыми в файле `utterances.json`, должно существовать до выполнения кода в `add-utterances.js`. Код в этой статье не создаст намерений и сущностей. Он только добавит фразы для существующих намерений и сущностей. 
* **Идентификатор версии** приложения, которое получает фразы. Идентификатор по умолчанию — 0.1.
* Создайте файл проекта с именем `add-utterances.js` в VSCode.

> [!NOTE] 
> Полный файл `add-utterances.js` доступен в [**репозитории GitHub** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/examples/add-utterances/nodejs).


## <a name="write-the-nodejs-code"></a>Написание кода Node.js

Добавьте зависимости NPM в файл.

   [!code-javascript[NPM Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=16-19 "NPM Dependencies")]

Добавьте в файл константы LUIS. Скопируйте следующий код и укажите свой ключ разработки, идентификатор приложения и идентификатор версии.

   [!code-javascript[LUIS key and IDs](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=22-29 "LUIS key and IDs")]

Добавьте имя и расположение передаваемого файла, содержащего фразы. 

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=31-33 "Add upload file")]

Добавьте переменные, которые содержат значения командной строки.

   [!code-javascript[Add upload file](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=35-49 "Add upload file")]


Добавьте функцию `sendUtteranceToApi` для отправки и получения вызовов HTTP. 

   [!code-javascript[Send the HTTP request](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=135-151 "Send the HTTP request")]

Добавьте JSON-объект конфигурации, используемый функцией `addUtterance`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=52-59 "Add configuration information for adding utterance")]

Добавьте функцию `addUtterance` для управления запросом и ответом API, используемыми методом `SendUtteranceToApp`.

   [!code-javascript[Add configuration information for adding utterance](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=62-92 "Add configuration information for adding utterance")]

Добавьте JSON-объект конфигурации, используемый функцией `train`.

   [!code-javascript[Add configuration information for training LUIS](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=94-101 "Add configuration information for training LUIS")]

Добавьте функцию `train` для запуска процесса обучения. 

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=103-133 "Train the application")]

Добавьте код, который выбирает действие для выполнения (добавление фразы или обучение) на основе переменных командной строки.

   [!code-javascript[Train the application](~/samples-luis/documentation-samples/authoring-api-samples/node/add-utterances.js?range=153-184 "Train the application")]

## <a name="specify-utterances-to-add"></a>Указание фраз, которые нужно добавить
Создайте и измените файл `utterances.json`, указав в нем **массив фраз**, которые нужно добавить в приложение LUIS. Намерение и сущности **должны** уже быть добавлены в приложение LUIS.

> [!NOTE]
> Приложение LUIS с намерениями и сущностями, используемыми в файле `utterances.json`, должно существовать до выполнения кода в `add-utterances.js`. Код в этой статье не создаст намерений и сущностей. Он только добавит фразы для существующих намерений и сущностей.

Поле `text` содержит текст фразы. Поле `intentName` должно соответствовать имени намерения в приложении LUIS. Поле `entityLabels` является обязательным. Если вы не хотите помечать сущности, укажите пустой список, как показано в следующем примере:

Если список entityLabels не пуст, `startCharIndex` и `endCharIndex` должны пометить сущность, указанную в поле `entityName`. Оба индекса отсчитываются с нуля. Это означает, что 6 в примере ссылается на букву "S" в слове Seattle, а не на пробел перед заглавной буквой S.

```json
[
    {
        "text": "go to Seattle",
        "intentName": "BookFlight",
        "entityLabels": [
            {
                "entityName": "Location::LocationTo",
                "startCharIndex": 6,
                "endCharIndex": 12
            }
        ]
    },
    {
        "text": "book a flight",
        "intentName": "BookFlight",
        "entityLabels": []
    }
]
```

## <a name="add-an-utterance-from-the-command-line"></a>Добавление фразы из командной строки

Запустите приложение из командной строки с помощью Node.js.

Если вызвать интерфейс добавления фраз без аргументов, фраза будет добавлена в приложение, но обучение по ней не будет выполнено.
````
> node add-utterances.js
````

В этом примере создается файл `results.json`, содержащий результаты вызова API добавления фраз. Поле `response` имеет такой формат для добавленных фраз. Параметр `hasError` имеет значение false, что значит, фраза была добавлена.  

```json
    "response": [
        {
            "value": {
                "UtteranceText": "go to seattle",
                "ExampleId": -5123383
            },
            "hasError": false
        },
        {
            "value": {
                "UtteranceText": "book a flight",
                "ExampleId": -169157
            },
            "hasError": false
        }
    ]
```

## <a name="add-an-utterance-and-train-from-the-command-line"></a>Добавление фразы и запуск обучения из командной строки
Вызовите интерфейс добавления фраз с аргументом `-train`, чтобы отправить запрос на обучение и впоследствии запросить состояние обучения. Состояние помещается в очередь сразу после начала обучения. Сведения о состоянии записываются в файл.

````
> node add-utterances.js -train
````

> [!NOTE]
> Дублирующиеся фразы не добавляются повторно и не приводят к ошибке. `response` содержит идентификатор исходной фразы.

При вызове примера с аргументом `-train` создается файл `training-results.json`, указывающий, что запрос на обучение приложения LUIS был успешно помещен в очередь. 

Ниже показан результат успешного запроса на обучение.
```json
{
    "request": null,
    "response": {
        "statusId": 9,
        "status": "Queued"
    }
}
```

После того как запрос на обучение будет поставлен в очередь, завершение обучения может занять некоторое время.

## <a name="get-training-status-from-the-command-line"></a>Получение состояния обучения из командной строки
Вызовите пример с аргументом `-status`, чтобы проверить состояние обучения и записать сведения о состоянии в файл.

````
> node add-utterances.js -status
````

## <a name="clean-up-resources"></a>Очистка ресурсов
Завершив работу с руководством, удалите Visual Studio и консольное приложение, если они вам больше не нужны. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Создание приложения LUIS программным способом](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
