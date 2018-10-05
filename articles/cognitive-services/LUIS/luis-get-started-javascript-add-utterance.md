---
title: Краткое руководство для JavaScript — изменение модели и обучение приложения LUIS
titleSuffix: Azure Cognitive Services
description: Из этого краткого руководства вы узнаете, как вызвать приложение LUIS с помощью JavaScript.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 38e25b0634b53f4fcc0507091e78ab49b29c8d38
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033402"
---
# <a name="quickstart-change-model-using-javascript"></a>Краткое руководство. Изменение модели с помощью JavaScript

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [Visual Studio Code](https://code.visualstudio.com/).

[!INCLUDE [Code is available in LUIS-Samples Github repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Файл JSON с примерами высказываний.

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]


## <a name="create-quickstart-code"></a>Создание простого примера кода

Создайте файл `add-utterances.html` и добавьте следующий код:

   [!code-html[Html code](~/samples-luis/documentation-samples/quickstarts/change-model/javascript/add-utterance.html "Javascript code")]

## <a name="run-code"></a>Выполнение кода

1. Откройте файл в браузере.

2. Добавьте идентификатор разработки LUIS (идентификатор приложения LUIS).

3. Измените **массив фраз**, которые нужно добавить в приложение. Они хранятся в переменной utteranceJSON. Измените эти значения в соответствии со своей областью применения и требованиями к фразам. 

    ```json
    // example batch utterances
    var utteranceJSON = [
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
        }
    ,
        {
            "text": "book a flight",
            "intentName": "BookFlight",
            "entityLabels": []
        }
    ];
    ```

4. Нажмите кнопку `Upload utterance`. Результаты LUIS отобразятся под кнопками.

5. Нажмите кнопку `Train model` (Обучить модель), чтобы обучить приложение на основе этих новых фраз.

6. Нажмите кнопку `Train Status` (Состояние обучения), чтобы увидеть состояние обучения. 

    ![add-utterances.html](./media/luis-quickstart-javascript-add-utterance/add-utterance.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Когда вы закончите работу с этим руководством, удалите все созданные при работе с ним файлы. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Интеграция LUIS в программу-робот](luis-csharp-tutorial-build-bot-framework-sample.md)
