---
title: Руководство по созданию составной сущности для извлечения сложных данных в Azure | Документация Майкрософт
description: Узнайте, как создать составную сущность в приложении LUIS для извлечения различных типов данных сущности.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 9c84afc231ff4b086e76f50702870e30da7add6e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364908"
---
# <a name="tutorial-6-add-composite-entity"></a>Руководство: 6. Добавление составной сущности 
В этом руководстве описывается процедура добавления составной сущности для объединения извлеченных данных в содержащую сущность.

Из этого руководства вы узнаете, как выполнять следующие задачи:

<!-- green checkmark -->
> [!div class="checklist"]
> * Общие сведения о составных сущностях 
> * Добавление составной сущности для извлечения данных
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS.

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения управления персоналом из руководства по [иерархическим сущностям](luis-quickstart-intent-and-hier-entity.md), [импортируйте](luis-how-to-start-new-app.md#import-new-app) файл JSON в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website). Приложение, которое следует импортировать, находится в репозитории Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json).

Чтобы сохранить исходное приложение по управлению персоналом, клонируйте версию приложения на странице [Параметры](luis-how-to-manage-versions.md#clone-a-version) и назовите ее `composite`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию.  

## <a name="composite-entity-is-a-logical-grouping"></a>Составная сущность представляет собой логическую группировку 
Целью сущности является группировка связанных сущностей в сущность родительской категории. До создания составной сущности информация существует в виде отдельных сущностей. Она похожа на иерархическую сущность, но может содержать больше типов сущностей. 

 Составную сущность следует создавать, когда возможна логическая группировка отдельных сущностей. Эта логическая группировка полезна в клиентском приложении. 

В этом приложении имя сотрудника определяется в сущности списка **Employee** и включает синонимы имени, адрес электронной почты, добавочный номер телефона компании, номер мобильного телефона и Федеральный налоговый номер США. 

В намерении **MoveEmployee** есть примеры высказываний для запроса перемещения сотрудника из одного здания или офиса в другой. Именами зданий являются буквы: "A", "B" и т. д., именами офисов — числа: "1234", "13245". 

Примеры высказываний в намерении **MoveEmployee**.

|Примеры высказываний|
|--|
|Переместить Джона В. Смита в a-2345|
|сдвинуть x12345 к h-1234 завтра|
 
Запрос на перемещение должен включать, по крайней мере, сотрудника (с использованием любого синонима) и конечное расположение здания или офиса. Запрос также может включать расположение исходного офиса и дату перемещения. 

Извлеченные из конечной точки данные должны содержать эту информацию и возвращать ее в составную сущность `RequestEmployeeMove`. 

## <a name="create-composite-entity"></a>Создание составной сущности
1. Убедитесь, что приложение Human Resources находится в разделе **Build** (Создание) на веб-сайте LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

2. На странице **Намерения** выберите намерение **MoveEmployee**. 

3. Выберите значок лупы на панели инструментов, чтобы отфильтровать список высказываний. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Снимок экрана LUIS для намерения MoveEmployee с выделенной кнопкой лупы")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Введите `tomorrow` в текстовое поле фильтра, чтобы найти высказывание `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Снимок экрана LUIS для намерения MoveEmployee с выделенным фильтром \"завтра\"")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    Другим методом является фильтрация сущности по datetimeV2. Для этого щелкните **Entity filters** (Фильтры сущности), затем в выпадающем списке выберите **datetimeV2**. 

5. Выберите первую сущность `Employee`, а затем в списке всплывающего меню выберите **Wrap in composite entity** (Заключить в составную сущность). 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Снимок экрана LUIS для намерения MoveEmployee, где изображен выделенный выбор первой сущности в составной")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Затем немедленно выберите последнюю сущность `datetimeV2` в высказывании. Зеленая линия под выбранными словами указывает на составную сущность. Во всплывающем меню введите составное имя `RequestEmployeeMove`, затем во втором всплывающем меню выберите **Create new composite** (Создать составную сущность). 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Снимок экрана LUIS для намерения MoveEmployee, где изображен выделенный выбор последней предварительно созданной сущности и создание новой")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. В диалоговом окне**What type of entity do you want to create?** (Какой тип сущности необходимо создать?) в списке содержатся почти все необходимые поля. Отсутствует только исходное расположение. В списке существующих сущностей выберите **Add a child entity** (Добавьте дочернюю сущность), затем — **Locations::Origin** и нажмите кнопку **Готово**. 

    ![Снимок экрана LUIS для намерения MoveEmployee, где изображено добавление другой сущности во всплывающем окне](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. На панели инструментов выберите лупу, чтобы удалить фильтр. 

## <a name="label-example-utterances-with-composite-entity"></a>Помеченный пример высказываний с составной сущностью
1. В каждом примере высказывания выберите самую левую сущность, которая должна быть в составной сущности. Затем выберите **Wrap in composite entity** (Заключить в составную сущность).

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Снимок экрана LUIS для намерения MoveEmployee, где изображен выделенный выбор первой сущности в составной")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Выберите последнее слово в составной сущности, а затем во всплывающем меню выберите **RequestEmployeeMove**. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Снимок экрана LUIS для намерения MoveEmployee, где изображен выделенный выбор последний сущности в составной")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Убедитесь, что все высказывания в намерении помечены составной сущностью. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Снимок экрана LUIS для намерения MoveEmployee с меткой всех высказываний")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
Приложение LUIS не знает о новой составной сущности, пока приложение не будет обучено. 

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>Запрос конечной точки 

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. Последний параметр строки запроса — `q`. Это запрос фразы. 

    Поскольку этот тест был создан для проверки правильности извлечения составной сущности, то он может включать либо существующие образцы высказываний, либо новое высказывание. Хорошей проверкой является включение всех дочерних сущностей в составную сущность.

    ```JSON
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  Это высказывание возвращает массив составных сущностей. Каждой сущности присваивается тип и значение. Чтобы достичь большей точности для каждой дочерней сущности, используйте комбинацию типа и значения из элемента составного массива, это позволит найти соответствующий элемент в массиве сущностей.  

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение идентифицировало намерение естественного языка запросов и вернуло извлеченные данные как именованную группу. 

Чат-бот теперь имеет достаточно информации для определения основного действия и связанных деталей в высказывании. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат намерения с наивысшим показателем и данные из сущности, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> [Руководство: 7. Добавление простой сущности и списка фраз](luis-quickstart-primary-and-secondary-data.md)  