---
title: Добавление предварительно созданных намерений и сущностей для извлечения общих данных в службе Language Understanding в Azure | Документы Майкрософт
description: Узнайте, как использовать предварительно созданные намерения и сущности для извлечения различных типов данных сущностей.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 06/11/2018
ms.author: v-geberr
ms.openlocfilehash: 20950ced66497fb0dc96365975b37f244f677ce3
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266385"
---
# <a name="use-prebuilt-intents-and-entities-to-handle-common-intents-and-data"></a>Использование предварительно созданных намерений и сущностей для обработки общих намерений и данных
Добавьте предварительно созданные намерения и сущности в приложение Human Resources, чтобы быстро осуществлять прогнозирование намерений и извлекать данные. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
* Добавление предварительно созданных намерений 
* Добавление предварительно созданных сущностей datetimeV2 и number
* Обучение и публикация
* Запрос LUIS и получение ответа прогнозирования

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения Human Resources, созданного с помощью руководства по [настраиваемым предметным областям](luis-quickstart-intents-only.md), [импортируйте](create-new-app.md#import-new-app) JSON-файл из репозитория GitHub с [примерами LUIS](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) в новое приложение на веб-сайте [LUIS][LUIS].

Чтобы сохранить исходное приложение по управлению персоналом, клонируйте версию приложения на странице [Параметры](luis-how-to-manage-versions.md#clone-a-version) и назовите ее `prebuilts`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. 

## <a name="add-prebuilt-intents"></a>Добавление предварительно созданных намерений
В службе LUIS доступно несколько предварительно созданных намерений, что упрощает процедуру выбора.  

1. Убедитесь, что приложение находится в разделе **Build** (Создание) службы LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

    [ ![Снимок экрана приложения LUIS со вкладкой "Build" (Создание), выделенной в верхней правой строке навигации](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. Щелкните **Add prebuilt domain intent** (Добавить предварительно созданное намерение предметной области). 

    [ ![Снимок экрана страницы намерений с выделенной кнопкой добавления предварительно созданного намерения предметной области](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png) ](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. Выполните поиск `Utilities`. 

    [ ![Снимок экрана диалогового окна предварительно созданных намерений со служебными программами в поле поиска](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Выберите следующие намерения и нажмите кнопку **Done** (Готово): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.Stop
    * Utilities.StartOver

## <a name="add-prebuilt-entities"></a>Добавление предварительно созданных сущностей
LUIS предоставляет несколько предварительно созданных сущностей для извлечения общих данных. 

1. Выберите **Сущности** в меню навигации слева.

    [ ![Снимок экрана со списком намерений, где кнопка "Сущности" выделена в меню навигации слева](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. Нажмите кнопку **Manage prebuilt entities** (Управление предварительно созданными сущностями).

    [ ![Снимок экрана со списком списка "Сущности" с выделенной кнопкой "Manage prebuilt entities" (Управление предварительно созданными сущностями)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. В списке предварительно созданных сущностей выберите **number** и **datetimeV2**, а затем нажмите кнопку **Done** (Готово).

    ![Снимок экрана выбора сущности number в диалоговом окне предварительно созданных сущностей](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>Обучение и публикация приложения
1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить). 

    ![Кнопка обучения](./media/luis-quickstart-intents-only/train-button.png)

    Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Строка состояния "Обучение пройдено"](./media/luis-quickstart-intents-only/trained.png)

2. В верхней правой части веб-сайта LUIS нажмите кнопку **Publish** (Опубликовать), чтобы открыть страницу публикации. По умолчанию выбран рабочий слот. Если вам нужен этот слот, нажмите кнопку **Publish** (Опубликовать). Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    Перед публикацией или перед тестированием URL-адреса конечной точки не требуется создавать ключ LUIS Azure. Каждое приложение LUIS имеет бесплатный начальный ключ для разработки. Он предоставляет неограниченное число обращений для разработки и [несколько для конечных точек](luis-boundaries.md#key-limits). 

## <a name="query-endpoint-with-an-utterance"></a>Запрос конечной точки с высказыванием
В нижней части страницы **Публикация** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. Перейдите в конец URL-адреса и введите `I want to cancel on March 3`. Последний параметр строки запроса — `q`. Это **запрос** высказывания. 

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

Легко и быстро добавляя предварительно созданные намерения и сущности, клиентское приложение может добавлять управление беседами и извлекать общие типы данных. 

## <a name="next-steps"></a>Дополнительная информация

[Узнайте больше о сущностях](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
