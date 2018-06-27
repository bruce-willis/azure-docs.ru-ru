---
title: Руководство по добавлению фраз в приложение LUIS с использованием C# | Документация Майкрософт
description: В этом руководстве вы узнаете, как вызвать приложение LUIS с помощью C#.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: v-geberr
ms.openlocfilehash: d9b3ca46cc635d961edadf3e3555f9656b6b5a1d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264249"
---
# <a name="tutorial-add-utterances-to-a-luis-app-using-c"></a>Руководство по добавлению фраз в приложение LUIS с использованием C# 
В этом руководстве рассматривается, как написать программу, чтобы добавить фразу в намерение, используя программные интерфейсы (API) разработки на языке C#.

<!-- green checkmark -->
> [!div class="checklist"]
> * Создание консольного проекта Visual Studio. 
> * Добавление метода для вызова API LUIS, чтобы добавить фразу и обучить приложение.
> * Добавление JSON-файла с примерами фраз для намерения "BookFlight".
> * Запуск консоли и просмотр состояния обучения фразам.

Дополнительные сведения см. в разделах технической документации по API-интерфейсам [добавления примера фразы в намерение](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c08), [обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) и [просмотра состояния обучения](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46).

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="prerequisites"></a>предварительным требованиям

* Последний [**выпуск Visual Studio Community**](https://www.visualstudio.com/downloads/). 
* **[Ключ разработки](luis-concept-keys.md#authoring-key)** LUIS. Его можно найти в разделе параметров учетной записи на веб-сайте [LUIS](luis-reference-regions.md).
* [**Идентификатор существующего приложения LUIS**](./luis-get-started-create-app.md). Идентификатор приложения отображается на панели мониторинга приложения. Приложение LUIS с намерениями и сущностями, используемыми в файле `utterances.json`, должно существовать до выполнения кода. Код в этой статье не создает намерений и сущностей. Он добавляет фразы для существующих намерений и сущностей. 
* **Идентификатор версии** приложения, которое получает фразы. Идентификатор по умолчанию — 0.1.
* Создайте файл проекта с именем `add-utterances.cs` в VSCode.

> [!NOTE] 
> Полное решение C#, в том числе пример файла `utterances.json`, доступны в [**репозитории GitHub** LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/authoring-api-samples/csharp/).

## <a name="create-the-project-in-visual-studio"></a>Создание проекта в Visual Studio

В Visual Studio создайте **классическое консольное приложение для Windows** на платформе .NET Framework. 

![Тип проекта Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

## <a name="add-the-systemweb-dependency"></a>Добавление зависимости System.Web

Проекту Visual Studio необходима зависимость **System.Web**. В обозревателе решений щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку**.

![Добавление ссылки на System.web](./media/luis-quickstart-cs-add-utterance/system.web.png)

## <a name="write-the-c-code"></a>Написание кода C#
Файл **Program.cs** должен выглядеть так:

```CSharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}

```

Добавьте зависимости System.IO и System.Net.Http.

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=1-6 "Add the dependencies")]


Добавьте идентификаторы LUIS и строки в класс **Program**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=12-30&dedent=8 "Add the LUIS IDs and strings")]

Добавьте метод JsonPrettyPrint.

   [!code-csharp[Add the JsonPrettyPrint method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=32-92 "Add the JsonPrettyPrint method")]

Добавьте запрос GET, который создает фразы или запускает обучение. 

   [!code-csharp[SendGet](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=93-103 "SendGet")]


Добавьте запрос POST, который создает фразы или запускает обучение. 

   [!code-csharp[SendPost](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=104-115 "SendPost")]

Добавьте функцию `AddUtterances`.

   [!code-csharp[AddUtterances method](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=116-125 "AddUtterances method")]


Добавьте функцию `Train`. 

   [!code-csharp[Train](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=126-136 "Train")]

Добавьте функцию `Status`.

   [!code-csharp[Status](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=137-143 "Status")]

Чтобы управлять аргументами, добавьте основной код.

   [!code-csharp[Main code](~/samples-luis/documentation-samples/authoring-api-samples/csharp/ConsoleApp1/Program.cs?range=144-172 "Main code")]

## <a name="specify-utterances-to-add"></a>Указание фраз, которые нужно добавить
Создайте и измените файл `utterances.json`, указав в нем **массив фраз**, которые нужно добавить в приложение LUIS. Намерение и сущности **должны** уже быть добавлены в приложение LUIS.

> [!NOTE]
> Приложение LUIS с намерениями и сущностями, используемыми в файле `utterances.json`, должно существовать до выполнения кода в `add-utterances.cs`. Код в этой статье не создает намерений и сущностей. Он только добавляет фразы для существующих намерений и сущностей.

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

## <a name="mark-the-json-file-as-content"></a>Пометка файла JSON как содержимого
В обозревателе решений щелкните правой кнопкой мыши `utterances.json` и выберите **Свойства**. В окне свойств параметр **Действие при построении** должен иметь значение `Content`, а параметр **Копировать в выходной каталог** — значение `Copy Always`.  

![Пометка файла JSON как содержимого](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="add-an-utterance-from-the-command-line"></a>Добавление фразы из командной строки

Создайте и запустите приложение из командной строки с помощью C# из каталога /bin/Debug. Убедитесь, что файл utterances.json также находится в этом каталоге.

При вызове файла add-utterances.cs только с utterance.json в качестве аргумента новые фразы будут добавлены, но приложение LUIS не обучено на их основе.
````
ConsoleApp\bin\Debug> ConsoleApp1.exe utterances.json
````

Эта командная строка отображает результаты вызова API добавления фраз. Поле `response` имеет такой формат для добавленных фраз. Параметр `hasError` имеет значение false, что значит, фраза была добавлена.  

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
ConsoleApp\bin\Debug> ConsoleApp1.exe -train utterances.json
````

> [!NOTE]
> Дублирующиеся фразы не добавляются повторно и не приводят к ошибке. `response` содержит идентификатор исходной фразы.

Ниже показан JSON, показывающий результат успешного запроса на обучение:

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
Вызовите приложение с аргументом `-status`, чтобы проверить состояние обучения и отобразить сведения о состоянии.

````
ConsoleApp\bin\Debug> ConsoleApp1.exe -status
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
