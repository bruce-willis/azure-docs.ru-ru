---
title: Руководство 8. Извлечение ключевых фраз в LUIS
titleSuffix: Azure Cognitive Services
description: Используйте предварительно созданную сущность keyPhrase, чтобы извлекать запрашиваемое содержимое из фраз. Не нужно помечать высказывания с использованием предварительно созданных сущностей. Сущность определяется автоматически.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 8fa183c22b9b6830c57b0a16b7f5d20ca38e3ef3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166526"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>Руководство 8. Извлечение ключевых фраз из высказывания
В рамках этого руководства используйте предварительно созданную сущность keyPhrase, чтобы извлечь содержимое из высказываний. Не нужно помечать высказывания с использованием предварительно созданных сущностей. Сущность определяется автоматически.

В следующих фразах содержатся примеры ключевых фраз.

|Фраза|Значения сущности keyPhrase|
|--|--|
|Имеется ли новый план медицинского обслуживания с меньшими отчислениями на следующий год?|"меньшие отчисления"<br>"новый план медицинского обслуживания"<br>"год"|
|Предусматривает ли план медицинского обслуживания с высокими отчислениями терапию для улучшения зрения?|"План медицинского обслуживания с высокими отчислениями"<br>"терапия для улучшения зрения"|

Клиентское приложение может использовать эти значения вместе с другими извлеченными сущностями, чтобы определить следующий шаг в диалоге.

**Из этого руководства вы узнаете, как выполнить следующие задачи:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Использовать существующее приложения из руководства.
> * Добавление сущности keyPhrase 
> * Train
> * Опубликовать
> * Получать намерения и сущности из конечной точки.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Использовать существующее приложение.

Продолжите работу с приложением (**HumanResources**), созданным в соответствии с инструкциями из предыдущего руководства. 

Если у вас нет приложения HumanResources из предыдущего руководства, сделайте следующее:

1.  Скачайте и сохраните [JSON-файл приложения](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json).

2. Импортируйте JSON-файл в новое приложение.

3. В разделе **Управление** на вкладке **Версии** клонируйте версию и присвойте ей имя `keyphrase`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. Так как имя версии используется в составе URL-адреса, оно не должно содержать символы, недопустимые для URL-адресов.

## <a name="add-keyphrase-entity"></a>Добавление сущности keyPhrase 
Добавьте предварительно созданную сущность keyPhrase для извлечения запрашиваемого содержимого из фраз.

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Выберите **Entities** (Сущности) в меню слева.

3. Выберите **Manage prebuilt entities** (Управление предварительно созданными сущностями).

4. Во всплывающем диалоговом окне выберите **keyPhrase**, а затем нажмите кнопку **Done** (Готово). 

    [ ![Снимок экрана всплывающего диалогового окна, содержащего список сущностей](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. В меню слева выберите **Intents** (Намерения) и щелкните **Utilities.Confirm**. Сущность keyPhrase помечена в нескольких фразах. 

    [ ![Снимок экрана с намерением Utilities.Confirm и сущностями keyPhrase, помеченными в фразах](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>Train

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Опубликовать

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Получение намерения и сущностей из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `does form hrf-123456 cover the new dental benefits and medical plan`. Последний параметр строки запроса — `q`. Это **запрос** фразы. 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    При поиске формы пользователь указал больше информации, чем требовалось. Дополнительные сведения возвращаются как **builtin.keyPhrase**. Клиентское приложение может использовать эти сведения для уточняющих вопросов, например "Вам хотелось бы обсудить новую стоматологическую страховку с сотрудником отдела кадров?" или предоставить меню с дополнительными вариантами, включая "Дополнительные сведения о новой стоматологической страховке или плане медицинского обслуживания".

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как добавить предварительно созданную сущность keyPhrase, которая позволяет быстро получить ключевые фразы из высказываний без необходимости помечать эти высказывания. 

> [!div class="nextstepaction"]
> [Добавление анализа тональности в приложение](luis-quickstart-intent-and-sentiment-analysis.md)