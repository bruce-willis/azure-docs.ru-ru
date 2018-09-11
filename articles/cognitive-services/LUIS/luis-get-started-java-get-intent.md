---
title: Анализ текста на естественном языке в службе распознавания речи (LUIS) с помощью Java — Azure Cognitive Services | Документация Майкрософт
description: Из этого краткого руководства вы узнаете, как использовать общедоступное приложение LUIS для определения намерений пользователя в разговоре. Отправляйте намерение пользователя в виде текста в конечную точку прогноза HTTP общедоступного приложения, используя Java. В конечной точке LUIS применяет модель общедоступного приложения, чтобы проанализировать смысл текста на естественном языке, определить общее намерение и извлечь данные, релевантные для предметной области приложения.
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 559c0e5832249b095b923fe88467f8f4c5ffa5e1
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/24/2018
ms.locfileid: "43771768"
---
# <a name="quickstart-analyze-text-using-java"></a>Краткое руководство. Анализ текста с помощью Java

[!include[Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Предварительные требования

* [JDK SE](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (комплект разработчика Java, выпуск "Стандартный");
* [Visual Studio Code](https://code.visualstudio.com/)
* Идентификатор общедоступного приложения: df67dcdb-c37d-46af-88e1-8b97951ca1c2.


[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Получение ключа LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="analyze-text-with-browser"></a>Анализ текста с помощью браузера

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="analyze-text-with-java"></a>Анализ текста с помощью Java 

С помощью Java можно получить доступ к тем же результатам, которые вы уже видели в окне браузера на предыдущем шаге. 

1. Скопируйте следующий код для создания класса в файл с именем `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Замените значение переменной `YOUR-KEY` ключом LUIS.

3. Скомпилируйте программу Java с помощью `javac -cp ":lib/*" LuisGetRequest.java`. 

4. Запустите приложение, нажав клавишу `java -cp ":lib/*" LuisGetRequest.java`. Отобразится тот же результат JSON, который вы видели ранее в окне браузера.

    ![Окно консоли, в котором отображается результат JSON из LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Ключи LUIS

[!include[Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

Удалите файл Java. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"]
> [Добавление высказываний](luis-get-started-java-add-utterance.md)