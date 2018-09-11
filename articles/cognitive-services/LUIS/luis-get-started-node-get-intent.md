---
title: Анализ текста на естественном языке в службе распознавания речи (LUIS) с помощью Node.js — Azure Cognitive Services | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как использовать общедоступное приложение LUIS для определения намерений пользователя в разговоре. С помощью Node.js отправьте намерение пользователя в виде текста в конечную точку прогнозирования HTTP общедоступного приложения. В конечной точке LUIS применяет модель общедоступного приложения, чтобы проанализировать смысл текста на естественном языке, определить общее намерение и извлечь данные, релевантные для предметной области приложения.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d7067041ae8e413675de3c95ca4ba0c1b987f47a
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771698"
---
# <a name="quickstart-analyze-text-using-nodejs"></a>Краткое руководство по анализу текста с использованием Node.js

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Предварительные требования

* Язык программирования [Node.js](https://nodejs.org/). 
* [Visual Studio Code](https://code.visualstudio.com/)
* Идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.


> [!NOTE] 
> Полное решение Node.js доступно в разделе [**LUIS-Samples** репозитория Github](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/analyze-text/node).

## <a name="get-luis-key"></a>Получение ключа LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Анализ текста с помощью браузера

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-nodejs"></a>Анализ текста с помощью Node.js

С помощью Node.js можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге.

1. Скопируйте следующий фрагмент кода.

   [!code-nodejs[Console app code that calls a LUIS endpoint for Node.js](~/samples-luis/documentation-samples/quickstarts/analyze-text/node/call-endpoint.js)]

2. Создайте файл `.env` со следующим текстом или задайте эти переменные в системной среде:

    ```CMD
    LUIS_APP_ID=df67dcdb-c37d-46af-88e1-8b97951ca1c2
    LUIS_ENDPOINT_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    ```

3. Укажите свой ключ для переменной среды `LUIS_ENDPOINT_KEY`.

4. Установите зависимости, выполнив следующую команду в командной строке: `npm install`.

5. Выполните код с помощью команды `npm start`. Отобразятся те же значения, которые вы видели ранее в окне браузера.

## <a name="luis-keys"></a>Ключи LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите файл Node.js.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-node-add-utterance.md)