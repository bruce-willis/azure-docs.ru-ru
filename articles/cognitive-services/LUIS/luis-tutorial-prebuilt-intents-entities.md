---
title: Добавление предварительно созданных намерений и сущностей для извлечения общих данных в службе Language Understanding в Azure | Документы Майкрософт
description: Узнайте, как использовать предварительно созданные намерения и сущности для извлечения различных типов данных сущностей.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 0ec6f002b35b1224118b62accda1f69e7be22fb8
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358528"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>Учебник: 2. Добавление предварительно созданных намерений и сущностей
Добавьте предварительно созданные намерения и сущности в учебное приложение Human Resources, чтобы быстро осуществлять прогнозирование намерений и извлекать данные. 

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
* Добавление предварительно созданных намерений 
* Добавление предварительно созданных сущностей datetimeV2 и number
* Обучение и публикация
* Запрос LUIS и получение ответа прогнозирования

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения [Human Resources](luis-quickstart-intents-only.md), созданного в предыдущем руководстве, [импортируйте](luis-how-to-start-new-app.md#import-new-app) файл JSON из [репозитория GitHub с примерами LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website).

Чтобы сохранить исходное приложение по управлению персоналом, клонируйте версию приложения на странице [Параметры](luis-how-to-manage-versions.md#clone-a-version) и назовите ее `prebuilts`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. 

## <a name="add-prebuilt-intents"></a>Добавление предварительно созданных намерений
В службе LUIS доступно несколько предварительно созданных намерений, что упрощает процедуру выбора.  

1. Убедитесь, что приложение находится в разделе **Build** (Создание) службы LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

2. Щелкните **Add prebuilt domain intent** (Добавить предварительно созданное намерение предметной области). 

    [ ![Снимок экрана страницы намерений с выделенной кнопкой добавления предварительно созданного намерения предметной области](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

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

    [ ![Снимок экрана со списком намерений, где кнопка "Сущности" выделена в меню навигации слева](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Нажмите кнопку **Manage prebuilt entities** (Управление предварительно созданными сущностями).

    [ ![Снимок экрана со списком списка "Сущности" с выделенной кнопкой "Manage prebuilt entities" (Управление предварительно созданными сущностями)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. В списке предварительно созданных сущностей выберите **number** и **datetimeV2**, а затем нажмите кнопку **Done** (Готово).

    ![Снимок экрана выбора сущности number в диалоговом окне предварительно созданных сущностей](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Обучение и публикация приложения

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Публикация приложения в конечной точке

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-endpoint-with-an-utterance"></a>Запрос конечной точки с высказыванием

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `I want to cancel on March 3`. Последний параметр строки запроса — `q`. Это **запрос** высказывания. 

    Результат прогнозируется в намерении Utilities.Cancel и извлекается дата "3 марта" и число 3. 

    ```
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

    Для даты "3 марта" существует два значения, так как из высказывания неясно, находится ли 3 марта в прошлом или в будущем. Приложение, вызывающее LUIS, должно самостоятельно предположить, в прошлом или в будущем находится эта дата, а в случае необходимости — уточнить это. 

    Легко и быстро добавляя предварительно созданные намерения и сущности, клиентское приложение может добавлять управление беседами и извлекать общие типы данных. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление сущности регулярного выражения в приложение](luis-quickstart-intents-regex-entity.md)

