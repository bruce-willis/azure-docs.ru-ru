---
title: 'Руководство по созданию приложения LUIS для извлечения данных: Azure | Документация Майкрософт'
description: В этом руководстве вы узнаете, как создать простое приложение LUIS, использующее намерения и простую сущность для извлечения данных машинного обучения.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265366"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Руководство по созданию приложения, использующего простую сущность
В этом руководстве описано создание приложения, которое показывает, как извлечь данные машинного обучения из фразы с помощью **простой** сущности.

<!-- green checkmark -->
> [!div class="checklist"]
> * Общие сведения о простых сущностях. 
> * Создание приложения LUIS для обмена данными с намерением SendMessage.
> * Добавление намерения _None_ и примеров фраз.
> * Добавление простой сущности, чтобы извлечь содержимое сообщения из фразы.
> * Тестирование и публикация приложения.
> * Запрос конечной точки приложения для просмотра ответа JSON LUIS.

Для работы с этой статьей требуется бесплатная учетная запись [LUIS][LUIS], в которой вы разработаете приложение LUIS.

## <a name="purpose-of-the-app"></a>Назначение приложения
Это приложение демонстрирует способы извлечения данных из фразы. Рассмотрите следующую фразу из чат-бота:

```JSON
Send a message telling them to stop
```

Предназначение намерения — отправка сообщений. Важные данные фразы — само сообщение, `telling them to stop`.  

## <a name="purpose-of-the-simple-entity"></a>Назначение простой сущности
Назначение простой сущности — научить LUIS тому, что такое сообщение и где его можно найти во фразе. Часть фразы, которая является сообщением, может изменяться в разных фразах в зависимости от подобранных слов и длины фразы. LUIS требуются примеры сообщений в любой фразе по всем намерениям.  

Для этого простого приложения сообщение будет расположено в конце фразы. 

## <a name="create-a-new-app"></a>Создание нового приложения
1. Выполните вход на веб-сайте [LUIS][LUIS]. Войдите в регион, где нужно опубликовать конечные точки LUIS.

2. На веб-сайте [LUIS][LUIS] выберите **Create new app** (Создать приложение).  

    ![Список приложений LUIS](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. Во всплывающем диалоговом окне введите имя `MyCommunicator`. 

    ![Список приложений LUIS](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. После завершения процесса появится страница **намерений** с намерением **None**. 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "Снимок экрана страницы намерений LUIS с намерением None")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>Создание намерения

1. На странице **намерений** выберите **Create new intent** (Создать намерение). 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "Снимок экрана LUIS с выделенной кнопкой \"Create new intent\" (Создать намерение)")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. Введите имя нового намерения `SendMessage`. Это намерение следует выбирать каждый раз, когда пользователь хочет отправить сообщение.

    Создавая намерение, вы создаете основную категорию информации, которую хотите идентифицировать. Благодаря присвоению имени категории любое другое приложение, использующее результаты запроса LUIS, по этому имени может найти подходящий ответ или предпринять соответствующее действие. LUIS не ответит на эти вопросы, а только определит, какой тип информации запрашивается на естественном языке. 

    ![Ввод имени намерения SendMessage](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. Добавьте в намерение `SendMessage`несколько фраз, которые вы ожидаете от пользователя, например:

    | Примеры фраз|
    |--|
    |Ответить: "Сообщение получено, отвечу завтра"|
    |Отправить сообщение: "Когда ты будешь дома"?|
    |Написать, что я занят|
    |Сообщить, что это необходимо сделать сегодня|
    |Отправить мгновенное сообщение, о том, что я еду на машине и отвечу позже|
    |Составить сообщение для Никиты с вопросом о том, что это было|
    |поприветствовать Николая|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "Снимок экрана LUIS с введенными фразами")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Добавление фраз в намерение None

В настоящее время приложение LUIS не содержит фраз для намерения **None**. Ему требуются фразы, на которые приложению не нужно отвечать. Их необходимо указать в намерении **None**. Заполните его. 
    
1. Выберите **намерения** на панели слева. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "Снимок экрана LUIS с выделенной кнопкой \"Intents\" (Намерения)")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. Выберите намерение **None**. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "Снимок экрана, где выбрано намерение None")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. Добавьте три фразы, которые может ввести пользователь, но которые не имеют отношения к приложению. Несколько подходящих фраз **None**:

    | Примеры фраз|
    |--|
    |Отмена!|
    |До свидания!|
    |Что происходит?|
    
    В приложении, вызывающем LUIS (таком как чат-бот), если LUIS возвращает намерение **None** для фразы, бот может спросить, хочет ли пользователь завершить разговор. Бот может также дать больше указаний для продолжения разговора, если пользователь хочет продолжить. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "Снимок экрана LUIS с фразами для намерения None")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>Создание простой сущности для извлечения сообщения 
1. Выберите **намерения** в меню слева.

    ![Выбор ссылки "Intents" (Намерения)](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. Выберите `SendMessage` из списка намерений.

    ![Выбор намерения SendMessage](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. Во фразе `Reply with I got your message, I will have the answer tomorrow` выберите первое слово текста сообщения (`I`) и последнее (`tomorrow`). Когда слова будут выбраны для сообщения, появится раскрывающееся меню с текстовым полем сверху.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "Снимок экрана выбора слов во фразе для сообщения")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. Введите имя сущности `Message` в текстовом поле.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "Снимок экрана ввода сущности в поле")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. Выберите **Create new entity** (Создать сущность) в раскрывающемся меню. Целью сущности является извлечение текста, который является текстом сообщения. В этом приложении LUIS текстовое сообщение находится в конце фразы. Однако как фраза, так и сообщение могут быть любой длины. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "Снимок экрана создания сущности из фразы")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. Во всплывающем окне тип сущности по умолчанию — **Простая**, а имя сущности — `Message`. Оставьте эти параметры и нажмите кнопку **Done** (Готово).

    ![Проверка типа сущности](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. Теперь, когда сущность создана, а одна фраза помечена, отметьте остальные фразы с этой сущностью. Выберите фразу, а затем — первое и последнее слово сообщения. В раскрывающемся меню выберите сущность `Message`. Теперь сообщение помечено в сущности. Пометьте все сообщения в оставшихся фразах.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "Снимок экрана всех помеченных фраз с сообщениями")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    Стандартное представление фраз — это **представление сущностей**. Выберите элемент управления **Entities view** (Представление сущностей) над фразами. В **представлении лексем** отображается текст фразы. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "Снимок экрана фраз в представлении лексем")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>Обучение приложения LUIS
Приложение LUIS не знает об изменениях намерений и сущностей (модели), пока не будет обучено. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Train** (Обучить).

    ![Кнопка обучения](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. Когда обучение будет завершено, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

    ![Уведомление о выполнении обучения](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Публикация приложения для получения URL-адреса конечной точки
Чтобы получить прогноз LUIS в чат-боте или другом приложении, необходимо опубликовать приложение. 

1. В верхней правой части веб-сайта LUIS нажмите кнопку **Publish** (Опубликовать). 

2. Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Снимок экрана страницы публикации с выделенной кнопкой \"Publish\" (Опубликовать) и выбранным слотом \"Production\" (Рабочий)")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. Когда публикация будет завершена, в верхней части веб-сайта появится зеленая панель состояния, свидетельствующая об успешном результате.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Запрос конечной точки с другой фразой
В нижней части страницы **публикации** выберите ссылку на **конечную точку**. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Снимок экрана страницы публикации с выделенной конечной точкой")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

В результате откроется другое окно браузера с URL-адресом конечной точки в адресной строке. Перейдите в конец URL-адреса и введите `text I'm driving and will be 30 minutes late to the meeting`. Последний параметр строки запроса — `q`. Это **запрос** фразы. Эта фраза не совпадает ни с какими помеченными фразами, поэтому она является хорошим тестом. В результате должны быть возвращены фразы `SendMessage`.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Результаты работы этого приложения LUIS
Это приложение, состоящее всего из двух намерений и одной сущности, идентифицировало намерение запроса на естественном языке и вернуло данные сообщения. 

Результат JSON определяет намерение `SendMessage` с высшим показателем (0,987501). Все оценки находятся в диапазоне 0–1, наилучшие оценки — те, которые ближе к 1. Оценка намерения `None` — 0,111048922, значительно ближе к нулю. 

Данные сообщения имеют тип `Message`, а также значение `i ' m driving and will be 30 minutes late to the meeting`. 

Ваш чат-бот теперь содержит достаточно информации для определения основного действия — `SendMessage` и параметра этого действия — текста сообщения. 

## <a name="where-is-this-luis-data-used"></a>Место использования этих данных приложения LUIS 
Приложение LUIS уже выполнило этот запрос. Вызывающее приложение, например чат-бот, может принять результат с наивысшим показателем и данные из сущности, чтобы отправить сообщение через сторонний API. Если существуют другие программные действия для бота или вызывающего приложения, LUIS не выполняет их. LUIS только определяет намерение пользователя. 

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Чтобы сделать это, щелкните меню с тремя точками (...) справа от имени приложения в списке приложений и выберите пункт **Delete** (Удалить). Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Руководство по созданию приложения, использующего иерархическую сущность](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
