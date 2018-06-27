---
title: 'Руководство по созданию приложения LUIS, которое возвращает ключевые фразы: Azure | Документация Майкрософт'
description: В этом руководстве вы узнаете, как добавить и вернуть сущность keyPhrase в приложение LUIS для анализа фраз на наличие запрашиваемого содержимого.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264621"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>Руководство по созданию приложения, которое возвращает данные сущности keyPhrases, найденные в фразах
В этом руководстве создается приложение, демонстрирующее, как извлечь запрашиваемое содержимое из фраз.

<!-- green checkmark -->
> [!div class="checklist"]
> * Общие сведения о сущностях keyPhrase. 
> * Создание приложения LUIS, предназначенного для работы с кадровыми ресурсами.
> * Добавление намерения _None_ и примеров фраз.
> * Добавление сущности keyPhrase для извлечения содержимого из фразы.
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS.

Для работы с этой статьей можно использовать бесплатную учетную запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="keyphrase-entity-extraction"></a>Извлечение сущностей keyPhrase
Запрашиваемое содержимое содержится в предварительно созданной сущности **keyPhrase**. Эта сущность возвращает запрашиваемое содержимое из фразы.

В следующих фразах содержатся примеры ключевых фраз.

|Фраза|Значения сущности keyPhrase|
|--|--|
|Имеется ли новый план медицинского обслуживания с меньшими отчислениями на следующий год?|"меньшие отчисления"<br>"новый план медицинского обслуживания"<br>"год"|
|Предусматривает ли план медицинского обслуживания с высокими отчислениями терапию для улучшения зрения?|"План медицинского обслуживания с высокими отчислениями"<br>"терапия для улучшения зрения"|

Ваш чат-бот может рассмотреть эти значения в дополнение ко всем другим извлеченным сущностям при принятии решения относительно следующего шага в диалоге.

## <a name="download-sample-app"></a>Скачивание примера приложения
Загрузите приложение [Human Resources](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) и сохраните его в файле с расширением *.json. Этот пример приложения распознает фразы, относящиеся к надбавкам сотрудников, структурным схемам организации и физическим ресурсам.

## <a name="create-a-new-app"></a>Создание нового приложения
1. Выполните вход на веб-сайте [LUIS][LUIS]. Войдите в [регион][LUIS-regions], где нужно опубликовать конечные точки LUIS.

2. На веб-сайте [LUIS][LUIS] выберите **Import new app** (Импорт нового приложения), чтобы импортировать приложение Human Resources, загруженное в предыдущем разделе. 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "Снимок экрана страницы со списком приложений")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. В диалоговом окне **импорта нового приложения** укажите для приложения имя `Human Resources with Key Phrase entity`. 

    ![Изображение диалогового окна создания приложения](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    Когда приложение будет создано, в LUIS отобразится список намерений.

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "Снимок экрана страницы списка намерений")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>Добавление сущности keyPhrase 
Добавьте предварительно созданную сущность keyPhrase для извлечения запрашиваемого содержимого из фраз.

1. Выберите **Entities** (Сущности) в меню слева.

    [ ![Снимок экрана, где пункт "Entities" (Сущности) выделен в левой области раздела "Build" (Создание)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. Выберите **Manage prebuilt entities** (Управление предварительно созданными сущностями).

    [ ![Снимок экрана всплывающего диалогового окна, содержащего список сущностей](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. Во всплывающем диалоговом окне выберите **keyPhrase**, а затем нажмите кнопку **Done** (Готово). 

    [ ![Снимок экрана всплывающего диалогового окна, содержащего список сущностей](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
Приложение LUIS не знает об этом изменении модели, пока не будет обучено. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Снимок экрана, где выделена кнопка "Train" (Обучить)](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Снимок экрана панели уведомлений, свидетельствующей об успешном обучении ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>Публикация приложения в конечной точке

1. Выберите **Publish** (Публикация) на правой верхней панели навигации.

    ![Снимок экрана страницы сущностей с выделенной кнопкой публикации ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "Снимок экрана страницы публикации с выделенной кнопкой \"Publish\" (Опубликовать) и выбранным слотом \"Production\" (Рабочий)")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-an-utterance"></a>Запрос конечной точки с фразой

1. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

    ![Снимок экрана страницы публикации с выделенным URL-адресом конечной точки](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. Перейдите в конец URL-адреса и введите `Is there a new medical plan with a lower deductible offered next year?`. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение с функцией обнаружения сущности keyPhrase распознало намерение запроса на естественном языке и вернуло извлеченные данные вместе с запрашиваемым содержимым. 

Ваш чат-бот теперь имеет достаточно информации для определения следующего шага в диалоге. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат с наивысшим показателем и данные keyPhrase из фразы, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Чтобы сделать это, щелкните меню с тремя точками (...) справа от имени приложения в списке приложений и выберите пункт **Delete** (Удалить). Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Создание приложения, которое возвращает тональность и прогноз намерения](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
