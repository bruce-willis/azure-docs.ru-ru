---
title: 'Руководство 2: Предварительно созданные намерения и сущности. Использование предварительно построенных общих высказывании. Извлечение общих данных в LUIS'
titleSuffix: Azure Cognitive Services
description: Добавьте предварительно созданные намерения и сущности в учебное приложение Human Resources, чтобы быстро осуществлять прогнозирование намерений и извлекать данные. Не нужно помечать высказывания с использованием предварительно созданных сущностей. Сущность определяется автоматически.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: d42aed76ecdbc2bd840e17517db2ca0b6ba11aa0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034439"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>Руководство 2: Определение общих намерений и сущностей
В этом руководстве измените приложение "Управление персоналом". Добавьте предварительно созданные намерения и сущности в учебное приложение Human Resources, чтобы быстро осуществлять прогнозирование намерений и извлекать данные. Вам не нужно указывать какие-либо высказывания с заранее созданными сущностями, потому что они обнаруживаются автоматически.

Предварительно созданные модели общих предметных доменов и типов данных дают возможность быстрого создания модели, а также привести пример того, как она выглядит. 

**Из этого руководства вы узнали, как выполнять такие задачи:**

> [!div class="checklist"]
> * Использовать существующее приложение из руководства
> * Добавление предварительно созданных намерений 
> * Добавление предварительно созданных сущностей 
> * Train 
> * Опубликовать 
> * Получение намерений и сущностей из конечной точки

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Использование существующего приложения
Продолжите работу с приложением **Управление персоналом**, созданным в соответствии с инструкциями из предыдущего руководства. 

Если у вас нет этого приложения сделайте следующее:

1.  Загрузите и сохраните [JSON-файл приложения](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json).

2. Импортируйте JSON-файл в новое приложение.

3. Из раздела **Управление** на вкладке **Версии**, скопируйте версию и назовите ее `prebuilts`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. Так как имя версии используется в составе URL-адреса, оно не должно содержать символы, недопустимые для URL-адресов. 

## <a name="add-prebuilt-intents"></a>Добавление предварительно созданных намерений
В службе LUIS доступно несколько предварительно созданных намерений, что упрощает процедуру выбора.  

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Выберите **Добавление предварительно созданных намерений**. 

3. Выполните поиск `Utilities`. 

    [ ![Снимок экрана диалогового окна предварительно созданных намерений со служебными программами в поле поиска](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Выберите следующие намерения и нажмите кнопку **Done** (Готово): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Добавление предварительно созданных сущностей
LUIS предоставляет несколько предварительно созданных сущностей для извлечения общих данных. 

1. Выберите **Сущности** в меню навигации слева.

2. Нажмите кнопку **Manage prebuilt entities** (Управление предварительно созданными сущностями).

3. В списке предварительно созданных сущностей выберите **number** и **datetimeV2**, а затем нажмите кнопку **Done** (Готово).

    ![Снимок экрана выбора сущности number в диалоговом окне предварительно созданных сущностей](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>Train

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Опубликовать

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Получение намерения и сущностей из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса в адресной строке браузера и введите `I want to cancel on March 3`. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

    ```JSON
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    Результат прогнозируется в намерении Utilities.Cancel и извлекается дата "3 марта" и число 3. 

    Для даты "3 марта" существует два значения, так как из высказывания неясно, находится ли 3 марта в прошлом или в будущем. Клиентское приложение должно самостоятельно предположить, в прошлом или в будущем находится эта дата, а в случае необходимости — уточнить это. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Добавляя предварительно созданные намерения и сущности, клиентское приложение может определить общие намерения пользователя и извлекать общие типы данных. 

> [!div class="nextstepaction"]
> [Добавление сущности регулярного выражения в приложение](luis-quickstart-intents-regex-entity.md)

