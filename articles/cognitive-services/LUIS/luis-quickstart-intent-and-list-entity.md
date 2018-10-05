---
title: Руководство 4. Точное текстовое совпадение. Список сущностей LUIS
titleSuffix: Azure Cognitive Services
description: Получите данные, соответствующие определенному списку элементов. У каждого элемента в списке могут быть синонимы, для которых существует точное совпадение.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b4fdf094653a4b16dead6397fe8e1a9f1a0258b9
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162089"
---
# <a name="tutorial-4-extract-exact-text-matches"></a>Руководство 4. Извлечение точных текстовых совпадений
В этом руководстве вы узнаете, как получить данные, соответствующие предварительно определенному списку элементов. Каждый элемент в списке может содержать список синонимов. Для приложения для управления персоналом можно выбрать сотрудника с помощью нескольких ключевых факторов (имени, адреса электронной почты, номера телефона и федерального ИНН США). 

Приложение "Управление персоналом" необходимо для того, чтобы определить, какой сотрудник перемещается из одного здания в другое. Для высказывания о перемещении сотрудника LUIS определяет намерение и извлекает сущность сотрудника, чтобы стандартный заказ на его перемещение мог быть создан клиентским приложением.

Это приложение использует сущность списка для извлечения сущности сотрудника. Сотрудник может быть указан с использованием имени, добавочного номера компании, номера телефона, электронной почты или федерального номера социального страхования (SSN) США. 

Сущность списка оптимальна для данных такого типа в таких случаях:

* Значения данных являются известным набором.
* Набор не превышает максимальные [ограничения](luis-boundaries.md) LUIS для этого типа сущности.
* Текст в высказывании в точности совпадает с синонимом или каноническим именем. 

**Из этого руководства вы узнаете, как выполнять следующие задачи:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Использовать существующее приложение из руководства
> * Добавление намерения MoveEmployee
> * Добавление сущности списка 
> * Train 
> * Опубликовать
> * Получать намерения и сущности из конечной точки.

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Использование существующего приложения
Продолжите работу с приложением (**HumanResources**), созданным в соответствии с инструкциями из предыдущего руководства. 

Если у вас нет приложения HumanResources из предыдущего руководства, сделайте следующее:

1.  Загрузите и сохраните [JSON-файл приложения](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-regex-HumanResources.json).

2. Импортируйте JSON-файл в новое приложение.

3. Из раздела **Управление** на вкладке **Версии** скопируйте версию и назовите ее `list`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. Так как имя версии используется в составе URL-адреса, оно не должно содержать символы, недопустимые для URL-адресов. 


## <a name="moveemployee-intent"></a>Намерение MoveEmployee

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Выберите **Create new intent**. (Создать намерение). 

3. Введите `MoveEmployee` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово). 

    ![Снимок экрана диалогового окна создания намерения с](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |переместить John W. Smith из B-1234 в H-4452|
    |переместить john.w.smith@mycompany.com из офиса b-1234 в офис h-4452|
    |сдвинуть x12345 к h-1234 завтра|
    |поместить 425-555-1212 в HH-2345|
    |переместить 123-45-6789 из A-4321 в J-23456|
    |переместить Jill Jones из D-2345 в J-23456|
    |сдвинуть jill-jones@mycompany.com в M-12345|
    |x23456 в M-12345|
    |425-555-0000 в h-4452|
    |234-56-7891 в hh-2345|

    [ ![Снимок экрана страницы намерения с выделенными фразами](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png) ](./media/luis-quickstart-intent-and-list-entity/hr-enter-utterances.png#lightbox)

    Помните, что номер и datetimeV2 были добавлены в предыдущем руководстве и будут назначаться автоматически при их обнаружении в любом примере высказывания.

    [!include[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="employee-list-entity"></a>Сущность списка сотрудников
Теперь, когда намерение **MoveEmployee** содержит высказывания, интеллектуальная служба распознавания речи должна распознавать сотрудников. 

Первичное _каноническое_ имя для каждого элемента является номером сотрудника. Далее приведены примеры синонимов для этой тематики. 

|Цель синонима|Значение синонима|
|--|--|
|Действие|John W. Smith|
|Адрес электронной почты|john.w.smith@mycompany.com|
|Добавочный номер|x12345|
|Персональный номер мобильного телефона|425-555-1212|
|Номер социального страхования в США|123-45-6789|


1. В области слева выберите **Entities** (Сущности).

2. Нажмите кнопку **Создать сущность**.

3. Во всплывающем диалоговом окне сущности введите имя сущности `Employee` и **Список** для типа сущности. Нажмите кнопку **Готово**.  

    [![](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png "Снимок экрана всплывающего диалогового окна создания сущности")](media/luis-quickstart-intent-and-list-entity/hr-list-entity-ddl.png#lightbox)

4. На странице сущности Employee введите `Employee-24612` в качестве нового значения.

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png "Снимок экрана ввода нового значения")](media/luis-quickstart-intent-and-list-entity/hr-emp1-value.png#lightbox)

5. В синонимы добавьте следующие значения:

    |Цель синонима|Значение синонима|
    |--|--|
    |Действие|John W. Smith|
    |Адрес электронной почты|john.w.smith@mycompany.com|
    |Добавочный номер|x12345|
    |Персональный номер мобильного телефона|425-555-1212|
    |Номер социального страхования в США|123-45-6789|

    [![](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png "Снимок экрана ввода синонимов")](media/luis-quickstart-intent-and-list-entity/hr-emp1-synonyms.png#lightbox)

6. Введите `Employee-45612` в качестве нового значения.

7. В синонимы добавьте следующие значения:

    |Цель синонима|Значение синонима|
    |--|--|
    |ИМЯ|Jill Jones|
    |Адрес электронной почты|jill-jones@mycompany.com|
    |Добавочный номер|x23456|
    |Персональный номер мобильного телефона|425-555-0000|
    |Номер социального страхования в США|234-56-7891|

## <a name="train"></a>Train

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Опубликовать

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Получение намерения и сущностей из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Перейдите в конец URL-адреса и введите `shift 123-45-6789 from Z-1242 to T-54672`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом и должна возвращать намерение `MoveEmployee` с извлеченным `Employee`.

  ```JSON
  {
    "query": "shift 123-45-6789 from Z-1242 to T-54672",
    "topScoringIntent": {
      "intent": "MoveEmployee",
      "score": 0.9882801
    },
    "intents": [
      {
        "intent": "MoveEmployee",
        "score": 0.9882801
      },
      {
        "intent": "FindForm",
        "score": 0.016044287
      },
      {
        "intent": "GetJobInformation",
        "score": 0.007611245
      },
      {
        "intent": "ApplyForJob",
        "score": 0.007063288
      },
      {
        "intent": "Utilities.StartOver",
        "score": 0.00684710965
      },
      {
        "intent": "None",
        "score": 0.00304174074
      },
      {
        "intent": "Utilities.Help",
        "score": 0.002981
      },
      {
        "intent": "Utilities.Confirm",
        "score": 0.00212222221
      },
      {
        "intent": "Utilities.Cancel",
        "score": 0.00191026414
      },
      {
        "intent": "Utilities.Stop",
        "score": 0.0007461446
      }
    ],
    "entities": [
      {
        "entity": "123 - 45 - 6789",
        "type": "Employee",
        "startIndex": 6,
        "endIndex": 16,
        "resolution": {
          "values": [
            "Employee-24612"
          ]
        }
      },
      {
        "entity": "123",
        "type": "builtin.number",
        "startIndex": 6,
        "endIndex": 8,
        "resolution": {
          "value": "123"
        }
      },
      {
        "entity": "45",
        "type": "builtin.number",
        "startIndex": 10,
        "endIndex": 11,
        "resolution": {
          "value": "45"
        }
      },
      {
        "entity": "6789",
        "type": "builtin.number",
        "startIndex": 13,
        "endIndex": 16,
        "resolution": {
          "value": "6789"
        }
      },
      {
        "entity": "-1242",
        "type": "builtin.number",
        "startIndex": 24,
        "endIndex": 28,
        "resolution": {
          "value": "-1242"
        }
      },
      {
        "entity": "-54672",
        "type": "builtin.number",
        "startIndex": 34,
        "endIndex": 39,
        "resolution": {
          "value": "-54672"
        }
      }
    ]
  }
  ```

  Сотрудник найден и возвращен как тип `Employee` со значением разрешения `Employee-24612`.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация
Из этого руководства вы узнали, как создать новое намерение, добавить примеры высказываний, а затем создать сущность списка для извлечения точного текстового совпадения из высказывания. После обучения и публикации приложения запрос к конечной точке идентифицировал намерение и вернул извлеченные данные.

> [!div class="nextstepaction"]
> [Добавление иерархической сущности в приложение](luis-quickstart-intent-and-hier-entity.md)

