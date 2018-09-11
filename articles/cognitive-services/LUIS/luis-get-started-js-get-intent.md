---
title: Анализ текста на естественном языке в службе распознавания речи (LUIS) с помощью JavaScript — Azure Cognitive Services | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как использовать общедоступное приложение LUIS для определения намерений пользователя в разговоре. С помощью JavaScript отправьте намерение пользователя в виде текста в конечную точку прогнозирования HTTP общедоступного приложения. В конечной точке LUIS применяет модель общедоступного приложения, чтобы проанализировать смысл текста на естественном языке, определить общее намерение и извлечь данные, релевантные для предметной области приложения.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: d787f744ff0fe7315553e9dd6f4465122f7e06b2
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159713"
---
# <a name="quickstart-analyze-text-using-javascript"></a>Краткое руководство по анализу текста с использованием JavaScript

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="prerequisites"></a>Предварительные требования

* [Visual Studio Code](https://code.visualstudio.com/)
* идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.


## <a name="get-luis-key"></a>Получение ключа LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Анализ текста с помощью браузера

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-javascript"></a>Анализ текста с помощью JavaScript 

С помощью JavaScript можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге. 

1. Скопируйте следующий код и сохраните его в HTML-файл:

   [!code-html[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/javascript/call-endpoint.html)]

2. Откройте файл в браузере. Введите ключ конечной точки LUIS в форму и нажмите кнопку **Submit** (Отправить).

    ![Отображаемый в браузере пример HTML с результатами LUIS для приложения Home Automation](./media/luis-get-started-js-get-intent/html-results.png)

    Результат появится в области под формой. 

## <a name="luis-keys"></a>Ключи LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите файл JavaScript.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-javascript-add-utterance.md)
