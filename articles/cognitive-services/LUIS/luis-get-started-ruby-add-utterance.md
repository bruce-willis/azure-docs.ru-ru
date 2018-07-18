---
title: Руководство по добавлению фраз в приложение LUIS с использованием Ruby | Документация Майкрософт
description: В этом руководстве вы узнаете, как вызвать приложение LUIS с помощью Ruby.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 7a470fd551a58978e6f2be0450a2e2a6cd471fc4
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266062"
---
# <a name="tutorial-add-utterances-to-app-using-ruby"></a>Руководство по добавлению фраз в приложение с использованием Ruby 
В этом руководстве рассматривается, как написать программу, чтобы добавить фразу в намерение, используя программные интерфейсы (API) разработки на языке Ruby.

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание консольного проекта Visual Studio. 
> * Добавление метода для вызова API LUIS, чтобы добавить фразу и обучить приложение.
> * Добавление JSON-файла с примерами фраз для намерения "BookFlight".
> * Запуск консоли и просмотр состояния обучения фразам.

Дополнительные сведения см. в разделах технической документации по API-интерфейсам [добавления примера фразы в намерение](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) и [просмотра состояния обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="prerequisites"></a>предварительным требованиям

* [Ruby](http://rubyinstaller.org/) 
* **[Ключ разработки](luis-concept-keys.md#authoring-key)** LUIS. Его можно найти в разделе параметров учетной записи на веб-сайте [LUIS](luis-reference-regions.md).
* [**Идентификатор существующего приложения LUIS**](./luis-get-started-create-app.md). Идентификатор приложения отображается на панели мониторинга приложения. Приложение LUIS с намерениями и сущностями, используемыми в файле `utterances.json`, должно существовать до выполнения кода в `add-utterances.rb`. Код в этой статье не создает намерений и сущностей. Он только добавляет фразы для существующих намерений и сущностей. 
* **Идентификатор версии** приложения, которое получает фразы. Идентификатор по умолчанию — 0.1.
* Создайте файл проекта с именем `add-utterances.rb` в VSCode.

> [!NOTE] 
> Полный файл `add-utterances.cs` и пример файла `utterances.json` доступны в [**репозитории GitHub** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/ruby/).


## <a name="write-the-ruby-code"></a>Написание кода Ruby

Добавьте зависимости в файл.

   [!code-ruby[Ruby and LUIS Dependencies](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=1-28 "Ruby and LUIS Dependencies")]

Добавьте запрос GET, получающий состояние обучения.

   [!code-ruby[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=30-40 "SendGet")]

Добавьте запрос POST, который создает фразы или запускает обучение. 

   [!code-ruby[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=42-54 "SendPost")]

Добавьте функцию `AddUtterances`.

   [!code-ruby[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=56-61 "AddUtterances method")]


Добавьте функцию `Train`. 

   [!code-ruby[Train](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=63-69 "Train")]

Добавьте функцию `Status`.

   [!code-ruby[Status](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=71-75 "Status")]

Чтобы управлять аргументами, добавьте основной код.

   [!code-ruby[Main code](~/samples-luis/documentation-samples/authoring-api-samples/ruby/add-utterances.rb?range=77-93 "Main code")]

## <a name="specify-utterances-to-add"></a>Указание фраз, которые нужно добавить
Создайте и измените файл `utterances.json`, указав в нем **массив фраз**, которые нужно добавить в приложение LUIS. Намерение и сущности **должны** уже быть добавлены в приложение LUIS.

> [!NOTE]
> Приложение LUIS с намерениями и сущностями, используемыми в файле `utterances.json`, должно существовать до выполнения кода в `add-utterances.rb`. Код в этой статье не создает намерений и сущностей. Он только добавляет фразы для существующих намерений и сущностей.

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

Запустите приложение из командной строки с помощью Ruby.

Если вызвать `add-utterances.rb` только с utterance.json в качестве аргумента, новые фразы будут добавлены, но приложение LUIS не обучено на их основе.
````
> ruby add-utterances.rb ./utterances.json
````

Возвращаются результаты вызова API добавления фраз. Поле `response` имеет такой формат для добавленных фраз. Параметр `hasError` имеет значение false, что значит, фраза была добавлена.  

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
Вызовите интерфейс добавления фраз с аргументом `-train` для отправки запроса на обучение.

````
> ruby add-utterances.rb ./utterances.json -train
````

> [!NOTE]
> Дублирующиеся фразы не добавляются повторно и не приводят к ошибке. `response` содержит идентификатор исходной фразы.

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
Вызовите пример с аргументом `-status` для проверки состояния обучения.

````
> ruby add-utterances.rb ./utterances.json -status
````

```
Requested training status.
[
   {
      "modelId": "eb2f117c-e10a-463e-90ea-1a0176660acc",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c1bdfbfc-e110-402e-b0cc-2af4112289fb",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "863023ec-2c96-4d68-9c44-34c1cbde8bc9",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "82702162-73ba-4ae9-a6f6-517b5244c555",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "37121f4c-4853-467f-a9f3-6dfc8cad2763",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "de421482-753e-42f5-a765-ad0a60f50d69",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "80f58a45-86f2-4e18-be3d-b60a2c88312e",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "c9eb9772-3b18-4d5f-a1e6-e0c31f91b390",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "2afec2ff-7c01-4423-bb0e-e5f6935afae8",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   },
   {
      "modelId": "95a81c87-0d7b-4251-8e07-f28d180886a1",
      "details": {
         "statusId": 0,
         "status": "Success",
         "exampleCount": 33,
         "trainingDateTime": "2017-11-20T18:09:11Z"
      }
   }
]
```

## <a name="clean-up-resources"></a>Очистка ресурсов
Завершив работу с руководством, удалите Visual Studio и консольное приложение, если они вам больше не нужны.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Создание приложения LUIS программным способом](luis-tutorial-node-import-utterances-csv.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
