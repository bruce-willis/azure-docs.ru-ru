---
title: Руководство 3. Шаблоны для улучшения прогнозирования LUIS
titleSuffix: Azure Cognitive Services
description: Используйте шаблоны для улучшения намерения и сущности прогнозирования, в то же время предоставляя меньше примеров высказываний. Шаблон предоставляется в виде примера высказывания шаблона, который включает синтаксис для идентификации сущностей и игнорируемый текст.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b09ebbb358b909c98df4eb05154c29b4b3cb7ee9
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888260"
---
# <a name="tutorial-3-add-common-utterance-formats"></a>Руководство 3. Добавление стандартных форматов высказываний

В этом руководстве используйте шаблоны для улучшения намерения и сущности прогнозирования, в то же время предоставляя меньше примеров высказываний. Шаблон предоставляется в виде примера высказывания шаблона, который включает синтаксис для идентификации сущностей и игнорируемый текст. Шаблон представляет собой сочетание сопоставления выражений и машинного обучения.  Пример высказывания шаблона вместе с высказываниями намерений дает LUIS лучшее представление о том, какие высказывания соответствуют намерениям. 

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Использовать существующее приложение из руководства 
> * Создавать намерение.
> * Train
> * Опубликовать
> * Получать намерения и сущности из конечной точки.
> * Создавать шаблон.
> * Проверять улучшения прогнозирования для шаблона.
> * Отмечать текст как тот, который нужно игнорировать и вкладывать в шаблон.
> * Использовать панель тестирования для проверки успешности шаблона.

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Использование существующего приложения

Продолжите работу с приложением **HumanResources**, созданным в рамках последнего руководства. 

Если у вас нет приложения HumanResources из предыдущего руководства, выполните приведенные ниже шаги.

1.  Загрузите и сохраните [JSON-файл приложения](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Импортируйте JSON-файл в новое приложение.

3. Из раздела **Управление** на вкладке **Версии** скопируйте версию и назовите ее `patterns`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. Так как имя версии используется в маршруте URL-адреса, оно не может содержать символы, которые недопустимы в URL-адресе.

## <a name="create-new-intents-and-their-utterances"></a>Создание новых намерений и их высказываний

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. На странице **намерений** выберите **Create new intent** (Создать намерение). 

3. Введите `OrgChart-Manager` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово).

    ![Создание нового сообщения всплывающего окна](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |Who is John W. Smith the subordinate of?|
    |Who does John W. Smith report to?|
    |Who is John W. Smith's manager?|
    |Who does Jill Jones directly report to?|
    |Who is Jill Jones supervisor?|

    [![Снимок экрана: добавление новых высказываний к намерениям в LUIS](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Снимок экрана: добавление новых высказываний к намерениям в LUIS")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Не беспокойтесь, если объект keyPhrase помечен в высказываниях намерения, а не сущности Employee. Оба они правильно прогнозированы в тестовой панели и в конечной точке. 

5. В левой области навигации выберите **Intents** (Намерения).

6. Выберите **Create new intent**. (Создать намерение). 

7. Введите `OrgChart-Reports` во всплывающем диалоговом окне и нажмите кнопку **Done** (Готово).

8. Добавьте примеры фраз в намерение.

    |Примеры высказываний|
    |--|
    |Who are John W. Smith's subordinates?|
    |Who reports to John W. Smith?|
    |Who does John W. Smith manage?|
    |Who are Jill Jones direct reports?|
    |Who does Jill Jones supervise?|

## <a name="caution-about-example-utterance-quantity"></a>Предупреждение о примере количества высказываний

[!include[Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Train

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Опубликовать

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Получение намерения и сущностей из конечной точки

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `Who is the boss of Jill Jones?`. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Был ли этот запрос успешным? В этом цикле обучения запрос выполнен успешно. Оценки двух главных намерений близки. Так как обучение LUIS не является каждый раз одинаковым, есть много вариантов, где эти две оценки могут инвертироваться на следующем тренировочном цикле. В результате может быть возвращено неправильное намерение. 

Используйте шаблоны, чтобы сделать правильную оценку намерения значительно выше в процентах и выше от следующей самой высокой оценки. 

Не закрывайте это второе окно браузера. Вы продолжите работу с ним далее в руководстве. 

## <a name="template-utterances"></a>Высказывания шаблона
Из-за особенностей предметной области управления персоналом существует несколько общих способов для отправки запросов о связях сотрудников в организациях. Например: 

|Высказывания|
|--|
|Who does Jill Jones report to?|
|Who reports to Jill Jones?|

Эти высказывания слишком близки для определения контекстной уникальности каждого из них без использования большого числа примеров высказываний. Благодаря добавлению шаблона для намерения LUIS изучает шаблоны общих высказываний для намерения без использования слишком большого числа примеров высказываний. 

К примерам шаблонов высказываний для этого намерения относятся:

|Примеры высказываний шаблона|Синтаксическое значение|
|--|--|
|Who does {Employee} report to[?]|взаимозаменяем {Employee}, игнорировать [?]}|
|Who reports to {Employee}[?]|взаимозаменяем {Employee}, игнорировать [?]}|

Синтаксис `{Employee}` отмечает расположение сущности в высказывании шаблона, а также указывает, какая это сущность. Необязательный синтаксис `[?]` отмечает слова или знаки препинания, которые являются необязательными. LUIS сопоставляет высказывание, игнорируя необязательный текст внутри скобок.

Хотя синтаксис выглядит как регулярные выражения, он им не является. Поддерживается синтаксис только фигурной (`{}`) и квадратной (`[]`) скобок. Их можно вложить до двух уровней.

Чтобы шаблон соответствовал высказываниям, сущности в высказываниях должны сначала совпадать с сущностями высказывания шаблона. Тем не менее шаблон не помогает прогнозировать сущности, а только намерения. 

**Хотя шаблоны и помогают указать меньше примеров фраз, но если сущности не обнаруживаются, шаблоны не совпадают.**

В этом руководстве добавьте два новых намерения: `OrgChart-Manager` и `OrgChart-Reports`. 

|Намерение|Фраза|
|--|--|
|OrgChart-Manager|Who does Jill Jones report to?|
|Отчеты OrgChart|Who reports to Jill Jones?|

Когда LUIS возвращает прогнозирование для клиентского приложения, имя намерения может использоваться как имя функции в клиентском приложении и сущность Employee может использоваться как параметр для этой функции.

```Javascript
OrgChartManager(employee){
    ///
}
```

Помните, что сотрудники были созданы в [Руководстве: 4. Добавление сущности списка](luis-quickstart-intent-and-list-entity.md).

1. Выберите функцию **Сборка** в верхнем меню.

2. В левой области навигации в разделе **Повышение производительности приложения** выберите **Шаблоны** в панели навигации слева.

3. Выберите намерение **OrgChart-Manager**, а затем введите следующие высказывания шаблона.

    |Высказывания шаблона|
    |:--|
    |Who is {Employee} the subordinate of[?]|
    |Who does {Employee} report to[?]|
    |Who is {Employee}['s] manager[?]|
    |Who does {Employee} directly report to[?]|
    |Who is {Employee}['s] supervisor[?]|
    |Who is the boss of {Employee}[?]|

    Сущности с ролями используют синтаксис, который включает в себя имя роли, и рассматриваются в [руководстве по улучшению приложения с помощью ролей шаблона](luis-tutorial-pattern-roles.md). 

    При вводе высказывания шаблона LUIS помогает заполнить сущность после того, как будет введена левая фигурная скобка `{`.

    [![Снимок экрана: ввод высказываний шаблона для намерения](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Выберите намерение **OrgChart-Reports**, а затем введите следующие высказывания шаблона.

    |Высказывания шаблона|
    |:--|
    |Who are {Employee}['s] subordinates[?]|
    |Who reports to {Employee}[?]|
    |Who does {Employee} manage[?]|
    |Who are {Employee} direct reports[?]|
    |Who does {Employee} supervise[?]|
    |Who does {Employee} boss[?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Запрос конечной точки при использовании шаблонов

1. Обучите и опубликуйте приложение еще раз.

2. Вернитесь на вкладку браузера с URL-адресом конечной точки.

3. Перейдите в конец URL-адреса и введите `Who is the boss of Jill Jones?` в качестве высказывания. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

    ```JSON
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Прогнозирование намерения теперь значительно выше.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Работа с предварительно созданными сущностями и необязательным текстом

В предыдущих высказываниях шаблона в этом руководстве содержалось несколько примеров с необязательным текстом, например использование буквы s в притяжательном падеже (`'s`) и вопросительного знака (`?`). Предположим, что в соответствии с высказываниями конечной точки менеджеры и представители отдела кадров ищут исторические данные, а также данные о плановом переходе сотрудников в другие отделы компании в будущем.

Примеры высказываний:

|Намерение|Примеры высказываний с предварительно созданными сущностями и необязательным текстом|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

В каждом из этих примеров используется глагол (`was`, `is`, `will be`), а также дата (`March 3`, `now` и `in a month`), которые служба LUIS должна правильно спрогнозировать. Обратите внимание, что в последних двух примерах используется практически тот же текст. Отличаются в нем только предлоги (`in` и `on`).

Примеры высказываний шаблона:
|Намерение|Примеры высказываний с предварительно созданными сущностями и необязательным текстом|
|:--|:--|
|OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

За счет использования синтаксиса с квадратными скобками (`[]`) этот необязательный текст легко добавить в высказывание шаблона и вложить вплоть до второго уровня (`[[]]`). Кроме того, вы можете добавить сущности или текст.

**Вопрос. Почему не удалось объединить два последних примера высказываний в отдельное высказывание шаблона?** Шаблон не поддерживает синтаксис OR. Чтобы захватить версию `in` и `on`, каждая из них должна быть отдельным высказыванием шаблона.

**Вопрос. Почему все первые буквы `w` каждого высказывания шаблона написаны в нижнем регистре? Разве регистр не следует выбирать случайным образом?** Регистр высказывания, отправленного клиентским приложением на конечную точку запроса, преобразовывается в нижний. Высказывание шаблона может иметь верхний или нижний регистр. То же самое относится к высказыванием конечной точки. Сравнение всегда выполняется после преобразования в нижний регистр.

**Вопрос. Почему предварительно созданная числовая часть высказывания шаблона March 3 не прогнозируется как число `3` и дата `March 3`?** В зависимости от контекста высказывание шаблона прогнозирует дату буквально (`March 3`) или абстрагировано (`in a month`). Дата может содержать число, но число не обязательно рассматривается как дата. Всегда используйте сущность, которая лучше всего представляет тип, который вы хотите вернуть в результатах прогнозирования JSON.  

**Вопрос. Как насчет плохо сформулированных высказываний, например `Who will {Employee}['s] manager be on March 3?`?** Грамматически разные временные глаголы, например глаголы с разделенными частями `will` и `be`, должны быть добавлены как новое высказывание шаблона. Имеющееся высказывание шаблона не сопоставит эти глаголы. Хотя намерение не изменилось, изменилась расстановка слов в высказывании. Это изменение влияет на прогноз в LUIS.

**Помните: сначала находятся сущности, а затем сравнивается шаблон.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Изменение имеющегося высказывания шаблона

1. На веб-сайте LUIS выберите **Build** (Создать) в верхнем меню, а затем щелкните **Patterns** (Шаблоны) в меню слева. 

2. Найдите имеющееся высказывание шаблона (`Who is {Employee}['s] manager[?]`) и щелкните многоточие (***...***) справа. 

3. В контекстном меню выберите **Edit** (Изменить). 

4. Измените высказывание шаблона следующим образом: `who is {Employee}['s] manager [[on]{datetimeV2}?]]`

## <a name="add-new-pattern-template-utterances"></a>Добавление новых высказываний шаблона

1. В разделе **Patterns** (Шаблоны) окна **Build** (Создание) добавьте несколько новых высказываний шаблона. В раскрывающемся меню намерений выберите **OrgChart-Manager** и введите каждое из этих высказываний шаблона:

    |Намерение|Примеры высказываний с предварительно созданными сущностями и необязательным текстом|
    |--|--|
    |OrgChart-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Обучите приложение.

3. Чтобы открыть панель тестирования, выберите **Test** (Тестировать) в верхней части панели. 

4. Введите несколько тестовых высказываний, чтобы проверить сопоставление шаблона и достаточно ли высока оценка намерения. 

    Когда введете первое высказывание, выберите **Inspect** (Проверить), чтобы просмотреть все результаты прогнозирования.

    |Фраза|
    |--|
    |Who will be Jill Jones manager|
    |who will be jill jones's manager|
    |Who will be Jill Jones's manager?|
    |who will be Jill jones manager on March 3|
    |Who will be Jill Jones manager next Month|
    |Who will be Jill Jones manager in a month?|

Для всех этих высказываний найдены сущности, поэтому они соответствуют тому же шаблону и имеют высокую оценку прогнозирования.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве добавляются два намерения для высказываний, которые трудно спрогнозировать с высокой точностью, не имея большого количества примеров высказываний. Добавление шаблонов позволило LUIS лучше прогнозировать намерение со значительно высшей оценкой достоверности. Маркировка сущностей и текст, который нужно игнорировать, позволяют LUIS применять шаблон к более широкому кругу высказываний.

> [!div class="nextstepaction"]
> [Tutorial: Improve app with pattern roles](luis-tutorial-pattern-roles.md) (Руководство. Улучшение приложения с помощью ролей шаблонов)