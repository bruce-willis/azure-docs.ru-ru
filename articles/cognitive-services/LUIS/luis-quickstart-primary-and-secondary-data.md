---
title: 'Руководство по созданию приложения LUIS для извлечения данных: Azure | Документация Майкрософт'
description: В этом руководстве вы узнаете, как создать простое приложение LUIS, использующее намерения и простую сущность для извлечения данных машинного обучения.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: a69ea8ea45a02399b7c6ad22f0dc514ad8537e06
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159662"
---
# <a name="tutorial-7-add-simple-entity-and-phrase-list"></a>Руководство: 7. Добавление простой сущности и списка фраз
В этом руководстве описано создание приложения, которое показывает, как извлечь данные машинного обучения из фразы с помощью **простой** сущности.

<!-- green checkmark -->
> [!div class="checklist"]
> * Общие сведения о простых сущностях. 
> * Создание приложения LUIS, предназначенного для работы с доменом отдела кадров 
> * Добавление простой сущности для извлечения заданий из приложения
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS.
> * Добавление списка фраз для усиления сигналов по словам, связанным с работой
> * Обучение, публикация приложения и повторный запрос конечной точки

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения для управления персоналом, созданного с помощью руководства по [составным сущностям](luis-tutorial-composite-entity.md), [импортируйте](luis-how-to-start-new-app.md#import-new-app) файл JSON в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website). Приложение, которое следует импортировать, находится в репозитории Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-composite-HumanResources.json).

Чтобы сохранить исходное приложение по управлению персоналом, клонируйте версию приложения на странице [Параметры](luis-how-to-manage-versions.md#clone-a-version) и назовите ее `simple`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию.  

## <a name="purpose-of-the-app"></a>Назначение приложения
Это приложение демонстрирует способы извлечения данных из фразы. Рассмотрите следующие фразы чат-бота:

|Фраза|Извлекаемое имя должности|
|:--|:--|
|Я хочу подать заявку на новую задачу учета.|учет|
|Отправьте резюме на должность инженера.|инженеры|
|Заполните заявку для задания 123456|123456|

Это руководство добавляет новую сущность для извлечения названия должности. 

## <a name="purpose-of-the-simple-entity"></a>Назначение простой сущности
Назначение простой сущности в этом приложении LUIS — научить LUIS тому, что такое имя должности и где его можно найти во фразе. Часть фразы, которая является должностью, может изменяться в разных фразах в зависимости от подобранных слов и длины фразы. LUIS требуются примеры должности в любой фразе по всем намерениям.  

Название должности сложно определить, так как оно может быть существительным, глаголом или фразой из нескольких слов. Например: 

|Задания|
|--|
|инженер|
|разработчик программного обеспечения|
|старший разработчик программного обеспечения|
|руководитель команды разработки |
|диспетчер службы движения|
|водитель автомобиля|
|водитель санитарного автомобиля|
|сиделка|
|мастер по работе с пресс-экструдером|
|монтажник|

У этого приложения LUIS есть названия должностей в нескольких намерениях. Пометив эти слова во всех фразах с намерениями, LUIS узнает больше о том, что такое должность и где она находится во фразах.

## <a name="create-job-simple-entity"></a>Создание простой сущности должности

1. Убедитесь, что приложение Human Resources находится в разделе **Build** (Создание) на веб-сайте LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

2. На странице **намерений** выберите намерение **ApplyForJob**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "Снимок экрана LUIS с выделенным намерением ApplyForJob")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. В выражении `I want to apply for the new accounting job` выберите `accounting`, введите `Job` в поле сверху всплывающего меню, а затем во всплывающем меню выберите **Create new entity** (Создать сущность). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Снимок экрана LUIS с намерением ApplyForJob и выделенными шагами создания сущности")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Во всплывающем окне проверьте имя и тип сущности, а затем выберите **Готово**.

    ![Создание модального диалога простой сущности с названием должности и простым типом](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. Во фразе `Submit resume for engineering position` обозначьте слово `engineering` как сущность должности. Выберите слово `engineering`, а затем **Должность** во всплывающем меню. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Снимок экрана с выделенной сущностью задания маркировки LUIS")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Все фразы помечены, но пять фраз недостаточно, чтобы обучить LUIS связанным с должностями словам и фразам. Нам не нужны дополнительные примеры должностей, использующих значение номера, так как используется сущность регулярного выражения. Нужно по крайней мере 15 дополнительных примеров должностей, являющихся словами или фразами. 

6. Добавьте больше фраз и отметьте слова или фразы, связанные с должностями, в качестве сущности **Должность**. Типы должностей являются общими в службе по трудоустройству. Если нужны должности, связанные с определенной отраслью, слова должности должны отражать это. 

    |Фраза|Сущность должности|
    |:--|:--|
    |Я подаю заявку на получение должности руководителя программ в отделе исследовательской работы|руководитель программ|
    |Вот мое резюме на повара линии раздачи.|повар линии раздачи|
    |Мое резюме на воспитателя детского лагеря прилагается.|воспитатель детского лагеря|
    |Это моя краткая биография для получения должности помощника по административной работе.|помощник по административной работе|
    |Я хочу подать заявку на получение управленческой должности в сфере продаж.|управление, продажи|
    |Это резюме для новой должности в сфере учета.|учет|
    |Моя заявка для помощника бармена включена.|помощник бармена|
    |Я отправляю резюме на кровельщика и плотника.|кровельщик, плотник|
    |Мою краткую биографию для получения должности водителя автобуса можно найти здесь.|водитель автобуса|
    |Я дипломированная медсестра. Вот мое резюме.|дипломированная медсестра|
    |Я хотел бы представить документы на должность учителя из газеты.|учитель|
    |Это моя краткая биография на получение должности специалиста, обслуживающего полки с фруктами и овощами в магазине.|специалист, обслуживающий полки|
    |Резюме для укладчика плитки.|Плитка|
    |Вложенные резюме для ландшафтного архитектора.|ландшафтный архитектор|
    |Моя краткая биография для получения должности профессора биологии прилагается.|профессор биологии|
    |Я хотел бы подать заявку на должность фотографа.|фотограф|git 

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Метка сущности в примерах намерений для GetJobInformation
1. Выберите **намерения** в меню слева.

2. Выберите **GetJobInformation** из списка намерений. 

3. Обозначьте должности в примерах фраз:

    |Фраза|Сущность должности|
    |:--|:--|
    |Есть ли должность для работы с базами данных?|databases|
    |Ищу новую работу с обязанностями в бухгалтерском учете|учет|
    |Какие должности доступны для старших инженеров?|старшие инженеры|

    Существуют и другие примеры фраз, но в них нет слов, связанных с должностью.

## <a name="train-the-luis-app"></a>Обучение приложения LUIS

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Перейдите в конец URL-адреса и введите `Here is my c.v. for the programmer job`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должны быть возвращены фразы `ApplyForJob`.

```JSON
{
  "query": "Here is my c.v. for the programmer job",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.9826467
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.9826467
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0218927357
    },
    {
      "intent": "MoveEmployee",
      "score": 0.007849265
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00349470088
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.00348804821
    },
    {
      "intent": "None",
      "score": 0.00319909188
    },
    {
      "intent": "FindForm",
      "score": 0.00222647213
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00211193133
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00172086991
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00138010911
    }
  ],
  "entities": [
    {
      "entity": "programmer",
      "type": "Job",
      "startIndex": 24,
      "endIndex": 33,
      "score": 0.5230502
    }
  ]
}
```

## <a name="names-are-tricky"></a>Сложности с извлечением названия
Приложению LUIS удалось найти правильное намерение с высокой достоверностью и извлечь название должности, хоть это и непросто. Попробуйте выражение `This is the lead welder paperwork`.  

В следующем коде JSON приложение LUIS отвечает правильным намерением, `ApplyForJob`, но не извлекает название должности `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Так как имя может быть любым, LUIS спрогнозирует сущности точнее, если у него есть список фраз, усиливающих сигнал.

## <a name="to-boost-signal-add-jobs-phrase-list"></a>Добавление списка фраз, связанных с должностью, для усиления сигнала
Откройте файл [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) из репозитория Github примеров LUIS. Список содержит более тысячи слов и фраз, связанных с должностями. Ознакомьтесь со списком важных слов, связанных с должностями. Если слов или фраз нет в списке, добавьте собственные.

1. В разделе **Build** (Сборка) приложения LUIS и в меню **Improve app performance** (Повышение производительности приложения) выберите **Phrase lists** (Списки фраз).

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Снимок экрана списка фраз с выделенной левой кнопкой навигации")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Выберите **Create new phrase list** (Создать список фраз). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Снимок экрана с выделенной кнопкой создания списка фраз")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Присвойте списку фраз имя `Jobs` и скопируйте список из файла jobs-phrase-list.csv в текстовое поле **Значения**. Нажмите клавишу "ВВОД". 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Снимок экрана со всплывающим диалоговым окном создания списка фраз")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Если вы хотите добавить в список фраз другие слова, просмотрите **Связанные значения** и добавьте те, что вам необходимы. 

4. Выберите **Сохранить**, чтобы активировать список фраз.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Снимок экрана всплывающего диалогового окна создания списка фраз со словами в окне значений списка фраз")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Обучите](#train-the-luis-app) и [опубликуйте](#publish-the-app-to-get-the-endpoint-URL) приложение еще раз, чтобы использовать список фраз.

6. Сделайте повторный запрос к конечной точке с той же фразой: `This is the lead welder paperwork.`

    Ответ JSON содержит извлеченные сущности:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="phrase-lists"></a>Списки фраз
Добавление списка фраз усиливает сигнал слов в списке, но **не используется** в качестве точного соответствия. В списке фраз есть несколько должностей, первое слово которых — `lead`, а также должность `welder`, но нет должности `lead welder`. Этот список фраз, связанных с должностями, может быть неполным. Так как вы регулярно [проверяете фразы конечной точки](luis-how-to-review-endoint-utt.md) и находите другие слова, связанные с должностями, добавьте их в список фраз. Затем выполните повторное обучение и публикацию.

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение, содержащее простую сущность и список фраз, идентифицировало намерение запроса на естественном языке и вернуло данные о должности. 

Ваш чат-бот теперь содержит достаточно информации для определения основного действия для заявки на получение должности и параметра действия, на должность которого есть ссылка. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат с наивысшим показателем и данные из сущности, чтобы использовать сторонний API для отправки информации о задании представителю службы управления персоналом. Если существуют другие программные действия для бота или вызывающего приложения, LUIS не выполняет их. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление готовой сущности keyPhrase](luis-quickstart-intent-and-key-phrase.md)