---
title: 'Руководство по созданию приложения LUIS, которое возвращает анализ тональности: Azure | Документация Майкрософт'
description: В этом руководстве показано, как добавить анализ тональности в приложение LUIS, чтобы проанализировать фразы на наличие позитивных, негативных и нейтральных эмоций.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265340"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>Руководство по созданию приложения, которое возвращает тональность и прогноз намерения
В этом руководстве создается приложение, демонстрирующее, как извлечь позитивные, негативные и нейтральные тональности из фраз.

<!-- green checkmark -->
> [!div class="checklist"]
> * Основные сведения об иерархических сущностях и обученных по контексту дочерних элементах. 
> * Создание посвященного путешествиям приложения LUIS с намерением Bookflight.
> * Добавление намерения _None_ и примеров фраз.
> * Добавление иерархической сущности расположения с дочерними элементами исходного и целевого расположений.
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения, чтобы увидеть ответ JSON LUIS, в том числе иерархические дочерние элементы. 

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="sentiment-analysis"></a>Анализ мнений
Анализ тональности — это возможность определить, является ли фраза пользователя позитивной, негативной или нейтральной. 

Ниже приведены примеры фраз, передающие тональность.

|Тональность и оценка|Фраза|
|:--|--|
|позитивная тональность — 0,89 |Суп и салат были великолепны.|
|негативная тональность — 0,07 |Мне не понравилась закуска во время обеда.|

Анализ тональности — это параметр приложения, который применяется к каждой фразе. Вам не нужно находить слова, указывающие тональность во фразе, и отмечать их. LUIS будет делать это за вас.

## <a name="create-a-new-app"></a>Создание нового приложения
1. Выполните вход на веб-сайте [LUIS][LUIS]. Войдите в [регион][LUIS-regions], где нужно опубликовать конечные точки LUIS.

2. На веб-сайте [LUIS][LUIS] выберите **Create new app** (Создать приложение). 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "Снимок экрана страницы со списком приложений")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. В диалоговом окне **создания приложения** назовите приложение `Restaurant Reservations With Sentiment` и нажмите кнопку **Done** (Готово). 

    ![Изображение диалогового окна создания приложения](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    Когда приложение будет создано, в LUIS отобразится список намерений, который содержит намерение None.

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "Снимок экрана страницы списка намерений")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>Добавление предварительно созданной области
Добавьте предварительно созданную область, чтобы быстро добавлять намерения, сущности и помеченные фразы.

1. Выберите **Prebuilt Domains** (Предварительно созданные области) в меню слева.

    [ ![Снимок экрана с кнопкой "Prebuilt Domains" (Предварительно созданные области)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. Нажмите кнопку **Add domain** (Добавить область) для предварительно созданной области **RestaurantReservation**. Подождите, пока область не будет добавлена.

    [ ![Снимок экрана со списком предварительно созданных областей](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. В левой области навигации выберите **Intents** (Намерения). Эта предварительно созданная область имеет одно намерение.

    [ ![Снимок экрана списка предварительно созданных областей с выделенным пунктом "Intents" (Намерения) в области навигации слева](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  Выберите намерение **RestaurantReservation.Reserve**. 

    [ ![Снимок экрана списка намерений с выделенным намерением RestaurantReservation.Reserve](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. Переключитесь на **представление сущностей**, чтобы просмотреть фразы с помеченными сущностями конкретной области.

    [ ![Снимок экрана с намерением RestaurantReservation.Reserve и представлением сущностей, переключенным на представление лексем](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
Приложение LUIS не знает об изменениях намерений и сущностей (модели), пока не будет обучено. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Снимок экрана, где выделена кнопка "Train" (Обучить)](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Снимок экрана панели уведомлений, свидетельствующей об успешном обучении ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>Настройка приложения для включения анализа тональности
Анализ тональности можно включить на странице **публикации**. 

1. Выберите **Publish** (Публикация) на правой верхней панели навигации.

    ![Снимок экрана страницы намерений с выделенной кнопкой публикации ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. Выберите **Enable Sentiment Analysis** (Включить анализ тональности).

    ![Снимок экрана страницы публикации с выделенным параметром "Enable Sentiment Analysis" (Включить анализ тональности) ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "Снимок экрана страницы публикации с выделенной кнопкой \"Publish\" (Опубликовать) и выбранным слотом \"Production\" (Рабочий)")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-an-utterance"></a>Запрос конечной точки с фразой

1. В нижней части страницы **публикации** выберите ссылку на **конечную точку**. В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. 

    ![Снимок экрана страницы публикации с выделенным URL-адресом конечной точки](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. Перейдите в конец URL-адреса и введите `Reserve table for  10 on upper level away from kitchen`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должно быть возвращено намерение `RestaurantReservation.Reserve` с извлечением анализа тональности.

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение с включенным анализом тональности распознало намерение запроса на естественном языке и вернуло извлеченные данные вместе с общей тональностью в виде оценки. 

Ваш чат-бот теперь имеет достаточно информации для определения следующего шага в диалоге. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат с наивысшим показателем и данные тональности из фразы, чтобы выполнить следующий шаг. LUIS не выполняет программные действия за чат-бота или вызывающее приложение. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Чтобы сделать это, щелкните меню с тремя точками (...) справа от имени приложения в списке приложений и выберите пункт **Delete** (Удалить). Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"] 
> [Руководство по вызову конечной точки LUIS с помощью C#](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
