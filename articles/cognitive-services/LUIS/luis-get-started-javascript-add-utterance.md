---
title: Руководство по добавлению фраз в приложение LUIS с использованием JavaScript | Документация Майкрософт
description: В этом руководстве вы узнаете, как вызвать приложение LUIS с помощью JavaScript.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/18/2017
ms.author: v-geberr
ms.openlocfilehash: b6d021dcfdddb5449aa989c6aa06d7faf326befb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265465"
---
# <a name="tutorial-add-utterances-to-app-using-javascript"></a>Руководство по добавлению фраз в приложение с использованием JavaScript
В этом руководстве рассматривается, как написать программу, чтобы добавить фразу в намерение, используя программные интерфейсы (API) разработки на языке JavaScript.

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание консольного проекта Visual Studio. 
> * Добавление метода для вызова API LUIS, чтобы добавить фразу и обучить приложение.
> * Добавление JSON-файла с примерами фраз для намерения "BookFlight".
> * Запуск консоли и просмотр состояния обучения фразам.

Дополнительные сведения см. в разделах технической документации по API-интерфейсам [добавления примера фразы в намерение](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) и [просмотра состояния обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="prerequisites"></a>предварительным требованиям
* [**Ключ разработки**](luis-concept-keys.md#authoring-key) LUIS. 
* **Идентификатор существующего приложения LUIS** и **идентификатор версии**. 
* Новый файл проекта с именем `add-utterances.html` в VSCode.

> [!NOTE] 
> Полный файл `add-utterances.html` доступен в [**репозитории GitHub** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/javascript/add-utterance.html).


## <a name="write-the-code"></a>Написание кода
Создайте файл `add-utterances.html` и добавьте следующий код:

   [!code-javascript[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/javascript/add-utterance.html "Java Dependencies")]

## <a name="view-in-browser"></a>Просмотр в браузере
1. Откройте файл в браузере.

2. Добавьте идентификатор разработки LUIS, идентификатор приложения LUIS и измените версию, если она отличается от `0.1`.

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
Завершив работу с руководством, удалите Visual Studio и консольное приложение, если они вам больше не нужны. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Интеграция LUIS в программу-робот](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website