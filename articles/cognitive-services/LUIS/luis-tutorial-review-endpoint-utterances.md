---
title: Руководство 1. Проверка высказываний конечной точки с помощью активного обучения
titleSuffix: Azure Cognitive Services
description: Повышение точности прогнозирования приложения путем проверки или корректировки высказываний, полученных через неизвестную для LUIS конечную точку HTTP интеллектуальной службы распознавания речи. Некоторые высказывания могут быть проверены на наличие намерений, а другие — на наличие сущностей. Высказывания конечных точек необходимо проверять в рамках планового обслуживания LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 1047c117228b57f7361a1e386bc6cde7acbfdde8
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042282"
---
# <a name="tutorial-1-fix-unsure-predictions"></a>Руководство 1. Исправление неточных прогнозов
Из этого руководства вы узнаете, как повысить точность прогнозирования приложения, проверяя или корректируя высказывания, полученные через неизвестную для LUIS конечную точку HTTP интеллектуальной службы распознавания речи. Некоторые высказывания необходимо проверять на наличие намерений, а другие — на наличие сущностей. Высказывания конечных точек необходимо проверять в рамках планового обслуживания LUIS. 

Этот процесс проверки — еще один способ обучить LUIS для использования вашего домена приложений. В LUIS выбираются те высказывания, которые отображаются в списке проверки. Этот список:

* относится к конкретному приложению;
* предназначен для повышения точности прогнозирования в приложении; 
* должен периодически проверяться. 

Проверив фрагменты речи конечной точки, вы подтверждаете или исправляете прогнозируемое намерение фрагмента речи. Кроме того, вы отмечаете настраиваемые сущности, которые не вошли в прогноз или были неверно спрогнозированы. 

**Из этого руководства вы узнали, как выполнять такие задачи:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Использовать существующее приложение из руководства
> * Просмотр фрагментов речи конечной точки
> * Обновлять список фраз
> * Обучать приложения
> * Публикация приложения
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Использование существующего приложения

Продолжите работу с приложением **HumanResources**, созданным в рамках последнего руководства. 

Если у вас нет приложения HumanResources из предыдущего руководства, сделайте следующее:

1.  Загрузите и сохраните [JSON-файл приложения](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-sentiment-HumanResources.json).

2. Импортируйте JSON-файл в новое приложение.

3. В разделе **Управление** на вкладке **Версии** клонируйте версию и присвойте ей имя `review`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. Так как имя версии используется в маршруте URL-адреса, оно не может содержать символы, которые недопустимы в URL-адресе.

    Если вы используете при работе с этим руководством новое импортированное приложение, для фрагментов речи нужно выполнить обучение и публикацию. Затем необходимо добавить фрагменты в конечную точку с помощью [скрипта](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) или из конечной точки в браузере. Фрагменты речи, которые нужно добавить:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

    Если у вас есть все версии приложения, созданные в рамках серии руководств, возможно, вас удивит, что список **проверки фрагментов речи конечной точки** не изменяется в зависимости от версии. Для проверки используется единственный пул высказываний независимо от того, какую версию вы активно редактируете или какая версия приложения опубликована в конечной точке. 

## <a name="review-endpoint-utterances"></a>Просмотр фрагментов речи конечной точки

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Выберите **Review endpoint utterances** (Проверить фрагменты речи конечной точки) на панели навигации слева. Список будет отфильтрован для намерения **ApplyForJob**. 

    [ ![Снимок экрана с кнопкой Review endpoint utterances (Проверить фрагменты речи конечной точки) на панели навигации слева](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png#lightbox)

3. Переключите **представление сущностей**, чтобы просмотреть отмеченные сущности. 
    
    [ ![Снимок экрана с элементом Review endpoint utterances (Проверить фрагменты речи конечной точки) и выделенным переключателем представления сущностей](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png)](./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-token-view.png#lightbox)

    |Фраза|Правильное намерение|Отсутствующие сущности|
    |:--|:--|:--|
    |Я ищу вакансию с обработкой естественного языка|GetJobInfo|Job — "Обработка естественного языка"|

    Этот фрагмент речи не является правильным намерением. Его оценка составляет менее 50 %. Для намерения **ApplyForJob** используется 21 фрагмент речи, а для **GetJobInformation** — 7. Чтобы правильно упорядочить количество фрагментов речи конечной точки, для намерения **GetJobInformation** нужно добавить больше фрагментов речи. Вы можете сделать это самостоятельно, чтобы попрактиковаться. Для каждого намерения, кроме **None**, должно использоваться приблизительно одинаковое количество примеров фрагментов речи. Число фрагментов речи с намерением **None** должно составлять 10 % от общего числа фрагментов речи в приложении. 

4. Для намерения `I'm looking for a job with Natual Language Processing` выберите правильное намерение **GetJobInformation** в столбце **Aligned intent** (Упорядоченное намерение). 

    [ ![Снимок экрана элемента Review endpoint utterances (Проверить фрагменты речи конечной точки) с упорядочиванием фрагмента речи для намерения](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

5. В том же фрагменте фрагмента речи сущностью для `Natural Language Processing` является keyPhrase. Вместо нее должна использоваться сущность **Job**. Выберите `Natural Language Processing`, а затем выберите в списке сущность **Job**.

    [ ![Снимок экрана с элементом Review endpoint utterances (Проверить фрагменты речи конечной точки) и отмеченной сущностью для фрагмента речи](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

6. В той же строке выберите обведенный кружком флажок в столбце **Add to aligned intent** (Добавить в список упорядоченных намерений). 

    [ ![Снимок экрана, демонстрирующий завершение упорядочивания фрагмента речи для намерения](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    После этого фрагмент речи перемещается из списка **проверки фрагментов речи конечной точки** в намерение **GetJobInformation**. Фрагмент речи теперь является примером для этого намерения. 

7. Просмотрите остальные фрагменты речи для этого намерения, отмечая их и при необходимости внося исправления в список **упорядоченных намерений**.

8. Если все фрагменты речи правильные, установите флажок для каждой строки, а затем выберите **Add selected** (Добавить выбранные), чтобы правильно упорядочить фрагменты речи. 

    [ ![Снимок экрана, демонстрирующий завершение упорядочения оставшихся фрагментов речи](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

9. В списке больше не должны содержаться эти фрагменты речи. Если появляется больше высказываний, продолжайте работу по списку, исправляя намерения и отмечая любые отсутствующие сущности, пока этот список не опустеет. 

10. Выберите следующее намерение в списке фильтрации и продолжайте исправлять фрагменты речи и отмечать сущности. Помните, что на последнем этапе обработки каждого намерения нужно либо выбрать **Add to aligned intent** (Добавить в список упорядоченных намерений) в строке фрагмента речи, либо установить флажок для каждого намерения и выбрать команду **Добавить выбранные** над таблицей.

    Продолжайте, пока список намерений и сущностей не опустеет. Это очень небольшое приложение. Проверка занимает несколько минут. 

## <a name="update-phrase-list"></a>Обновлять список фраз
Обновляйте список фраз, добавляя названия любых обнаруженных вакансий. 

1. Выберите **Phrase lists** (Списки фраз) на панели навигации слева.

2. Выберите список фраз **Jobs** (Вакансии).

3. Введите значение `Natural Language Processing` и выберите **Save** (Сохранить). 

## <a name="train"></a>Train

Приложение LUIS не узнает об изменениях, пока не будет обучено. 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Опубликовать

Если приложение импортировано, выберите **Sentiment analysis** (Анализ тональности).

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Получение намерения и сущностей из конечной точки

Попробуйте использовать фрагмент речи, близкий к исправленному. 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `Are there any natural language processing jobs in my department right now?`. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

  ```JSON
  {
    "query": "are there any natural language processing jobs in my department right now?",
    "topScoringIntent": {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    "intents": [
      {
        "intent": "GetJobInformation",
        "score": 0.9247605
      },
      {
        "intent": "ApplyForJob",
        "score": 0.129989788
      },
      {
        "intent": "FindForm",
        "score": 0.006438211
      },
      {
        "intent": "EmployeeFeedback",
        "score": 0.00408575451
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00194211153
      },
      {
        "intent": "None",
        "score": 0.00166400627
      },
      {
        "intent": "Utilities.Help",
        "score": 0.00118593348
      },
      {
        "intent": "MoveEmployee",
        "score": 0.0007885918
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.0006373631
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0005980781
      },
      {
        "intent": "Utilities.Confirm",
        "score": 3.719905E-05
      }
    ],
    "entities": [
      {
        "entity": "right now",
        "type": "builtin.datetimeV2.datetime",
        "startIndex": 64,
        "endIndex": 72,
        "resolution": {
          "values": [
            {
              "timex": "PRESENT_REF",
              "type": "datetime",
              "value": "2018-07-05 15:23:18"
            }
          ]
        }
      },
      {
        "entity": "natural language processing",
        "type": "Job",
        "startIndex": 14,
        "endIndex": 40,
        "score": 0.9869922
      },
      {
        "entity": "natural language processing jobs",
        "type": "builtin.keyPhrase",
        "startIndex": 14,
        "endIndex": 45
      },
      {
        "entity": "department",
        "type": "builtin.keyPhrase",
        "startIndex": 53,
        "endIndex": 62
      }
    ],
    "sentimentAnalysis": {
      "label": "positive",
      "score": 0.8251864
    }
  }
  }
  ```

  Правильное намерение спрогнозировано с высокой оценкой, а сущность **Job** определена как `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Можно ли вместо проверки добавить больше фрагментов речи? 
У вас может возникнуть вопрос, почему бы не добавить больше примеров фрагментов речи. Какова цель проверки фрагментов речи конечной точки? В реальном приложении LUIS фрагменты речи конечной точки поступают от пользователей после выбора и организации слов. Эти операции вы еще не выполняли. Если бы вы использовали выбор и организацию одинаковых слов, для исходного прогноза процент был бы выше. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Почему намерения с самым высоким показателем находятся в списке фрагментов речи? 
Некоторые высказывания конечной точки будут иметь высокую оценку прогнозирования в списке проверки. Их по-прежнему нужно проверить и подтвердить. Они входят в список, так как оценка следующего намерения с самым высоким показателем слишком близка к самой высокой оценке намерения. Между двумя верхними намерениями разница должна составлять около 15 %.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как просматривать высказывания, отправленные неизвестной для LUIS конечной точке. После того как эти высказывания будут проверены и перенесены в правильные намерения в качестве примеров высказываний, точность прогнозов LUIS увеличится.

> [!div class="nextstepaction"]
> [Сведения об использовании шаблонов](luis-tutorial-pattern.md)
