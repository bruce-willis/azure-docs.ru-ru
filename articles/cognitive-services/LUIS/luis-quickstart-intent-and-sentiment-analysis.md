---
title: Руководство 9. Анализ положительной, отрицательной и нейтральной тональности в LUIS
titleSuffix: Azure Cognitive Services
description: В этом руководстве создается приложение, демонстрирующее, как извлечь позитивные, негативные и нейтральные тональности из фраз. Тональность определяется на основе всего высказывания.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: ff5a47f977f34535c5ad1fde7e6cac5995e7f7dd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031464"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>Руководство 9. Извлечение тональности из всего высказывания
В этом руководстве создается приложение, демонстрирующее, как извлечь позитивные, негативные и нейтральные тональности из фраз. Тональность определяется на основе всего высказывания.

Анализ тональности — это возможность определить, является ли фраза пользователя позитивной, негативной или нейтральной. 

Ниже приведены примеры фраз, передающие тональность.

|Мнение|Оценка|Фраза|
|:--|:--|:--|
|Позитивная|0,91 |Тарас Хмелев проделал отличную работу на презентации в Париже.|
|Позитивная|0,84 |Сотрудник jill-jones@mycompany.com отлично поработал над презентацией Parker.|

Анализ тональности — это параметр публикации, который применяется к каждому высказыванию. Вам не нужно искать и выделять слова, указывающие на тональность в каждой фразе, так как анализ тональности применяется ко всей фразе целиком. 

Так как это параметр публикации, вы не увидите его на страницах намерений или сущностей. Он будет отображаться в области [интерактивного тестирования](luis-interactive-test.md#view-sentiment-results) или при тестировании на странице по URL-адресу конечной точки. 

**Из этого руководства вы узнаете, как выполнить следующие задачи:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Использование существующего приложения из руководства. 
> * Добавление анализа тональности как параметра публикации.
> * Train
> * Опубликовать
> * Получение данных о тональности высказывания из конечной точки.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Использование существующего приложения

Продолжите работу с приложением **HumanResources**, созданным в рамках последнего руководства. 

Если у вас нет приложения HumanResources из предыдущего руководства, сделайте следующее:

1.  Скачайте и сохраните [JSON-файл приложения](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json).

2. Импортируйте JSON-файл в новое приложение.

3. В разделе **Управление** на вкладке **Версии** клонируйте версию и присвойте ей имя `sentiment`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. Так как имя версии используется в маршруте URL-адреса, оно не может содержать символы, которые недопустимы в URL-адресе.

## <a name="employeefeedback-intent"></a>Намерение EmployeeFeedback 
Добавьте новое намерение, чтобы получить отзывы о сотрудниках от других работников компании. 

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Выберите **Create new intent**. (Создать намерение).

3. В качестве имени нового намерения введите `EmployeeFeedback`.

    ![Диалоговое окно для создания намерения с именем EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Добавьте несколько выражений, чтобы указать на хорошую работу сотрудника или область, в которой требуются улучшения:

    Помните, что в этом приложении для управления персоналом сотрудники определяются в сущности списка `Employee` по имени, электронной почте, добавочному номеру телефона, номеру мобильного телефона и номеру социального страхования в США. 

    |Высказывания|
    |--|
    |Сотрудник 425-555-1212 хорошо проявил себя при организации встречи для коллеги, вернувшейся из декретного отпуска.|
    |Сотрудник 234-56-7891 хорошо проявил себя, оказав поддержку коллеге во время траура.|
    |Сотрудник jill-jones@mycompany.com не располагал всеми необходимыми счетами для оформления документов.|
    |Сотрудник john.w.smith@mycompany.com вернул необходимые бланки с опозданием на месяц и без подписей.|
    |Сотрудник x23456 не явился на важную выездную встречу маркетологов.|
    |Сотрудник x12345 пропустил собрание для обсуждения показателей за июнь.|
    |Мария Шевченко подготовила прекрасное коммерческое предложение для встречи в Лондоне.|
    |Тарас Хмелев проделал отличную работу на презентации в Мюнхене.|

    [ ![Снимок экрана с приложением LUIS и примерами фраз в намерении EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train"></a>Train

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Настройка приложения для включения анализа тональности
1. На правой верхней панели навигации выберите **Управление** и щелкните **Параметры публикации** в меню слева.

2. Переключите параметр **Анализ тональности**, чтобы включить его. 

    ![](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>Опубликовать

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>Получение данных о тональности высказывания из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `Jill Jones work with the media team on the public portal was amazing`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `EmployeeFeedback` с извлечением анализа тональности.
    
    ```JSON
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.4983256
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.4983256
        },
        {
          "intent": "MoveEmployee",
          "score": 0.06617523
        },
        {
          "intent": "GetJobInformation",
          "score": 0.04631853
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0103248553
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.007531875
        },
        {
          "intent": "FindForm",
          "score": 0.00344597152
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00337914471
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0026357458
        },
        {
          "intent": "None",
          "score": 0.00214573368
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00157622492
        },
        {
          "intent": "Utilities.Confirm",
          "score": 7.379545E-05
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 0,
          "endIndex": 9,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "media team",
          "type": "builtin.keyPhrase",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "public portal",
          "type": "builtin.keyPhrase",
          "startIndex": 43,
          "endIndex": 55
        },
        {
          "entity": "jill jones",
          "type": "builtin.keyPhrase",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    Анализ тональности является положительным с оценкой 0,86. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация
В рамках этого руководства вы добавили анализ тональности как параметр публикации, чтобы извлечь значения тональности из всего высказывания.

> [!div class="nextstepaction"] 
> [Проверка фрагментов речи конечной точки в приложении для управления персоналом](luis-tutorial-review-endpoint-utterances.md) 

