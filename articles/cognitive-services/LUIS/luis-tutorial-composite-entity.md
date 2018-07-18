---
title: Создание составной сущности для извлечения сложных данных в Azure | Документы Майкрософт
description: Узнайте, как создать составную сущность в приложении LUIS для извлечения различных типов данных сущности.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 03/28/2018
ms.author: v-geberr
ms.openlocfilehash: cb581ee60dea2b0810332933455a03a8b68e16ea
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264391"
---
# <a name="use-composite-entity-to-extract-complex-data"></a>Использование составной сущности для извлечения сложных данных
В этом простом приложении имеется два [намерения](luis-concept-intent.md) и несколько сущностей. Оно предназначено для бронирования авиабилетов, например "1 ticket from Seattle to Cairo on Friday" (1 билет из Сиэтла в Каир в пятницу), и возвращения всех особенностей резервирования в виде одного набора данных. 

Из этого руководства вы узнаете, как выполнять такие задачи:

> [!div class="checklist"]
* Добавление предварительно созданных сущностей datetimeV2 и number
* Создание составной сущности
* Выполнение запроса к LUIS и получение данных составной сущности

## <a name="before-you-begin"></a>Перед началом работы
* Приложение LUIS из **[краткого руководства](luis-tutorial-composite-entity.md)**. 

> [!Tip]
> Если у вас еще нет подписки, вы можете зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/).

## <a name="composite-entity-is-a-logical-grouping"></a>Составная сущность представляет собой логическую группировку 
Целью сущности является поиск и категоризация частей текста во фразе. [Составная](luis-concept-entity-types.md) сущность образована из других типов сущностей, обученных в контексте. Для этого приложения для путешествий с функциями бронирования авиабилетов существует несколько фрагментов информации, такие как даты, расположения и количество мест. 

До создания составной сущности информация существует в виде отдельных сущностей. Составную сущность следует создавать при наличии возможности логической группировки отдельных сущностей. Эта логическая группировка будет полезна для чат-бота или другого приложения, использующего LUIS. 

Ниже приведены примеры простых фраз пользователей.

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Reserve a seat from New York to Paris on the first of April
```
 
Составная сущность соответствует количеству мест, месту отправления, месту назначения и дате. 

## <a name="what-luis-does"></a>Действия приложения LUIS
Когда намерение и сущности выражения будут определены, [извлечены](luis-concept-data-extraction.md#list-entity-data) и возвращены в формате JSON из [конечной точки](https://aka.ms/luis-endpoint-apis), работа LUIS будет завершена. Вызывающее приложение или чат-бот принимает этот ответ JSON и выполняет запрос любым настроенным в нем способом. 

## <a name="add-prebuilt-entities-number-and-datetimev2"></a>Добавление предварительно созданных сущностей datetimeV2 и number
1. Выберите приложение `MyTravelApp` в списке приложений на веб-сайте [LUIS][LUIS].

2. В открытом приложении щелкните ссылку **Entities** (Сущности) в левой области навигации.

    ![Нажатие кнопки сущностей](./media/luis-tutorial-composite-entity/intents-page-select-entities.png)    

3. Выберите **Manage prebuilt entities** (Управление предварительно созданными сущностями).

    ![Нажатие кнопки сущностей](./media/luis-tutorial-composite-entity/manage-prebuilt-entities-button.png)

4. Во всплывающем окне выберите **number** и **datetimeV2**.

    ![Нажатие кнопки сущностей](./media/luis-tutorial-composite-entity/prebuilt-entity-ddl.png)

5. Для извлечения новых сущностей нажмите кнопку **Train** (Обучить) в верхней строке навигации.

    ![Выбор кнопки обучения](./media/luis-tutorial-composite-entity/train.png)

## <a name="use-existing-intent-to-create-composite-entity"></a>Использование существующего намерения для создания составной сущности
1. В левой области навигации выберите **Intents** (Намерения). 

    ![Выбор ссылки "Intents" (Намерения)](./media/luis-tutorial-composite-entity/intents-from-entities-page.png)

2. Выберите `BookFlight` в списке **Intents** (Намерения).  

    ![Выбор намерения Select BookFlight в списке](./media/luis-tutorial-composite-entity/intent-page-with-prebuilt-entities-labeled.png)

    Предварительно созданные сущности number и datetimeV2 помечены в выражениях.

3. Для выражения `book 2 flights from seattle to cairo next monday` выберите сущность `number` синего цвета, затем в списке выберите **Wrap in composite entity** (Заключить в составную сущность). Зеленая линия под словами следует за курсором при его перемещении вправо и указывает на составную сущность. Затем перейдите вправо, чтобы выбрать последнюю предварительно созданную сущность `datetimeV2`, введите `FlightReservation` в текстовом поле выплывающего окна, после этого выберите **Create new composite** (Создать составную сущность). 

    ![Создание составной сущности на странице намерений](./media/luis-tutorial-composite-entity/create-new-composite.png)

4. Появится всплывающее диалоговое окно для проверки дочерних элементов составной сущности. Нажмите кнопку **Готово**.

    ![Создание составной сущности на странице намерений](./media/luis-tutorial-composite-entity/validate-composite-entity.png)

## <a name="wrap-the-entities-in-the-composite-entity"></a>Заключение сущностей в составную сущность
После создания составной сущности пометьте остальные фразы в составной сущности. Чтобы упаковать фразу как составную сущность, выберите самое левое слово, затем в появившемся списке выберите **Wrap in composite entity** (Заключить в составную сущность), выберите самое правое слово, а затем именованную составную сущность `FlightReservation`. Это быстрое и состоящее из операций выбора действие разбивается следующие шаги.

1. Во фразе `schedule 4 seats from paris to london for april 1` выберите 4 в качестве предварительно созданной сущности number.

    ![Выбор самого левого слова](./media/luis-tutorial-composite-entity/wrap-composite-step-1.png)

2. В появившемся списке выберите **Wrap in composite entity** (Заключить в составную сущность).

    ![Выбор параметра заключения](./media/luis-tutorial-composite-entity/wrap-composite-step-2.png)

3. Выберите самое правое слово. Под фразой появится зеленая линия, означающая составную сущность.

    ![Выбор самого правого слова](./media/luis-tutorial-composite-entity/wrap-composite-step-3.png)

4. В появившемся списке выберите составную сущность с именем `FlightReservation`.

    ![Выбор именованной составной сущности](./media/luis-tutorial-composite-entity/wrap-composite-step-4.png)

    Для последней фразы воспользуйтесь теми же инструкциями и заключите `London` и `tomorrow` в составную сущность. 

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
Приложение LUIS не знает об изменениях намерений и сущностей (модели), пока не будет обучено. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Обучение приложения](./media/luis-tutorial-composite-entity/train-button.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Обучение успешно выполнено](./media/luis-tutorial-composite-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки
Чтобы получить прогноз LUIS в чат-боте или другом приложении, необходимо опубликовать приложение. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Publish** (Опубликовать). 

2. Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

    ![публикация приложения](./media/luis-tutorial-composite-entity/publish-to-production.png)

3. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой
1. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

    ![Выбор URL-адреса конечной точки](./media/luis-tutorial-composite-entity/publish-select-endpoint.png)

2. Перейдите в конец URL-адреса и введите `reserve 3 seats from London to Cairo on Sunday`. Последний параметр строки запроса — `q`. Это запрос фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `BookFlight` с извлечением иерархической сущности.

```
{
  "query": "reserve 3 seats from London to Cairo on Sunday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.999999046
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.999999046
    },
    {
      "intent": "None",
      "score": 0.227036044
    }
  ],
  "entities": [
    {
      "entity": "sunday",
      "type": "builtin.datetimeV2.date",
      "startIndex": 40,
      "endIndex": 45,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-03-25"
          },
          {
            "timex": "XXXX-WXX-7",
            "type": "date",
            "value": "2018-04-01"
          }
        ]
      }
    },
    {
      "entity": "3 seats from london to cairo on sunday",
      "type": "flightreservation",
      "startIndex": 8,
      "endIndex": 45,
      "score": 0.6892485
    },
    {
      "entity": "cairo",
      "type": "Location::Destination",
      "startIndex": 31,
      "endIndex": 35,
      "score": 0.557570755
    },
    {
      "entity": "london",
      "type": "Location::Origin",
      "startIndex": 21,
      "endIndex": 26,
      "score": 0.8933808
    },
    {
      "entity": "3",
      "type": "builtin.number",
      "startIndex": 8,
      "endIndex": 8,
      "resolution": {
        "value": "3"
      }
    }
  ],
  "compositeEntities": [
    {
      "parentType": "flightreservation",
      "value": "3 seats from london to cairo on sunday",
      "children": [
        {
          "type": "builtin.datetimeV2.date",
          "value": "sunday"
        },
        {
          "type": "Location::Destination",
          "value": "cairo"
        },
        {
          "type": "builtin.number",
          "value": "3"
        },
        {
          "type": "Location::Origin",
          "value": "london"
        }
      ]
    }
  ]
}
```

Эта фраза возвращает массив составных сущностей, включая объект **flightreservation** с извлеченными данными.  

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение, состоящее всего из двух намерений и составной сущности, идентифицировало намерение запроса на естественном языке и вернуло извлеченные данные. 

Ваш чат-бот теперь имеет достаточно информации для определения основного действия, `BookFlight`, а информацию о бронировании можно найти во фразе. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат намерения с наивысшим показателем и данные из сущности, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="next-steps"></a>Дополнительная информация

[Узнайте больше о сущностях](luis-concept-entity-types.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
