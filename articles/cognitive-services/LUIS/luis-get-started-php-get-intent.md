---
title: Краткое руководство по анализу текста на естественном языке в службе распознавания речи (LUIS) с помощью PHP — Azure Cognitive Services | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как использовать общедоступное приложение LUIS для определения намерений пользователя в разговоре. Отправляйте намерение пользователя в виде текста в конечную точку прогноза HTTP общедоступного приложения, используя PHP. В конечной точке LUIS применяет модель общедоступного приложения, чтобы проанализировать смысл текста на естественном языке, определить общее намерение и извлечь данные, релевантные для предметной области приложения.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/23/2018
ms.author: diberry
ms.openlocfilehash: 14ac32ffc0f4332d0cbc0da59a55ccc500e216d7
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771689"
---
# <a name="quickstart-analyze-text-using-php"></a>Краткое руководство по анализу текста с использованием PHP

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Предварительные требования

* Язык программирования [PHP](http://php.net/).
* [Visual Studio Code](https://code.visualstudio.com/)
* Идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Анализ текста с помощью браузера

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-php"></a>Анализ текста с помощью PHP 

С помощью PHP можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге. 

1. Скопируйте следующий код и сохраните его в файл с именем `endpoint-call.php`.

   [!code-php[PHP code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/php/endpoint-call.php)]

2. Замените `"YOUR-KEY"` ключом своей конечной точки.

3. Запустите приложение PHP, используя `php endpoint-call.php`. Отобразится тот же результат JSON, который вы видели ранее в окне браузера.

## <a name="luis-keys"></a>Ключи LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите файл PHP.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-php-add-utterance.md)