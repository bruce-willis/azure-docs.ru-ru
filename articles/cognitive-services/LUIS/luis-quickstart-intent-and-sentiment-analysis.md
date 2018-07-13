---
title: 'Руководство по созданию приложения LUIS, которое возвращает анализ тональности: Azure | Документы Майкрософт'
description: В этом руководстве показано, как добавить анализ тональности в приложение LUIS, чтобы проанализировать фразы на наличие позитивных, негативных и нейтральных эмоций.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: v-geberr
ms.openlocfilehash: 1a48810287c1639910db8e39af2da61d836b2988
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37340939"
---
# <a name="tutorial-8--add-sentiment-analysis"></a>Руководство: 8.  Добавление анализа тональности.
В этом руководстве создается приложение, демонстрирующее, как извлечь позитивные, негативные и нейтральные тональности из фраз.

<!-- green checkmark -->
> [!div class="checklist"]
> * Понимание анализа тональности.
> * Использование приложения LUIS в домене управления персоналом. 
> * Добавление анализа тональности.
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS. 

Для работы с этой статьей требуется бесплатная учетная запись [LUIS](luis-reference-regions.md#luis-website), в которой вы создадите приложение LUIS.

## <a name="before-you-begin"></a>Перед началом работы
Если у вас нет приложения для управления персоналом, созданного с помощью руководства по [предварительно созданной сущности keyPhrase](luis-quickstart-intent-and-key-phrase.md), [импортируйте](create-new-app.md#import-new-app) файл JSON в новое приложение на веб-сайте [LUIS](luis-reference-regions.md#luis-website). Приложение, которое следует импортировать, находится в репозитории Github [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-keyphrase-HumanResources.json).

Если вы хотите сохранить исходное приложение Human Resources, клонируйте версию на странице [Settings](luis-how-to-manage-versions.md#clone-a-version) (Параметры) и назовите его `sentiment`. Клонирование — это отличный способ поэкспериментировать с различными функциями LUIS без влияния на исходную версию. 

## <a name="sentiment-analysis"></a>Анализ мнений
Анализ тональности — это возможность определить, является ли фраза пользователя позитивной, негативной или нейтральной. 

Ниже приведены примеры фраз, передающие тональность.

|Мнение|Оценка|Фраза|
|:--|:--|:--|
|Позитивная|0,91 |Тарас Хмелев проделал отличную работу на презентации в Париже.|
|Позитивная|0,84 |Сотрудник jill-jones@mycompany.com отлично поработал над презентацией Parker.|

Анализ тональности — это параметр приложения, который применяется к каждой фразе. Вам не нужно искать и выделять слова, указывающие на тональность в каждой фразе, так как анализ тональности применяется ко всей фразе целиком. 

## <a name="add-employeefeedback-intent"></a>Добавление намерения EmployeeFeedback 
Добавьте новое намерение, чтобы получить отзывы о сотрудниках от других работников компании. 

1. Убедитесь, что приложение Human Resources находится в разделе **Build** (Создание) на веб-сайте LUIS. Вы можете перейти к этому разделу, выбрав **Build** (Создание) в верхней правой строке меню. 

    [ ![Снимок экрана приложения LUIS со вкладкой "Build" (Создание), выделенной в верхней правой строке навигации](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-first-image.png#lightbox)

2. Выберите **Create new intent**. (Создать намерение).

    [ ![Снимок экрана приложения LUIS со вкладкой "Build" (Создание), выделенной в верхней правой строке навигации](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent.png#lightbox)

3. В качестве имени нового намерения введите `EmployeeFeedback`.

    ![Диалоговое окно для создания намерения с именем EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Добавьте несколько выражений, чтобы указать на хорошую работу сотрудника или область, в которой требуются улучшения:

    Помните, что в этом приложении для управления персоналом сотрудники определяются в сущности списка `Employee` по имени, электронной почте, добавочному номеру телефона, номеру мобильного телефона и номеру социального страхования в США. 

    |Высказывания|
    |--|
    |Сотрудник 425-555-1212 хорошо проявил себя при организации встречи для коллеги, вернувшейся из декретного отпуска.|
    |Сотрудник 234-56-7891 хорошо проявил себя, оказав поддержку коллеге во время траура.|
    |Сотрудник jill-jones@mycompany.com не располагал всеми необходимыми счетами для оформления документов.|
    |Сотрудник john.w.smith@mycompany.com вернул необходимые бланки с опозданием на месяц и без подписей.|
    |Сотрудник x23456 не явился на важную выездную встречу маркетологов.|
    |Сотрудник x12345 пропустил собрание для обсуждения показателей за июнь.|
    |Мария Шевченко подготовила прекрасное коммерческое предложение для встречи в Лондоне.|
    |Тарас Хмелев проделал отличную работу на презентации в Мюнхене.|

    [ ![Снимок экрана с приложением LUIS и примерами фраз в намерении EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
LUIS не располагает сведениями о новом намерении и его примерных фразах, пока не будет проведено обучение. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Снимок экрана, где выделена кнопка "Train" (Обучить)](./media/luis-quickstart-intent-and-sentiment-analysis/train-button.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Снимок экрана панели уведомлений, свидетельствующей об успешном обучении ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-trained-inline.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Настройка приложения для включения анализа тональности
Настройте анализ тональности на странице **публикации**. 

1. Выберите **Publish** (Публикация) на правой верхней панели навигации.

    ![Снимок экрана страницы намерений с выделенной кнопкой публикации ](./media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-button-in-top-nav-highlighted.png)

2. Выберите **Enable Sentiment Analysis** (Включить анализ тональности). Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png "Снимок экрана страницы публикации с выделенной кнопкой \"Publish\" (Опубликовать) и выбранным слотом \"Production\" (Рабочий)")](media/luis-quickstart-intent-and-sentiment-analysis/hr-publish-to-production-expanded.png#lightbox)

4. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-an-utterance"></a>Запрос конечной точки с фразой

1. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

    ![Снимок экрана страницы публикации с выделенным URL-адресом конечной точки](media/luis-quickstart-intent-and-sentiment-analysis/hr-endpoint-url-inline.png)

2. Перейдите в конец URL-адреса и введите `Jill Jones work with the media team on the public portal was amazing`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `EmployeeFeedback` с извлечением анализа тональности.

```
{
  "query": "Jill Jones work with the media team on the public portal was amazing",
  "topScoringIntent": {
    "intent": "EmployeeFeedback",
    "score": 0.4983256
  },
  "intents": [
    {
      "intent": "EmployeeFeedback",
      "score": 0.4983256
    },
    {
      "intent": "MoveEmployee",
      "score": 0.06617523
    },
    {
      "intent": "GetJobInformation",
      "score": 0.04631853
    },
    {
      "intent": "ApplyForJob",
      "score": 0.0103248553
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.007531875
    },
    {
      "intent": "FindForm",
      "score": 0.00344597152
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00337914471
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0026357458
    },
    {
      "intent": "None",
      "score": 0.00214573368
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00157622492
    },
    {
      "intent": "Utilities.Confirm",
      "score": 7.379545E-05
    }
  ],
  "entities": [
    {
      "entity": "jill jones",
      "type": "Employee",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          "Employee-45612"
        ]
      }
    },
    {
      "entity": "media team",
      "type": "builtin.keyPhrase",
      "startIndex": 25,
      "endIndex": 34
    },
    {
      "entity": "public portal",
      "type": "builtin.keyPhrase",
      "startIndex": 43,
      "endIndex": 55
    },
    {
      "entity": "jill jones",
      "type": "builtin.keyPhrase",
      "startIndex": 0,
      "endIndex": 9
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8694164
  }
}
```

Анализ тональности является положительным с оценкой 0,86. 

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение с включенным анализом тональности распознало намерение запроса на естественном языке и вернуло извлеченные данные вместе с общей тональностью в виде оценки. 

Ваш чат-бот теперь имеет достаточно информации для определения следующего шага в диалоге. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат с наивысшим показателем и данные тональности из фразы, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Выберите **Мои приложения** в верхнем меню слева. Щелкните меню с тремя точками (…) справа от имени приложения в списке и выберите пункт **Удалить**. Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"] 
> [Руководство по вызову конечной точки LUIS с помощью C#](luis-get-started-cs-get-intent.md) 

