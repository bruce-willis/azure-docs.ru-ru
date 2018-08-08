---
title: Руководство по улучшению прогнозирования в LUIS с помощью шаблонов — Azure | Документы Майкрософт
titleSuffix: Cognitive Services
description: В этом руководстве используются шаблоны для улучшения прогнозирования намерений и сущностей LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 07/30/2018
ms.author: diberry
ms.openlocfilehash: 355c1edd4fa7433e68a9c0e903f4f782203326fe
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365884"
---
# <a name="tutorial-improve-app-with-patterns"></a>Руководство по улучшению приложения с помощью шаблонов

В этом руководстве используются шаблоны для улучшения прогнозирования намерений и сущностей.  

> [!div class="checklist"]
* Определение необходимости в использовании шаблона для вашего приложения
* Создание шаблона
* Проверка улучшения прогнозирования для шаблона

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Перед началом работы

Если у вас нет приложения по управлению персоналом из руководства по [пакетному тестированию](luis-tutorial-batch-testing.md), [импортируйте](luis-how-to-start-new-app.md#import-new-app) файл JSON в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website). Приложение, которое следует импортировать, находится в репозитории GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json).

Чтобы сохранить исходное приложение по управлению персоналом, клонируйте версию приложения на странице [Параметры](luis-how-to-manage-versions.md#clone-a-version) и назовите ее `patterns`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Обучение LUIS общим высказываниям с меньшим числом примеров благодаря использованию шаблонов

Из-за особенностей предметной области управления персоналом существует несколько общих способов для отправки запросов о связях сотрудников в организациях. Например: 

|Высказывания|
|--|
|Who does Jill Jones report to?|
|Who reports to Jill Jones?|

Эти высказывания слишком близки для определения контекстной уникальности каждого из них без использования большого числа примеров высказываний. Благодаря добавлению шаблона для намерения LUIS изучает шаблоны общих высказываний для намерения без использования слишком большого числа примеров высказываний. 

К примерам шаблонов высказываний для этого намерения относятся:

|Примеры высказываний шаблона|
|--|
|Who does {Employee} report to?|
|Who reports to {Employee}?|

Шаблон предоставляется в виде примера высказывания шаблона, который включает синтаксис для идентификации сущностей и игнорируемый текст. Шаблон представляет собой сочетание сопоставления регулярных выражений и машинного обучения.  Пример высказывания шаблона вместе с высказываниями намерений дает LUIS лучшее представление о том, какие высказывания соответствуют намерениям.

Чтобы шаблон соответствовал высказываниям, сущности в высказываниях должны сначала совпадать с сущностями высказывания шаблона. Тем не менее шаблон не помогает прогнозировать сущности, а только намерения. 

**Хотя шаблоны и помогают указать меньше примеров фраз, но если сущности не обнаруживаются, шаблоны не совпадают.**

Помните, что сотрудники были созданы в [Руководстве: 4. Добавление сущности списка](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Создание новых намерений и их высказываний

Добавьте два новых намерения: `OrgChart-Manager` и `OrgChart-Reports`. Когда LUIS возвращает прогнозирование для клиентского приложения, имя намерения может использоваться как имя функции в клиентском приложении и сущность Employee может использоваться как параметр для этой функции.

```Javascript
OrgChart-Manager(employee){
    ///
}
```

1. Убедитесь, что приложение Human Resources находится в разделе **Build** (Создание) на веб-сайте LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

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

Количество примеров высказываний в этих намерениях недостаточно для того, чтобы правильно обучать LUIS. В реальном приложении каждое намерение должно содержать как минимум 15 высказываний с множеством вариантов слов и длиной высказывания. Эти несколько высказываний выбраны специально для выделения шаблонов. 

## <a name="train-the-luis-app"></a>Обучение приложения LUIS

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

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

## <a name="add-the-template-utterances"></a>Добавление высказываний шаблона

1. Выберите функцию **Сборка** в верхнем меню.

2. В левой области навигации в разделе **Повышение производительности приложения** выберите **Шаблоны** в панели навигации слева.

3. Выберите намерение **OrgChart-Manager**, затем укажите следующие высказывания шаблона по одному, нажимая клавишу ВВОД после указания каждого высказывания:

    |Высказывания шаблона|
    |:--|
    |Who is {Employee} the subordinate of[?]|
    |Who does {Employee} report to[?]|
    |Who is {Employee}['s] manager[?]|
    |Who does {Employee} directly report to[?]|
    |Who is {Employee}['s] supervisor[?]|
    |Who is the boss of {Employee}[?]|

    Синтаксис `{Employee}` отмечает расположение сущности в высказывании шаблона, а также указывает, какая это сущность. 

    Сущности с ролями используют синтаксис, который включает в себя имя роли, и рассматриваются в [руководстве по улучшению приложения с помощью ролей шаблона](luis-tutorial-pattern-roles.md). 

    Необязательный синтаксис `[]` отмечает слова или знаки препинания, которые являются необязательными. LUIS сопоставляет высказывание, игнорируя необязательный текст внутри скобок.

    При вводе высказывания шаблона LUIS помогает заполнить сущность после того, как будет введена левая фигурная скобка `{`.

    [![Снимок экрана: ввод высказываний шаблона для намерения](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Выберите намерение **OrgChart-Reports**, затем укажите следующие высказывания шаблона по одному, нажимая клавишу ВВОД после указания каждого высказывания:

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

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Tutorial: Improve app with pattern roles](luis-tutorial-pattern-roles.md) (Руководство. Улучшение приложения с помощью ролей шаблонов)