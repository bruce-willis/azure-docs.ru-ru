---
title: 'Руководство по созданию приложения LUIS для получения данных расположения: Azure | Документация Майкрософт'
description: В этом руководстве вы узнаете, как создать простое приложение LUIS, использующее намерения и иерархическую сущность для извлечения данных.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/22/2018
ms.author: v-geberr
ms.openlocfilehash: 6ba45de8ef41c8a57ca9c042a304e323a4fac263
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081699"
---
# <a name="tutorial-5-add-hierarchical-entity"></a>Учебник. 5. Добавление иерархической сущности
В этом руководстве создается приложение, которое показывает, как найти взаимосвязанные элементы данных с учетом контекста. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Основные сведения об иерархических сущностях и обученных по контексту дочерних элементах. 
> * Использование приложения LUIS в домене управления персоналом. 
> * Добавление иерархической сущности расположения с дочерними элементами исходного и целевого расположений.
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения, чтобы увидеть ответ JSON LUIS, в том числе иерархические дочерние элементы. 

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения управления персоналом из руководства по [сущностям списка](luis-quickstart-intent-and-list-entity.md), [импортируйте](create-new-app.md#import-new-app) файл JSON в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website). Приложение, которое следует импортировать, находится в репозитории Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-list-HumanResources.json).

Если вы хотите сохранить исходное приложение Human Resources, клонируйте версию на странице [Settings](luis-how-to-manage-versions.md#clone-a-version) (Параметры) и назовите его `hier`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. 

## <a name="purpose-of-the-app-with-this-entity"></a>Назначение приложения с этой сущностью
Это приложение определяет, в каких случаях сотрудник должен перемещаться из исходного (здание и офис) в целевое расположение (здание и офис). Для определения расположения в пределах фразы в этом приложении используется иерархическая сущность. 

Иерархическая сущность является оптимальным выбором для этого типа данных из-за двух блоков данных:

* Связаны между собой в контексте фразы.
* В них использовано конкретное слово, обозначающее каждое расположение. Примеры этих слов включают в себя: из/в, выход/переход, от/к.
* Они часто содержатся в одной фразе. 

Назначение **иерархической** сущности — находить связанные данные во фразе, исходя из контекста. Рассмотрите следующую фразу:

```JSON
mv Jill Jones from a-2349 to b-1298
```
Во фразе указано два расположения: `a-2349` и `b-1298`. Предположим, что буква соответствует названию здания и номеру офиса в этом здании. Логично, что оба эти значения сгруппированы как дочерние элементы иерархической сущности `Locations`, так как они связаны друг с другом и их необходимо извлечь из фразы. 
 
Если имеется только один дочерний элемент (источник или назначение) иерархической сущности, он по-прежнему будет извлечен. Не нужно находить все дочерние элементы, чтобы извлечь один или несколько. 

## <a name="remove-prebuilt-number-entity-from-app"></a>Удаление предварительно созданной сущности номера из приложения
Чтобы просмотреть всю фразу и пометить иерархические дочерние элементы, временно удалите предварительно созданную сущность номера.

1. Убедитесь, что приложение Human Resources находится в разделе **Build** (Создание) на веб-сайте LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

    [ ![Снимок экрана приложения LUIS со вкладкой "Build" (Создание), выделенной в верхней правой строке навигации](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png)](./media/luis-quickstart-intent-and-hier-entity/hr-first-image.png#lightbox)

2. Выберите **Entities** (Сущности) в меню слева.

    [ ![Снимок экрана приложения LUIS с кнопкой "Сущности", выделенной в меню слева](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entities-button.png#lightbox)


3. Выберите многоточие (...) справа от сущности номера в списке. Нажмите кнопку **Удалить**. 

    [ ![Снимок экрана приложения LUIS на странице списка сущностей с кнопкой удаления, выделенной для предварительно созданной сущности номера](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png)](./media/luis-quickstart-intent-and-hier-entity/hr-delete-number-prebuilt.png#lightbox)


## <a name="add-utterances-to-findform-intent"></a>Добавление фраз в намерение FindForm

1. Выберите **намерения** в меню слева.

    [ ![Снимок экрана приложения LUIS с выделенным разделом Intents (Намерения) в меню слева](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-intents-button.png#lightbox)

2. Выберите **MoveEmployee** из списка намерений.

    [ ![Снимок экрана приложения LUIS с выделенным намерением MoveEmployee в меню слева](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png)](./media/luis-quickstart-intent-and-hier-entity/hr-intents-list-moveemployee.png#lightbox)

3. Добавьте следующие примеры фраз:

    |Примеры фраз|
    |--|
    |Переместить Джона В. Смита **в** a-2345|
    |Направить Джил Джонс **в** b-3499|
    |Упорядочить перемещение x23456 **из** hh-2345 **в** e-0234|
    |Начать работу с документами, чтобы задать для x12345 **выход** из a-3459 и **переход** в f-34567|
    |Заменить 425-555-0000 **от** g-2323 **к** hh-2345|

    В соответствии с руководством по [списку сущностей](luis-quickstart-intent-and-list-entity.md) сотрудник может назначаться по имени, адресу электронной почты, дополнительному номеру телефона, номеру мобильного телефона или по федеральному номеру социальной страховки США. Эти номера сотрудников используются во фразе. Предыдущий пример фразы содержит различные способы, позволяющие записать исходное и целевое расположения и пометить их жирным шрифтом. В некоторых фразах используются только целевые расположения. Это позволяет приложению LUIS определить размещение этих расположений во фразе, если исходное расположение не указано.

    [ ![Снимок экрана приложения LUIS с новыми фразами в намерении MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)
     

## <a name="create-a-location-entity"></a>Создание сущности расположения
Приложению LUIS необходимо определить расположение, отмечая исходное и целевое расположения во фразах. Если необходимо просмотреть фразу в представлении токенов (необработанном представлении), выберите переключатель в строке над фразой с меткой **Entities View** (Представление сущностей). Выбрав переключатель, вы увидите, что этот элемент управления обозначен **Tokens View** (Представление токенов).

1. Во фразе `Displace 425-555-0000 away from g-2323 toward hh-2345` выберите слово `g-2323`. Появится раскрывающееся меню с текстовым полем в верхней области. Введите имя сущности `Locations` в текстовом поле, а затем выберите в раскрывающемся меню **Create new entity** (Создать сущность). 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png "Снимок экрана создания сущности на странице намерения")](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-1.png#lightbox)

2. Во всплывающем окне выберите **иерархический** тип сущности с `Origin` и `Destination` в качестве дочерних сущностей. Нажмите кнопку **Готово**.

    ![](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Снимок экрана всплывающего диалогового окна создания сущности для новой сущности \"Расположение\"")

3. Метка для `g-2323` помечается как `Locations`, так как LUIS не знает, это исходный город, город назначения или ни один из них. Выберите `g-2323`, затем выберите **Расположения**, а затем в меню справа выберите `Origin`.

    [![](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png "Снимок экрана всплывающего диалогового окна обозначения сущности для изменения расположения дочернего элемента сущности")](media/luis-quickstart-intent-and-hier-entity/hr-label-entity.png#lightbox)

5. Отметьте другие расположения во всех других фразах, выбрав здание и офис во фразе, а затем выбрав "Расположения", щелкнув меню справа, чтобы выбрать `Origin` или `Destination`. Если все расположения имеют метки, фразы в разделе **Tokens View** (Представление токенов) выглядят как шаблон. 

    [![](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png "Снимок экрана отмеченной во фразах сущности \"Расположения\"")](media/luis-quickstart-intent-and-hier-entity/hr-entities-labeled.png#lightbox)

## <a name="add-prebuilt-number-entity-to-app"></a>Добавление предварительно созданной сущности номера в приложение
Добавьте предварительно созданную сущность номера обратно в приложение.

1. Выберите **Сущности** в меню навигации слева.

    [ ![Снимок экрана, где кнопка "Сущности" выделена в меню навигации слева](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png)](./media/luis-quickstart-intent-and-hier-entity/hr-select-entity-button-from-intent-page.png#lightbox)

2. Нажмите кнопку **Manage prebuilt entities** (Управление предварительно созданными сущностями).

    [ ![Снимок экрана списка "Сущности" с выделенной кнопкой Manage prebuilt entities (Управление предварительно созданными сущностями)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png)](./media/luis-quickstart-intent-and-hier-entity/hr-manage-prebuilt-button.png#lightbox)

3. Выберите **номер** из списка предварительно созданных сущностей, а затем щелкните **Done** (Готово).

    ![Снимок экрана выбора номера в диалоговом окне предварительно созданных сущностей](./media/luis-quickstart-intent-and-hier-entity/hr-add-number-back-ddl.png)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
Приложение LUIS не знает об изменениях намерений и сущностей (модели), пока не будет обучено. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Обучение приложения](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Обучение успешно выполнено](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки
Чтобы получить прогноз LUIS в чат-боте или другом приложении, необходимо опубликовать приложение. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Publish** (Опубликовать). 

2. Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "Снимок экрана страницы публикации с выделенной кнопкой \"Publish\" (Опубликовать) и выбранным слотом \"Production\" (Рабочий)")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой
1. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "Снимок экрана страницы публикации с выделенным URL-адресом конечной точки")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. Перейдите в конец URL-адреса в адресной строке и введите `Please relocation jill-jones@mycompany.com from x-2345 to g-23456`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `MoveEmployee` с извлечением иерархической сущности.

```JSON
{
  "query": "Please relocation jill-jones@mycompany.com from x-2345 to g-23456",
  "topScoringIntent": {
    "intent": "MoveEmployee",
    "score": 0.9966052
  },
  "intents": [
    {
      "intent": "MoveEmployee",
      "score": 0.9966052
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0325253047
    },
    {
      "intent": "FindForm",
      "score": 0.006137873
    },
    {
      "intent": "GetJobInformation",
      "score": 0.00462633232
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00415637763
    },
    {
      "intent": "ApplyForJob",
      "score": 0.00382325822
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00249120337
    },
    {
      "intent": "None",
      "score": 0.00130756292
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.00119622645
    },
    {
      "intent": "Utilities.Confirm",
      "score": 1.26910036E-05
    }
  ],
  "entities": [
    {
      "entity": "jill - jones @ mycompany . com",
      "type": "Employee",
      "startIndex": 18,
      "endIndex": 41,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "x - 2345",
      "type": "Locations::Origin",
      "startIndex": 48,
      "endIndex": 53,
      "score": 0.8520272
    },
    {
      "entity": "g - 23456",
      "type": "Locations::Destination",
      "startIndex": 58,
      "endIndex": 64,
      "score": 0.974032
    },
    {
      "entity": "-2345",
      "type": "builtin.number",
      "startIndex": 49,
      "endIndex": 53,
      "resolution": {
        "value": "-2345"
      }
    },
    {
      "entity": "-23456",
      "type": "builtin.number",
      "startIndex": 59,
      "endIndex": 64,
      "resolution": {
        "value": "-23456"
      }
    }
  ]
}
```

## <a name="could-you-have-used-a-regular-expression-for-each-location"></a>Можно ли использовать регулярное выражение для каждого расположения?
Да, создайте регулярное выражение с исходной и целевой ролями и используйте его в шаблоне.

Расположения в этом примере, такие как `a-1234`, соответствуют определенному формату и состоят из одной или двух букв, дефиса и серии из 4–5 цифр. Эти данные можно описать как сущность регулярного выражения с ролью для каждого расположения. Роли доступны для шаблонов. Вы можете создать шаблоны на основе этих фраз, а затем создать регулярное выражение для формата расположения и добавить его в шаблоны. <!-- Go to this tutorial to see how that is done -->

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение, состоящее всего из нескольких намерений и иерархической сущности, идентифицировало намерение запроса на естественном языке и вернуло извлеченные данные. 

Ваш чат-бот теперь имеет достаточно информации для определения основного действия, `MoveEmployee`, а информацию о расположении можно найти во фразе. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат намерения с наивысшим показателем и данные из сущности, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Чтобы сделать это, щелкните меню с тремя точками (...) справа от имени приложения в списке приложений и выберите пункт **Delete** (Удалить). Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация
> [!div class="nextstepaction"] 
> Узнайте, как [добавить список сущностей](luis-quickstart-intent-and-list-entity.md). 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
