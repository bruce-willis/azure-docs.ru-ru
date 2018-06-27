---
title: 'Руководство по созданию приложения LUIS для получения точных текстовых совпадений с данными в списке: Azure | Документация Майкрософт'
description: В рамках работы с этим кратким руководством вы узнаете, как создать простое приложение LUIS, использующее намерения и сущности списка для извлечения данных.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 33394dff1091f27c79c74d8648a90724ba8d6698
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264833"
---
# <a name="tutorial-create-app-using-a-list-entity"></a>Руководство по созданию приложения, использующего сущность списка
В этом руководстве вы создадите приложение, демонстрирующее, как получить данные, соответствующие предварительно определенному списку. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Общие сведения о сущностях списка 
> * Создание приложения LUIS, посвященного напиткам, с намерением OrderDrinks.
> * Добавление намерения _None_ и примеров фраз.
> * Добавление сущности списка для извлечения элементов напитков из фразы.
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS.

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="purpose-of-the-list-entity"></a>Предназначение сущности списка
Это приложение принимает заказы на напитки, например `1 coke and 1 milk please`, и возвращает данные, такие как тип напитка. Сущность **списка** напитков ищет точные текстовые совпадения и возвращает их. 

Сущность списка представляет собой оптимальный выбор для данных такого типа, когда значения данных являются известным набором. Названия напитков могут варьироваться в зависимости от сленга и аббревиатур, но имена меняются нечасто. 

## <a name="app-intents"></a>Намерения приложения
Намерения — это категории того, что нужно пользователю. У этого приложения два намерения: OrderDrink и None. Намерение [None](luis-concept-intent.md#none-intent-is-fallback-for-app) является целенаправленным и указывает что-либо, не отвечающее предназначению приложения.  

## <a name="list-entity-is-an-exact-text-match"></a>Сущность списка представляет собой точное текстовое совпадение.
Целью сущности является поиск и категоризация частей текста во фразе. Сущность [списка](luis-concept-entity-types.md) допускает точное совпадение слов или фраз.  

Для этого приложения напитков LUIS извлекает заказ на напиток таким образом, чтобы можно было создать и заполнить стандартный заказ. В LUIS фразы могут включать вариации, аббревиатуры и сленг. 

Ниже приведены примеры простых фраз пользователей.

```
2 glasses of milk
3 bottles of water
2 cokes
```

Ниже приведены сокращенные или сленговые версии фраз.

```
5 milk
3 h2o
1 pop
```
 
Сущность списка сопоставляет `h2o` с водой, а `pop` с прохладительными напитками.  

## <a name="what-luis-does"></a>Действия приложения LUIS
Когда намерение и сущности фразы будут определены, [извлечены](luis-concept-data-extraction.md#list-entity-data) и возвращены в формате JSON из [конечной точки](https://aka.ms/luis-endpoint-apis), работа LUIS будет завершена. Вызывающее приложение или чат-бот принимает этот ответ JSON и выполняет запрос любым настроенным в нем способом. 

## <a name="create-a-new-app"></a>Создание нового приложения
1. Выполните вход на веб-сайте [LUIS][LUIS]. Войдите в [регион][LUIS-regions], где нужно опубликовать конечные точки LUIS.

2. На веб-сайте [LUIS][LUIS] выберите **Create new app** (Создать приложение).  

    ![Создание приложения](./media/luis-quickstart-intent-and-list-entity/app-list.png)

3. Во всплывающем диалоговом окне введите имя `MyDrinklist`. 

    ![Назовите приложение MyDrinkList.](./media/luis-quickstart-intent-and-list-entity/create-app-dialog.png)

4. После завершения процесса появится страница **намерений** с намерением **None**. 

    [![](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png "Снимок экрана со страницей намерений")](media/luis-quickstart-intent-and-list-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>Создание намерения

1. На странице **намерений** выберите **Create new intent** (Создать намерение). 

    [![](media/luis-quickstart-intent-and-list-entity/create-new-intent.png "Снимок экрана страницы намерений с выделенной кнопкой создания намерения")](media/luis-quickstart-intent-and-list-entity/create-new-intent.png#lightbox)

2. Введите имя нового намерения `OrderDrinks`. Это намерение следует выбирать каждый раз, когда пользователь хочет заказать напиток.

    Создавая намерение, вы создаете основную категорию информации, которую хотите идентифицировать. Благодаря присвоению имени категории любое другое приложение, использующее результаты запроса LUIS, по этому имени может найти подходящий ответ или предпринять соответствующее действие. LUIS не ответит на эти вопросы, а только определит, какой тип информации запрашивается на естественном языке. 

    [![](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png "Снимок экрана создания намерения OrderDrings")](media/luis-quickstart-intent-and-list-entity/intent-create-dialog-order-drinks.png#lightbox)

3. Добавьте в намерение `OrderDrinks` несколько фраз, которые вы ожидаете от пользователя, например:

    | Примеры фраз|
    |--|
    |Отправьте в комнату 2 колы и бутылку воды|
    |2 сидра с кусочком лайма|
    |h20|

    [![](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png "Снимок экрана ввода фразы на странице намерения OrderDrinks")](media/luis-quickstart-intent-and-list-entity/intent-order-drinks-utterance.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Добавление фраз в намерение None

В настоящее время приложение LUIS не содержит фраз для намерения **None**. Ему требуются фразы, на которые приложению не нужно отвечать. Их необходимо указать в намерении **None**. Заполните его. 

1. Выберите **намерения** на панели слева. 

    [![](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png "Снимок экрана выбора ссылки намерений на панели слева")](media/luis-quickstart-intent-and-list-entity/left-panel-intents.png#lightbox)

2. Выберите намерение **None**. Добавьте три фразы, которые может ввести пользователь, но которые не имеют отношения к приложению:

    | Примеры фраз|
    |--|
    |Отмена!|
    |До свидания!|
    |Что происходит?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>Фразы, которые попадут в намерение None
В приложении, вызывающем LUIS (таком как чат-бот), когда LUIS возвращает намерение **None** для фразы, бот может спросить, хочет ли пользователь завершить разговор. Бот может также дать больше указаний для продолжения разговора, если пользователь хочет продолжить. 

Сущности выполняются в намерении **None**. Если намерение с высшим показателем — **None**, но сущность извлечена, что важно для чат-бота, он может задать уточняющий вопрос, который сосредоточен на намерении клиента. 

## <a name="create-a-menu-entity-from-the-intent-page"></a>Создание сущности меню на странице намерения
Теперь, когда у двух намерений есть фразы, приложению LUIS нужно понять тип напитка. Вернитесь к намерению `OrderDrinks` и отметьте напитки во фразе, выполнив следующие шаги:

1. Вернитесь к намерению `OrderDrinks`, выбрав **намерения** на левой панели.

2. Выберите `OrderDrinks` из списка намерений.

3. Во фразе `Please send 2 cokes and a bottle of water to my room` выберите слово `water`. Появится раскрывающееся меню с текстовым полем в верхней области для создания сущности. Введите имя сущности `Drink` в текстовом поле, а затем выберите в раскрывающемся меню **Create new entity** (Создать сущность). 

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png "Снимок экрана создания сущности с помощью выбора слова во фразе")](media/luis-quickstart-intent-and-list-entity/intent-label-h2o-in-utterance.png#lightbox)

4. Во всплывающем окне выберите тип сущности **списка**. Добавьте синоним `h20`. Нажимайте клавишу ВВОД после каждого синонима. Не добавляйте `perrier` в список синонимов. Это добавление мы рассмотрим на следующем шаге. Нажмите кнопку **Готово**.

    [![](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png "Снимок экрана настройки новой сущности")](media/luis-quickstart-intent-and-list-entity/create-list-ddl.png#lightbox)

5. Теперь, когда сущность создана, отметьте другие синонимы воды, выбрав синоним для слова "вода", затем выберите `Drink` в раскрывающемся списке. Выберите меню справа, затем выберите `Set as synonym`, а затем — `water`.

    [![](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png "Снимок экрана пометки фразы с имеющейся сущностью")](media/luis-quickstart-intent-and-list-entity/intent-label-perriers.png#lightbox)

## <a name="modify-the-list-entity-from-the-entity-page"></a>Изменение сущности списка на странице "Сущность"
При создании сущности списка напитков она содержит немного элементов и синонимов. Если вы знаете некоторые термины, аббревиатуры и сленг, заполните список на странице **сущности**. Так быстрее всего. 

1. Выберите **Entities** (Сущности) на панели слева.

    [![](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png "Снимок экрана выбора параметра \"Entities\" (Сущности) на панели слева")](media/luis-quickstart-intent-and-list-entity/intent-select-entities.png#lightbox)

2. Выберите `Drink` из списка сущностей.

    [![](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png "Снимок экрана выбора сущности \"Drink\" (Напиток) из списка сущностей")](media/luis-quickstart-intent-and-list-entity/entities-select-drink-entity.png#lightbox)

3. В текстовом поле введите `Soda pop`, а затем нажмите клавишу ВВОД. Это термин, который широко применяется для газированных напитков. Для обозначения этого типа напитка в каждой культуре есть сокращение или сленг.

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png "Снимок экрана ввода канонического имени")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-canonical-name.png#lightbox)

4. В той же строке, что и `Soda pop`, введите синонимы, такие как: 

    ```
    coke
    cokes
    coca-cola
    coca-colas
    ```

    Синонимы могут включать в себя фразы, знаки препинания, притяжательные местоимения и множественные числа. Так как сущность списка является точным текстовым совпадением (кроме некоторых случаев), синонимы должны включать все варианты. Вы можете расширить список, когда узнаете больше вариантов из журналов запросов или просмотрите совпадения конечных точек. 

    В этой статье используется несколько синонимов для сохранения краткости примера. Приложение LUIS производственного уровня будет включать много синонимов и регулярно пересматриваться и расширяться. 

    [![](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png "Снимок экрана добавления синонимов")](media/luis-quickstart-intent-and-list-entity/drink-entity-enter-synonyms.png#lightbox)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
Приложение LUIS не знает об изменениях намерений и сущностей (модели), пока не будет обучено. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Обучение приложения](./media/luis-quickstart-intent-and-list-entity/train-button.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Обучение успешно выполнено](./media/luis-quickstart-intent-and-list-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки
Чтобы получить прогноз LUIS в чат-боте или другом приложении, необходимо опубликовать приложение. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Publish** (Опубликовать). 

    [![](media/luis-quickstart-intent-and-list-entity/publish.png "Снимок экрана нажатия кнопки публикации")](media/luis-quickstart-intent-and-list-entity/publish.png#lightbox)

2. Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать). 

    [![](media/luis-quickstart-intent-and-list-entity/publish-to-production.png "Снимок экрана нажатия кнопки публикации и выбора рабочего слота")](media/luis-quickstart-intent-and-list-entity/publish-to-production.png#lightbox)

3. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой
1. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

    [![](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png "Снимок экрана URL-адреса конечной точки на странице публикации")](media/luis-quickstart-intent-and-list-entity/publish-select-endpoint.png#lightbox)

2. Перейдите в конец URL-адреса и введите `2 cokes and 3 waters`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `OrderDrinks` с двумя типами напитков: `cokes` и `waters`.

```
{
  "query": "2 cokes and 3 waters",
  "topScoringIntent": {
    "intent": "OrderDrinks",
    "score": 0.999998569
  },
  "intents": [
    {
      "intent": "OrderDrinks",
      "score": 0.999998569
    },
    {
      "intent": "None",
      "score": 0.23884207
    }
  ],
  "entities": [
    {
      "entity": "cokes",
      "type": "Drink",
      "startIndex": 2,
      "endIndex": 6,
      "resolution": {
        "values": [
          "Soda pop"
        ]
      }
    },
    {
      "entity": "waters",
      "type": "Drink",
      "startIndex": 14,
      "endIndex": 19,
      "resolution": {
        "values": [
          "h20"
        ]
      }
    }
  ]
}
```

## <a name="where-is-the-natural-language-processing-in-the-list-entity"></a>Где происходит обработка естественного языка в сущности списка? 
Так как сущность списка представляет собой точное текстовое совпадение, она не полагается на обработку естественного языка (или машинное обучение). Приложение LUIS использует обработку естественного языка (или машинное обучение), чтобы выбрать намерение с наивысшим показателем. Кроме того, фраза может представлять собой сочетание нескольких сущностей или даже нескольких типов сущностей. Каждая фраза обрабатывается для всех сущностей в приложении, включая сущности обработки естественного языка (или машинного обучения), например **простая** сущность.

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение, состоящее всего из двух намерений и сущности списка, идентифицировало намерение запроса на естественном языке и вернуло извлеченные данные. 

Теперь у чат-бота достаточно сведений, чтобы определить основное действие `OrderDrinks` и типы напитков, которые были заказаны в сущности списка напитков. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат намерения с наивысшим показателем и данные из сущности, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Чтобы сделать это, щелкните меню с тремя точками (...) справа от имени приложения в списке приложений и выберите пункт **Delete** (Удалить). Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Сведения о добавлении сущности регулярных выражений](luis-quickstart-intents-regex-entity.md)

Добавьте **предварительно созданную сущность** [номера](luis-how-to-add-entities.md#add-prebuilt-entity), чтобы извлечь номер. 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
