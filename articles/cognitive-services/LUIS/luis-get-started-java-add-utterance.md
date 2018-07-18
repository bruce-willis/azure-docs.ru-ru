---
title: Руководство по добавлению фраз в приложение LUIS с использованием Java | Документация Майкрософт
description: В этом руководстве вы узнаете, как вызвать приложение LUIS с помощью Java.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 5b9c7b90ca96b23fbabfeb1e1d06e4124e65a0dc
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266045"
---
# <a name="tutorial-add-utterances-to-app-using-java"></a>Руководство по добавлению фраз в приложение с использованием Java 
В этом руководстве рассматривается, как написать программу, чтобы добавить фразу в намерение, используя программные интерфейсы (API) разработки на языке Java.

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание консольного проекта Visual Studio. 
> * Добавление метода для вызова API LUIS, чтобы добавить фразу и обучить приложение.
> * Добавление JSON-файла с примерами фраз для намерения "BookFlight".
> * Запуск консоли и просмотр состояния обучения фразам.

Дополнительные сведения см. в разделах технической документации по API-интерфейсам [добавления примера фразы в намерение](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) и [просмотра состояния обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="prerequisites"></a>предварительным требованиям

* [**Пакет Java/JDK**](http://www.oracle.com/technetwork/java/javase/downloads/index.html) для Oracle последней версии.
* [Библиотека JSON Google GSON](https://github.com/google/gson).
* **[Ключ разработки](luis-concept-keys.md#authoring-key)** LUIS. Его можно найти в разделе параметров учетной записи на веб-сайте [LUIS](luis-reference-regions.md).
* [**Идентификатор существующего приложения LUIS**](./luis-get-started-create-app.md). Идентификатор приложения отображается на панели мониторинга приложения. Приложение LUIS с намерениями и сущностями, используемыми в файле `utterances.json`, должно существовать до выполнения кода в `AddUtterances.java`. Код в этой статье не создаст намерений и сущностей. Он только добавит фразы для существующих намерений и сущностей. 
* **Идентификатор версии** приложения, которое получает фразы. Идентификатор по умолчанию — 0.1.
* Создайте текстовый файл с именем `AddUtterances.java`.

> [!NOTE] 
> Полный файл `add-utterances.cs` и пример файла `utterances.json` доступны в [**репозитории GitHub** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/authoring-api-samples/java).


## <a name="write-the-java-code"></a>Написание кода Java

Добавьте зависимости Java в файл.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=31-34 "Java Dependencies")]

Создайте класс `AddUtterances`.

```Java
public class AddUtterances {
    // Insert code here
}
```

Этот класс содержит все приведенные ниже фрагменты кода.

Добавьте в класс константы LUIS. Скопируйте следующий код и укажите свой ключ разработки, идентификатор приложения и идентификатор версии.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=41-53 "LUIS-based IDs")]

Добавьте метод для вызова API LUIS. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=55-178 "HTTP request to LUIS")]

Добавьте метод для получения HTTP-ответа из API LUIS.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=180-226 "HTTP response from LUIS")]


Добавьте поведение обработки исключений. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=228-256 "Exception Handling")]

Добавьте поведение обработки выходных данных.

   [!code-java[Add output handling](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=258-267 "Add configuration information for adding utterance")]

Добавьте функцию main.

   [!code-java[Add main function](~/samples-luis/documentation-samples/authoring-api-samples/java/AddUtterances.java?range=269-345 "Add main function")]


## <a name="specify-utterances-to-add"></a>Указание фраз, которые нужно добавить
Создайте и измените файл `utterances.json`, указав в нем **массив фраз**, которые нужно добавить в приложение LUIS. Намерение и сущности **должны** уже быть добавлены в приложение LUIS.

> [!NOTE]
> Приложение LUIS с намерениями и сущностями, используемыми в файле `utterances.json`, должно существовать до выполнения кода в `AddUtterances.java`. Код в этой статье не создаст намерений и сущностей. Он только добавит фразы для существующих намерений и сущностей.

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

Скомпилируйте файл AddUtterance с зависимостями.

```
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

Если вызвать `AddUtterance` без аргументов, фразы будут добавлены в приложение LUIS, но обучение по ним не будет выполнено.
````
> java -classpath .;gson-2.8.2.jar AddUtterances
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
Вызовите `add-utterance` с аргументом `-train` для отправки запроса на обучение. 

````
> java -classpath .;gson-2.8.2.jar AddUtterances -train
````

> [!NOTE]
> Дублирующиеся фразы не добавляются повторно и не приводят к ошибке. `response` содержит идентификатор исходной фразы.

При вызове приложения с аргументом `-train` создается файл `training-results.json`, указывающий, что запрос на обучение приложения LUIS был успешно помещен в очередь. 

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
Вызовите приложение с аргументом `-status`, чтобы проверить состояние обучения и записать сведения о состоянии в файл.

````
> java -classpath .;gson-2.8.2.jar AddUtterances -status
````

## <a name="clean-up-resources"></a>Очистка ресурсов
Завершив работу с руководством, удалите Visual Studio и консольное приложение, если они вам больше не нужны. 

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Создание приложения LUIS программным способом](luis-tutorial-node-import-utterances-csv.md) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website