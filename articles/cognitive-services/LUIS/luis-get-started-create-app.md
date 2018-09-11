---
title: Создайте первое приложение LUIS всего за 10 минут в Cognitive Services | Документация Майкрософт
description: В этом кратком руководстве описано, как создать приложение LUIS, которое использует предварительно созданную предметную область `HomeAutomation` для включения и отключения освещения и устройств. Эта предварительно созданная предметная область предоставляет для использования намерения, сущности и фразы. Когда все будет готово, вы получите работающую в облаке конечную точку LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "43771644"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Краткое руководство. Использование предварительно созданного приложения для системы домашней автоматики

В этом кратком руководстве описано, как создать приложение LUIS, которое использует предварительно созданную предметную область `HomeAutomation` для включения и отключения освещения и устройств. Эта предварительно созданная предметная область предоставляет для использования намерения, сущности и фразы. Когда все будет готово, вы получите работающую в облаке конечную точку LUIS.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей требуется бесплатная учетная запись LUIS, которую можно создать на портале LUIS по адресу [http://www.luis.ai](http://www.luis.ai). 

## <a name="create-a-new-app"></a>Создание нового приложения
Создавать и администрировать приложения можно на странице **Мои приложения**. 

1. Войдите на портал LUIS.

2. Выберите **Создать приложение**.

    [![](media/luis-quickstart-new-app/app-list.png "Снимок экрана со списком приложений")](media/luis-quickstart-new-app/app-list.png)

3. В диалоговом окне присвойте приложению имя Home Automation.

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "Снимок экрана всплывающего диалогового окна создания приложения")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Выберите язык и региональные параметры для приложения. Для нашего примера Home Automation выберите английский язык. Затем выберите **Готово**. LUIS создаст приложение Home Automation. 

    >[!NOTE]
    >Язык и региональные параметры нельзя изменить после создания приложения. 

## <a name="add-prebuilt-domain"></a>Добавление готовой предметной области

На панели навигации слева выберите **Prebuilt domains** (Предварительно созданные предметные области). Выполните поиск по строке Home. Выберите **Add domain** (Добавить предметную область).

[![](media/luis-quickstart-new-app/home-automation.png "Снимок экрана с выбором предметной области для приложения Home Automation из меню предварительно созданных предметных областей")](media/luis-quickstart-new-app/home-automation.png)

Когда предметная область будет успешно добавлена, в области предварительно созданных предметных областей отобразится кнопка **Remove domain** (Удалить предметную область).

[![](media/luis-quickstart-new-app/remove-domain.png "Снимок экрана с предметной областью Home Automation, рядом с которой отображается кнопку удаления")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Намерения и сущности

Выберите **Intents** (Намерения) в области навигации слева, чтобы просмотреть намерения из предметной области HomeAutomation. 

[![](media/luis-quickstart-new-app/home-automation-intents.png "Снимок экрана с таблицей намерений, где выделены имена намерений")](media/luis-quickstart-new-app/home-automation-intents.png)

По каждому намерению представлены примеры высказываний.

> [!NOTE]
> Намерение **None** (Нет) стандартно предоставляется всеми приложениями LUIS. Оно используется для обработки фраз, которые не соответствуют функциям приложения. 

Выберите намерение **HomeAutomation.TurnOff**. Вы увидите, что это намерение содержит список фраз с обозначенными сущностями.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "Снимок экрана с намерением HomeAutomation.TurnOff")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>Обучение приложения

Выберите **Train** (Обучить) в меню навигации сверху.

[![](media/luis-quickstart-new-app/trained.png "Снимок экрана с намерением HomeAutomation.TurnOff и зеленым уведомлением об успешном выполнении")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>Тестирование приложения
После обучения можно выполнить тестирование приложения. Щелкните **Test** (Тестировать) в меню навигации сверху. Введите тестовое высказывание, например Turn off the lights (Выключить свет) в области интерактивного тестирования и нажмите клавишу ВВОД. 

```
Turn off the lights
```

Убедитесь, что намерение с самой высокой оценкой для этого тестового высказывания выбрано правильно.

В нашем примере Turn off the lights (Выключить свет) правильно распознано как намерение HomeAutomation.TurnOff.

[![](media/luis-quickstart-new-app/test.png "Снимок экрана с панелью тестирования, на которой выделено намерение")](media/luis-quickstart-new-app/test.png)


Снова щелкните **Test** (Тестировать), чтобы свернуть панель тестирования. 

## <a name="publish-your-app"></a>Публикация приложения
Щелкните **Publish** (Опубликовать) в меню навигации сверху. 

[![](media/luis-quickstart-new-app/publish.png "Снимок экрана приложения, где выделена кнопка публикации")](media/luis-quickstart-new-app/publish.png)

Выберите слот Production (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

Зеленая панель уведомлений в верхней части указывает на успешную публикацию приложения.

[![](media/luis-quickstart-new-app/published.png "Снимок экрана приложения после успешной публикации")](media/luis-quickstart-new-app/published.png)

Когда публикация успешно завершится, можно использовать конечную точку, URL-адрес которой отображается на странице **Publish app** (Публикация приложения).

[![](media/luis-quickstart-new-app/endpoint.png "Снимок экрана страницы публикации, где выделен URL-адрес конечной точки")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>Использование приложения
Вы можете проверить работу опубликованной конечной точки с помощью браузера, используя созданный URL-адрес. Откройте этот URL-адрес и добавьте к тестовому запросу параметр URL-адреса &q. Например, добавьте `turn off the living room light` в конец URL-адреса и нажмите клавишу ВВОД. Браузер отображает ответ JSON для конечной точки HTTP.


[![](media/luis-quickstart-new-app/turn-off-living-room.png "Снимок экрана браузера с результатом в формате JSON, где обнаружено намерение TurnOff")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>Очистка ресурсов
Удалите приложение LUIS, если оно больше не нужно. Для этого нажмите кнопку многоточия (***...***) справа от имени приложения в списке и выберите **Удалить**. Во всплывающем диалоговом окне **Delete app?** (Удалить приложение?) нажмите кнопку **ОК**.

## <a name="next-steps"></a>Дополнительная информация

Эту конечную точку можно вызвать из кода:

> [!div class="nextstepaction"]
> [Руководство по вызову конечной точки LUIS с помощью кода](luis-get-started-cs-get-intent.md).
