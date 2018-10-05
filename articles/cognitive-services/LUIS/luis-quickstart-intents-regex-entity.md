---
title: Руководство 3. Согласованные данные регулярного выражения. Извлечение данных в правильном формате
titleSuffix: Azure Cognitive Services
description: Извлечение согласованно отформатированных данных из высказывания с помощью сущности регулярного выражения.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 06e212ef756fda9224b38b41c69c7c4eccfb9796
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159862"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>Руководство 3. Извлечение данных в правильном формате
В этом руководстве изменяется приложение "Управление персоналом" для извлечения согласованно отформатированных данных из высказывания с помощью сущности **регулярного выражения**.

Назначение сущности — это извлечение важных данных, содержащихся в высказывании. Приложение использует сущность регулярного выражения для извлечения форматированных номеров формы в системе управления персоналом из высказывания. Хотя намерение высказывания всегда определяется машинным обучением, этот тип сущности к машинному обучению не относится. 

**Примеры высказывания включают следующее.**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Регулярное выражение оптимально для данных такого типа, когда:

* данные имеют правильный формат.

**Из этого руководства вы узнаете, как выполнять следующие задачи**.

<!-- green checkmark -->
> [!div class="checklist"]
> * Использовать существующее приложение из руководства
> * Добавление намерения FindForm
> * Добавление сущности регулярного выражения 
> * Train
> * Опубликовать
> * Получать намерения и сущности из конечной точки

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Использование существующего приложения
Продолжите работу с приложением **Управление персоналом**, созданным в соответствии с инструкциями из предыдущего руководства. 

Если у вас нет этого приложения, сделайте следующее.

1. Загрузите и сохраните [JSON-файл приложения](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json).

2. Импортируйте JSON-файл в новое приложение.

3. В разделе **Управление** на вкладке **Версии** клонируйте версию и присвойте ей имя `regex`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. Так как имя версии используется в маршруте URL-адреса, оно не может содержать символы, которые недопустимы в URL-адресе. 

## <a name="findform-intent"></a>Намерение FindForm

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Выберите **Create new intent**. (Создать намерение). 

3. Введите `FindForm` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово). 

    ![Снимок экрана диалогового окна создания намерения со служебными программами в поле поиска](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Добавьте примеры фраз в намерение.

    |Примеры фраз|
    |--|
    |Каков URL-адрес для hrf-123456?|
    |Где находится hrf-345678?|
    |Когда hrf-456098 был обновлен?|
    |Обновлял ли Артем Кузнецов hrf-234639 на прошлой неделе?|
    |Сколько существует версий hrf-345123?|
    |Кому необходимо авторизовать форму hrf-123456?|
    |Скольким сотрудникам необходимо подтвердить hrf-345678?|
    |Дата hrf-234123?|
    |Автор hrf-546234?|
    |Заголовок hrf-456234?|

    [ ![Снимок экрана страницы намерения с выделенными фразами](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    В приложении есть предварительно созданная сущность номера, добавленная в предыдущем руководстве, поэтому каждый номер формы помечен. Этого может быть достаточно для клиентского приложения, но номер не будет помечен типом. Благодаря созданию сущности с соответствующим именем клиентское приложение может обрабатывать возвращенную из LUIS сущность соответствующим образом.

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>Сущность регулярного выражения 
Сущность регулярного выражения для поиска номера формы — `hrf-[0-9]{6}`. Это регулярное выражение сопоставляет символьные литералы `hrf-`, но не учитывает регистр и варианты языка и региональных параметров. Оно ищет цифры 0–9 (всего 6 цифр).

HRF означает `human resources form`.

LUIS помечает высказывания, добавляемые в намерение. При разметке этих фраз добавляются пробелы до и после дефиса (`Where is HRF - 123456?`). Регулярное выражение применяется к фразам в необработанной форме до выполнения разметки. Так как он применяется к _необработанные_ формы, регулярное выражение не имеет дело с границами слов. 

Создайте сущность регулярного выражения, чтобы сообщить LUIS формат номера HRF, выполнив следующие шаги:

1. В области слева выберите **Entities** (Сущности).

2. Нажмите кнопку **Create new entity** (Создать сущность) на странице "Сущности". 

3. Во всплывающем диалоговом окне введите имя новой сущности `HRF-number` и выберите **RegEx** как тип сущности, а затем введите `hrf-[0-9]{6}` в качестве значения **RegEx** и нажмите кнопку **Готово**.

    ![Снимок экрана всплывающего диалогового окна, где задаются свойства новой сущности](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Выберите **Intents** (Намерения) в меню слева, а затем намерение **FindForm**, чтобы увидеть регулярное выражение, помеченное в высказывании. 

    [![Снимок экрана помеченной фразы с имеющейся сущностью и шаблоном регулярного выражения](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Так как сущность не обучена с помощью машинного обучения, метка применяется к фразам и отображается на веб-сайте LUIS сразу же после создания.

## <a name="train"></a>Train

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Опубликовать

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Получение намерения и сущностей из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `When were HRF-123456 and hrf-234567 published in the last year?`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `FindForm` с двумя номерами форм: `HRF-123456` и `hrf-234567`.

    ```JSON
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    Номера во фразе возвращаются дважды: один раз как новая сущность `hrf-number` и один раз в качестве предварительно созданной сущности `number`. Как показывает этот пример, фраза может иметь несколько сущностей, которые могут принадлежать к одному типу. С помощью сущности регулярного выражения LUIS извлекает именованные данные, что программно более полезно для клиентского приложения, получающего ответ JSON.


## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как создать новое намерение, добавить примеры высказываний, а затем создать сущность регулярного выражения для извлечения данных в правильном формате из высказывания. После обучения и публикации приложения, запрос к конечной точке идентифицировал намерение и вернул извлеченные данные.

> [!div class="nextstepaction"]
> [Дополнительные сведения о списке сущности](luis-quickstart-intent-and-list-entity.md)

