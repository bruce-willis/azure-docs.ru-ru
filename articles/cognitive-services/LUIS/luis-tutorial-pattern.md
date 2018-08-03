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
ms.date: 07/20/2018
ms.author: diberry
ms.openlocfilehash: 9ad1d9e1543c3d9a74025fb23bd1767478b53b4b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238460"
---
# <a name="tutorial-improve-app-with-patterns"></a>Руководство по улучшению приложения с помощью шаблонов

В этом руководстве используются шаблоны для улучшения прогнозирования намерений и сущностей.  

> [!div class="checklist"]
* Определение необходимости в использовании шаблона для вашего приложения
* Создание шаблона 
* Проверка улучшения прогнозирования для шаблона

Для работы с этой статьей требуется бесплатная учетная запись [LUIS](luis-reference-regions.md), в которой вы создадите приложение LUIS.

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения по управлению персоналом из руководства по [пакетному тестированию](luis-tutorial-batch-testing.md), [импортируйте](luis-how-to-start-new-app.md#import-new-app) файл JSON в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website). Приложение, которое следует импортировать, находится в репозитории GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-batchtest-HumanResources.json).

Чтобы сохранить исходное приложение по управлению персоналом, клонируйте версию приложения на странице [Параметры](luis-how-to-manage-versions.md#clone-a-version) и назовите ее `patterns`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Обучение LUIS общим высказываниям с меньшим числом примеров благодаря использованию шаблонов
Из-за особенностей предметной области управления персоналом существует несколько общих способов для отправки запросов о связях сотрудников в организациях. Например: 

```
Who does Jill Jones report to?
Who reports to Jill Jones? 
```

Эти высказывания слишком близки для определения контекстной уникальности каждого из них без использования большого числа примеров высказываний. Благодаря добавлению шаблона для намерения LUIS изучает шаблоны общих высказываний для намерения без использования слишком большого числа примеров высказываний. 

К примерам шаблонов высказываний для этого намерения относятся:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Шаблон предоставляется в виде примера высказывания шаблона, который включает синтаксис для идентификации сущностей и игнорируемый текст. Шаблон представляет собой сочетание сопоставления регулярных выражений и машинного обучения.  Пример высказывания шаблона вместе с высказываниями намерений дает LUIS лучшее представление о том, какие высказывания соответствуют намерениям.

Чтобы шаблон соответствовал высказываниям, сущности в высказываниях должны сначала совпадать с сущностями высказывания шаблона. Тем не менее шаблон не помогает прогнозировать сущности, а только намерения. 

**Хотя шаблоны и помогают указать меньше примеров фраз, но если сущности не обнаруживаются, шаблоны не совпадают.**

Помните, что сотрудники были созданы в [Руководстве: 4. Добавление сущности списка](luis-quickstart-intent-and-list-entity.md).

## <a name="create-new-intents-and-their-utterances"></a>Создание новых намерений и их высказываний
Добавьте два новых намерения: `OrgChart-Manager` и `OrgChart-Reports`. Когда LUIS возвращает прогнозирование для клиентского приложения, имя намерения может использоваться как имя функции в клиентском приложении и сущность Employee может использоваться как параметр для этой функции.

```
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

    [![](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Снимок экрана LUIS \"Добавление новых высказываний к намерениям\"")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

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
Новые намерения и высказывания требуют обучения. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Изображение кнопки обучения](./media/luis-tutorial-pattern/hr-train-button.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Изображение панели уведомления, свидетельствующей об успешном обучении](./media/luis-tutorial-pattern/hr-trained-inline.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки
Чтобы получить прогноз LUIS в чат-боте или другом приложении, необходимо опубликовать приложение. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Publish** (Опубликовать). 

2. Выберите слот Production (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

    [ ![Снимок экрана страницы публикации с выделенной кнопкой "Publish" (Опубликовать) и выбранным слотом "Production" (Рабочий)](./media/luis-tutorial-pattern/hr-publish-to-production.png)](./media/luis-tutorial-pattern/hr-publish-to-production.png#lightbox)

3. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой
1. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

    [ ![Снимок экрана страницы публикации с выделенным URL-адресом конечной точки](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png)](./media/luis-tutorial-pattern/hr-publish-select-endpoint.png#lightbox)


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
    
    Сущности с ролями используют синтаксис, который включает в себя имя роли, и рассматриваются в разделе [Tutorial: Improve app with pattern roles](luis-tutorial-pattern-roles.md) (Руководство. Улучшение приложения с помощью ролей шаблонов). 

    Необязательный синтаксис `[]` отмечает слова или знаки препинания, которые являются необязательными. LUIS сопоставляет высказывание, игнорируя необязательный текст внутри скобок.

    При вводе высказывания шаблона LUIS помогает заполнить сущность после того, как будет введена левая фигурная скобка `{`.

    [ ![Снимок экрана с вводом высказываний шаблона для намерения](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)



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

2. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

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

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Чтобы это сделать, щелкните многоточие (***...***) справа от имени приложения в списке и выберите **Удалить**. Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Tutorial: Improve app with pattern roles](luis-tutorial-pattern-roles.md) (Руководство. Улучшение приложения с помощью ролей шаблонов)