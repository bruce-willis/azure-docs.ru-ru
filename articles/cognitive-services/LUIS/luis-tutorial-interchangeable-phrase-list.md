---
title: Руководство по улучшению прогнозирования в LUIS с помощью списка фраз в Azure | Документы Майкрософт
description: В этом руководстве описано добавление списка фраз в приложение LUIS и просмотр повышения оценки.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2017
ms.author: v-geberr
ms.openlocfilehash: feb8acb674fd2dc62b62c26da6a6b42515f30242
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265977"
---
# <a name="tutorial-add-phrase-list-to-improve-predictions"></a>Руководство по улучшению прогнозирования в LUIS с помощью списка фраз
В этом руководстве приводятся действия по повышению точности оценок намерений и определению сущностей для слов, которые имеют одинаковое значение (синонимы), путем добавления взаимозаменяемого [списка фраз](./luis-concept-feature.md).

> [!div class="checklist"]
* Импорт нового приложения  
* Запрос конечной точки с известным высказыванием 
* Запрос конечной точки с _неизвестным_ высказыванием
* Добавление списка фраз для улучшения оценки неизвестного высказывания
* Проверка обнаружения сущности при использовании списка фраз

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="import-a-new-app"></a>Импорт нового приложения
1. Скачайте [пример приложения LUIS][LuisSampleApp], предназначенный для этого руководства. Он понадобится на следующем шаге. 

2. Как описано в статье о [создании приложения](Create-new-app.md#import-new-app), импортируйте скачанный файл на веб-сайт [LUIS][LUIS] в качестве нового приложения. Имя приложения — "My Phrase List tutorial". В приложении есть намерения, сущности и высказывания. 

3. [Обучите](luis-how-to-train.md) приложение. Пока приложение не будет обучено, вы не сможете [проверить его в интерактивном режиме](interactive-test.md#interactive-testing) на веб-сайте [LUIS][LUIS]. 

4. На странице [Publish](PublishApp.md) (Публикация) установите флажок **Include all predicted intent scores** (Включить все прогнозируемые оценки намерения). Если флажок установлен, возвращаются все намерения. Если флажок снят, возвращается только намерение с высокой оценкой. 

5. [Опубликуйте](PublishApp.md) приложение. Опубликованное приложение можно протестировать с помощью конечной точки HTTPS. 

## <a name="test-a-trained-utterance"></a>Тестирование обученного высказывания
С помощью опубликованной конечной точки выполните запрос к высказыванию, которое уже известно приложению. Так как LUIS уже знает высказывание, выставляется высокая оценка и сущность обнаруживается.

1. На веб-сайте [Language Understanding (LUIS)][LUIS] на странице **Publish** (Публикация) нового приложения в разделе **Resources and Keys** (Ресурсы и ключи) выберите URL-адрес конечной точки. 

    ![Публикация URL-адреса конечной точки](./media/luis-tutorial-interchangeable-phrase-list/luis-publish-url.png)

2. В браузере в конце URL-адреса добавьте следующий запрос после `q=`.

    `I want a computer replacement`

    Конечная точка выведет следующий JSON:
    
    ```JSON
    {
      "query": "I want a computer replacement",
      "topScoringIntent": {
        "intent": "GetHardware",
        "score": 0.9735458
      },
      "intents": [
        {
          "intent": "GetHardware",
          "score": 0.9735458
        },
        {
          "intent": "None",
          "score": 0.07053033
        },
        {
          "intent": "Whois",
          "score": 0.03760778
        },
        {
          "intent": "CheckCalendar",
          "score": 0.02285902
        },
        {
          "intent": "CheckInventory",
          "score": 0.0110072717
        }
      ],
      "entities": [
        {
          "entity": "computer",
          "type": "Hardware",
          "startIndex": 9,
          "endIndex": 16,
          "score": 0.8465915
        }
      ]
    }
    ```

    Оценка намерения (0,973) и оценка обнаружения сущности (0,846) являются высокими, так как приложение было обучено с помощью этого высказывания. Высказывание находится в приложении LUIS на странице намерения для **GetHardware**. Текст высказывания `computer` помечен как сущность **Hardware**. 
    
    |Status|Word| Оценка намерения | Оценка сущности |
    |--|--|--|--|
    |Обучение пройдено| want | 0,973 | 0,846 |
    
    
## <a name="test-an-untrained-utterance"></a>Тестирование необученного высказывания
В браузере используйте ту же опубликованную конечную точку, чтобы выполнить запрос с высказыванием, которое пока неизвестно приложению:

`I require a computer replacement`

В этом высказывании используется синоним предыдущего высказывания:

| Обученное слово | Необученный синоним |
|--|--|
| want | require |

Ответ конечной точки:

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.840912163
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.840912163
    },
    {
      "intent": "None",
      "score": 0.0972757638
    },
    {
      "intent": "Whois",
      "score": 0.0448251367
    },
    {
      "intent": "CheckCalendar",
      "score": 0.0291390456
    },
    {
      "intent": "CheckInventory",
      "score": 0.0137837809
    }
  ],
  "entities": []
}
```

| Status | Word | Оценка намерения | Оценка сущности |
|--|--|--|--|
| Обучение пройдено| want | 0,973 | 0,846 |
| Обучение не пройдено| require | 0,840 | - |

Необученное намерение имеет более низкую оценку, чем намерение помеченного высказывания, так как LUIS известно, что предложение грамматически то же. Но LUIS не знает, что высказывания имеют одинаковое значение. Кроме того, без списка фраз сущность **Hardware** не найдена.

Необходимо обучить LUIS, что слова *want* и *require* значат то же самое в этой предметной области приложения, поскольку слово может иметь несколько значений. 

## <a name="improve-the-score-of-untrained-utterance-with-phrase-list"></a>Повышение оценки необученного высказывания с помощью списка фраз 
1. Добавьте компонент [списка фраз](luis-how-to-add-features.md) с именем **want** и значением `want`, а затем нажмите клавишу **ВВОД**.

    > [!TIP]
    > Нажимайте клавишу **ВВОД** после каждого слова или фразы. Слово или фраза добавляются в поле **Phrase list values** (Значения списка фраз), а курсор остается в поле **Значение**. С помощью этой функции можно быстро ввести много значений.

2. Чтобы просмотреть слова, которые рекомендует LUIS, выберите **Recommend** (Рекомендуемые). 

    ![Рекомендуемые значения](./media/luis-tutorial-interchangeable-phrase-list/recommend.png)

3. Добавьте все слова. Если `require` отсутствует в списке рекомендуемых значений, добавьте его в качестве обязательного значения. 

4. Так как эти слова являются синонимами, оставьте параметр *взаимозаменяемости*, а затем нажмите кнопку **Сохранить**.

    ![Значения списка фраз](./media/luis-tutorial-interchangeable-phrase-list/phrase-list-values.png)

5. В верхней панели навигации нажмите кнопку **Train** (Обучить), чтобы обучить приложение, но не публиковать его. Теперь у вас есть две модели. Вы можете сравнить значения в этих двух моделях.

## <a name="compare-the-phrase-list-model-to-the-published-model"></a>Сравнение модели списка фраз с опубликованной моделью
В этом приложении опубликованная модель не прошла обучение с использованием синонимов. Список фраз синонимов входит только в текущую редактируемую модель. Чтобы сравнить модели, используйте [интерактивное тестирование](interactive-test.md#interactive-testing). 

1. Откройте область **Test** (Тест) и введите следующее высказывание:

    `I require a computer replacement`

2. Чтобы открыть панель проверки, нажмите кнопку **Inspect** (Проверить). 

    ![Нажатие кнопки проверки](./media/luis-tutorial-interchangeable-phrase-list/inspect-button.png)

3. Чтобы сравнить опубликованную модель с новой моделью списка фраз, выберите **Compare with published** (Сравнить с опубликованной).

    ![Сравнение опубликованной модели с текущей](./media/luis-tutorial-interchangeable-phrase-list/inspect.png)

После добавления списка фраз определяется повышенная точность высказывания и обнаруживается сущность **Hardware**. 

|Status | Список фраз| Оценка намерения | Оценка сущности |
|--|--|--|--|
| Опубликовано | - | 0,84 | - |
| Выполняется редактирование |✔| 0,92 | Определена сущность Hardware |

> [!TIP]
> * С помощью [интерактивного тестирования](interactive-test.md#interactive-testing) опубликованную модель можно сравнивать с любыми обученными изменениями, внесенными после публикации. 
> * С помощью [тестирования конечной точки](PublishApp.md#test-your-published-endpoint-in-a-browser) можно просмотреть точный JSON ответа LUIS. 

## <a name="get-the-entity-score-with-the-endpoint-test"></a>Получение оценки сущности с помощью теста конечной точки
Чтобы просмотреть оценку сущности, [опубликуйте модель](PublishApp.md) и выполните запрос к конечной точке. 

`I require a computer replacement`

```JSON
{
  "query": "I require a computer replacement",
  "topScoringIntent": {
    "intent": "GetHardware",
    "score": 0.916503668
  },
  "intents": [
    {
      "intent": "GetHardware",
      "score": 0.916503668
    },
    {
      "intent": "None",
      "score": 0.136505231
    },
    {
      "intent": "Whois",
      "score": 0.02778677
    },
    {
      "intent": "CheckInventory",
      "score": 0.0144592477
    },
    {
      "intent": "CheckCalendar",
      "score": 0.01401332
    }
  ],
  "entities": [
    {
      "entity": "computer",
      "type": "Hardware",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.5959917
    }
  ]
}
```

Для сущности **Hardware** отображается оценка 0,595 со списком фраз. До появления списка фраз сущность не была обнаружена. 

|Status | Список фраз| Оценка намерения | Оценка сущности |
|--|--|--|--|
| Опубликовано | - | 0,84 | - |
| Выполняется редактирование |✔| 0,92 | 0,595 |


## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Чтобы сделать это, щелкните меню с тремя точками (...) справа от имени приложения в списке приложений и выберите пункт **Delete** (Удалить). Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Получение прогнозирования фраз с помощью запроса конечной точки](luis-get-started-cs-get-intent.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
[LuisFeatures]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[LuisSampleApp]: https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/phrase_list/interchangeable/luis-app-before-phrase-list.json
