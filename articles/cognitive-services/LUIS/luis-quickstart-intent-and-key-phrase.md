---
title: 'Руководство по созданию приложения LUIS, которое возвращает ключевые фразы: Azure | Документы Майкрософт'
description: В этом руководстве вы узнаете, как добавить и вернуть сущность keyPhrase в приложение LUIS для анализа фраз на наличие запрашиваемого содержимого.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/27/2018
ms.author: v-geberr
ms.openlocfilehash: f3808620c4527f2971d8eb6d53a09c893b162b59
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340956"
---
# <a name="tutorial-7-add-keyphrase-entity"></a>Руководство: 7. Добавление сущности keyPhrase 
В этом руководстве используется приложение, демонстрирующее, как извлечь запрашиваемое содержимое из фраз.

<!-- green checkmark -->
> [!div class="checklist"]
> * Общие сведения о сущностях keyPhrase. 
> * Использование приложения LUIS в домене управления персоналом. 
> * Добавление сущности keyPhrase для извлечения содержимого из фразы.
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения, чтобы увидеть ответ JSON LUIS, в том числе ключевые фразы.

Для работы с этой статьей можно использовать бесплатную учетную запись [LUIS](luis-reference-regions.md#publishing-regions), в которой вы создадите приложение LUIS.

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения управления персоналом из руководства по [простым сущностям](luis-quickstart-primary-and-secondary-data.md), [импортируйте](create-new-app.md#import-new-app) файл JSON в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website). Приложение, которое следует импортировать, находится в репозитории Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-simple-HumanResources.json).

Если вы хотите сохранить исходное приложение Human Resources, клонируйте версию на странице [Settings](luis-how-to-manage-versions.md#clone-a-version) (Параметры) и назовите его `keyphrase`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. 

## <a name="keyphrase-entity-extraction"></a>Извлечение сущностей keyPhrase
Запрашиваемое содержимое находится в предварительно созданной сущности **keyPhrase**. Эта сущность возвращает запрашиваемое содержимое из фразы.

В следующих фразах содержатся примеры ключевых фраз.

|Фраза|Значения сущности keyPhrase|
|--|--|
|Имеется ли новый план медицинского обслуживания с меньшими отчислениями на следующий год?|"меньшие отчисления"<br>"новый план медицинского обслуживания"<br>"год"|
|Предусматривает ли план медицинского обслуживания с высокими отчислениями терапию для улучшения зрения?|"План медицинского обслуживания с высокими отчислениями"<br>"терапия для улучшения зрения"|

Клиентское приложение может использовать эти значения вместе с другими извлеченными сущностями, чтобы определить следующий шаг в диалоге.

## <a name="add-keyphrase-entity"></a>Добавление сущности keyPhrase 
Добавьте предварительно созданную сущность keyPhrase для извлечения запрашиваемого содержимого из фраз.

1. Убедитесь, что приложение Human Resources находится в разделе **Build** (Создание) на веб-сайте LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

    [ ![Снимок экрана приложения LUIS со вкладкой "Build" (Создание), выделенной в верхней правой строке навигации](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png)](./media/luis-quickstart-intent-and-key-phrase/hr-first-image.png#lightbox)

2. Выберите **Entities** (Сущности) в меню слева.

    [ ![Снимок экрана, где пункт "Entities" (Сущности) выделен в левой области раздела "Build" (Создание)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png)](./media/luis-quickstart-intent-and-key-phrase/hr-select-entities-button.png#lightbox)

3. Выберите **Manage prebuilt entities** (Управление предварительно созданными сущностями).

    [ ![Снимок экрана всплывающего диалогового окна, содержащего список сущностей](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-manage-prebuilt-entities.png#lightbox)

4. Во всплывающем диалоговом окне выберите **keyPhrase**, а затем нажмите кнопку **Done** (Готово). 

    [ ![Снимок экрана всплывающего диалогового окна, содержащего список сущностей](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->
5. В меню слева выберите **Intents** (Намерения) и щелкните **Utilities.Confirm**. Сущность keyPhrase помечена в нескольких фразах. 

    [ ![Снимок экрана с намерением Utilities.Confirm и сущностями keyPhrase, помеченными в фразах](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
Новую версию `keyphrase` приложения нужно обучить.  

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Обучение приложения](./media/luis-quickstart-intent-and-key-phrase/train-button.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Обучение успешно выполнено](./media/luis-quickstart-intent-and-key-phrase/trained.png)

## <a name="publish-app-to-endpoint"></a>Публикация приложения в конечной точке

1. Выберите **Publish** (Публикация) на правой верхней панели навигации.

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png "Снимок экрана страницы публикации с выделенной кнопкой \"Publish\" (Опубликовать) и выбранным слотом \"Production\" (Рабочий)")](media/luis-quickstart-intent-and-key-phrase/hr-publish-button-top-nav.png#lightbox)

2. Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

    [![](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png "Снимок экрана страницы публикации с выделенной кнопкой \"Publish\" (Опубликовать) и выбранным слотом \"Production\" (Рабочий)")](media/luis-quickstart-intent-and-key-phrase/hr-publish-to-production-expanded.png#lightbox)

3. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-an-utterance"></a>Запрос конечной точки с фразой

1. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

    ![Снимок экрана страницы публикации с выделенным URL-адресом конечной точки](media/luis-quickstart-intent-and-key-phrase/hr-endpoint-url-inline.png )

2. Перейдите в конец URL-адреса и введите `does form hrf-123456 cover the new dental benefits and medical plan`. Последний параметр строки запроса — `q`. Это **запрос** фразы. 

```
{
  "query": "does form hrf-123456 cover the new dental benefits and medical plan",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.9300641
  },
  "intents": [
    {
      "intent": "FindForm",
      "score": 0.9300641
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0359598845
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0141798034
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0112197418
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00507669244
    },
    {
      "intent": "None",
      "score": 0.00238501839
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00202810857
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00102957746
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0008688423
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.557994E-05
    }
  ],
  "entities": [
    {
      "entity": "hrf-123456",
      "type": "HRF-number",git 
      "startIndex": 10,
      "endIndex": 19
    },
    {
      "entity": "new dental benefits",
      "type": "builtin.keyPhrase",
      "startIndex": 31,
      "endIndex": 49
    },
    {
      "entity": "medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 55,
      "endIndex": 66
    },
    {
      "entity": "hrf",
      "type": "builtin.keyPhrase",
      "startIndex": 10,
      "endIndex": 12
    },
    {
      "entity": "-123456",
      "type": "builtin.number",
      "startIndex": 13,
      "endIndex": 19,
      "resolution": {
        "value": "-123456"
      }
    }
  ]
}
```

При поиске формы пользователь указал больше информации, чем требовалось. Дополнительные сведения возвращаются как **builtin.keyPhrase**. Клиентское приложение может использовать эти сведения для уточняющих вопросов, например "Вам хотелось бы обсудить новую стоматологическую страховку с сотрудником отдела кадров?" или предоставить меню с дополнительными вариантами, включая "Дополнительные сведения о новой стоматологической страховке или плане медицинского обслуживания".

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение с функцией обнаружения сущности keyPhrase распознало намерение запроса на естественном языке и вернуло извлеченные данные вместе с запрашиваемым содержимым. 

Ваш чат-бот теперь имеет достаточно информации для определения следующего шага в диалоге. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат с наивысшим показателем и данные keyPhrase из фразы, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Выберите **Мои приложения** в верхнем меню слева. Щелкните меню с тремя точками (…) справа от имени приложения в списке и выберите пункт **Удалить**. Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Добавление анализа тональности в приложение](luis-quickstart-intent-and-sentiment-analysis.md)

