---
title: 'Руководство по созданию приложения LUIS для получения точных текстовых совпадений с данными в списке: Azure | Документация Майкрософт'
description: В рамках работы с этим кратким руководством вы узнаете, как создать простое приложение LUIS, использующее намерения и сущности списка для извлечения данных.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: afad3fe725fddd0748cc206517a7274815cf1653
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495270"
---
# <a name="tutorial-4-add-list-entity"></a>Руководство: 4. Добавление сущности списка
В этом руководстве вы создадите приложение, демонстрирующее, как получить данные, соответствующие предварительно определенному списку. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Общие сведения о сущностях списка 
> * Создание приложения LUIS для работы с доменом отдела кадров с намерением MoveEmployee
> * Добавление сущности списка для извлечения сущности сотрудника из фразы
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS.

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения управления персоналом из руководства по [сущностям регулярных выражений](luis-quickstart-intents-regex-entity.md), [импортируйте](luis-how-to-start-new-app.md#import-new-app) файл JSON в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website). Приложение, которое следует импортировать, находится в репозитории Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-regex-HumanResources.json).

Чтобы сохранить исходное приложение по управлению персоналом, клонируйте версию приложения на странице [Параметры](luis-how-to-manage-versions.md#clone-a-version) и назовите ее `list`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. 

## <a name="purpose-of-the-list-entity"></a>Предназначение сущности списка
Это приложение формулирует предположительную фразу о перемещении сотрудника из одного здания в другое. Это приложение использует сущность списка для извлечения сущности сотрудника. Сотрудник может быть указан с использованием имени, номера телефона, электронной почты или федерального номера социального страхования (SSN) США. 

Сущность списка может содержать множество элементов с синонимами для каждого элемента. Для компаний малого и среднего размера сущность списка используется для извлечения сведений о сотрудниках. 

Каноническое имя для каждого элемента является номером сотрудника. Далее приведены примеры синонимов для этого домена: 

|Цель синонима|Значение синонима|
|--|--|
|Действие|John W. Smith|
|Адрес электронной почты|john.w.smith@mycompany.com|
|Добавочный номер|x12345|
|Персональный номер мобильного телефона|425-555-1212|
|Номер социального страхования в США|123-45-6789|

Сущность списка оптимальна для данных такого типа в таких случаях:

* Значения данных являются известным набором.
* Набор не превышает максимальные [ограничения](luis-boundaries.md) LUIS для этого типа сущности.
* Текст во фразе в точности совпадает с синонимом. 

LUIS извлекает сведения о сотруднике таким образом, чтобы в клиентском приложении можно было создать стандартный заказ на перемещение сотрудника.
<!--
## Example utterances
Simple example utterances for a `MoveEmployee` inent:

```
move John W. Smith from B-1234 to H-4452
mv john.w.smith@mycompany from office b-1234 to office h-4452

```
-->

## <a name="add-moveemployee-intent"></a>Добавление намерения MoveEmployee

1. Убедитесь, что приложение Human Resources находится в разделе **Build** (Создание) на веб-сайте LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

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

## <a name="create-an-employee-list-entity"></a>Создание сущности списка сотрудников
Теперь, когда намерение **MoveEmployee** содержит фразы, приложение LUIS должно распознавать, что являют собой сотрудники. 

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

## <a name="train-the-luis-app"></a>Обучение приложения LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

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

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Где происходит обработка естественного языка в сущности списка? 
Так как сущность списка представляет собой точное текстовое совпадение, она не полагается на обработку естественного языка (или машинное обучение). Приложение LUIS использует обработку естественного языка (или машинное обучение), чтобы выбрать намерение с наивысшим показателем. Кроме того, фраза может представлять собой сочетание нескольких сущностей или даже нескольких типов сущностей. Каждая фраза обрабатывается для всех сущностей в приложении, включая сущности обработки естественного языка (или машинного обучения).

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение с сущностью списка извлекло правильного сотрудника. 

Теперь ваш чат-бот имеет достаточно информации для определения основного действия, `MoveEmployee`, и того, какого сотрудника перемещать. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат намерения с наивысшим показателем и данные из сущности, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление иерархической сущности в приложение](luis-quickstart-intent-and-hier-entity.md)

