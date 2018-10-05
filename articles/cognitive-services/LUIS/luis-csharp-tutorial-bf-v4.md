---
title: Бот LUIS с C# — Руководство. Бот веб-приложения — Bot Framework SDK 4.0
titleSuffix: Azure Cognitive Services
description: Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), используя C#. Для быстрой реализации решений ботов этот чат-бот использует приложение "Управление персоналом". Бот создается с помощью Bot Framework версии 4 и бота веб-приложения Azure.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/25/2018
ms.author: diberry
ms.openlocfilehash: f8350d46fecff726dd9f591fe3df0272f556b3e7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47168196"
---
# <a name="tutorial-luis-bot-in-c"></a>Руководство. Бот LUIS в C#
С помощью C# можно создавать чат-боты, интегрированные со службой распознавания речи (LUIS). Этот бот использует приложение HomeAutomation, чтобы реализовать решение ботов. Бот создается с помощью [бота веб-приложения Azure](https://docs.microsoft.com/azure/bot-service/) и [Bot Framework версии 4](https://github.com/Microsoft/botbuilder-js).

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание бота веб-приложения. Этот процесс создает новое приложение LUIS.
> * Добавление предварительно созданного домена в новую модель LUIS
> * Загрузка проекта, созданного веб-службой ботов
> * Запуск бота и эмулятора на локальном компьютере
> * Изменение кода ботов для новых намерений LUIS
> * Просмотр результатов высказывания в боте

## <a name="prerequisites"></a>Предварительные требования

* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Создание бота веб-приложения

1. На [портале Azure](https://portal.azure.com) выберите **Создание ресурса**.

2. С помощью поля поиска найдите и выберите **Web App Bot** (Бот веб-приложения). Нажмите кнопку **Создать**.

3. В **Службе Bot** введите необходимые сведения.

    |Параметр|Назначение|Предлагаемый параметр|
    |--|--|--|
    |Имя бота|Имя ресурса|`luis-csharp-bot-` + `<your-name>`, например `luis-csharp-bot-johnsmith`.|
    |Подписка|Подписка, в которой нужно создать бот.|Основная подписка.
    |Группа ресурсов|Логическая группа ресурсов Azure|Создайте группу для хранения всех ресурсов, используемых с этим ботом, и назовите эту группу `luis-csharp-bot-resource-group`.|
    |Расположение|Регион Azure не обязательно должен совпадать с регионом разработки или публикации LUIS.|`westus`|
    |Ценовой уровень|Используется для ограничения запросов службы и выставления счетов.|`F0` — уровень "Бесплатный".
    |Имя приложения.|При развертывании бота в облаке имя используется в качестве поддомена (например, humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, например `luis-csharp-bot-johnsmith`.|
    |Шаблон бота|Параметры Bot Framework (см. следующую таблицу)|
    |Расположение приложения LUIS|Должен совпадать с регионом ресурса LUIS|`westus`|

4. Выберите следующие компоненты в **параметрах шаблона бота**, а затем нажмите кнопку **Выбрать**:

    |Параметр|Назначение|Выбор|
    |--|--|--|
    |Версия пакета SDK|Версия Bot Framework|**Пакет SDK версии 4**|
    |Язык пакета SDK|Язык программирования бота|**C#**|
    |Echo или базовый бот|Тип бота|**Базовый бот**|
    
5. Нажмите кнопку **Создать**. В Azure будет создана и развернута служба ботов. Элементом этого процесса является создание приложения LUIS с именем `luis-csharp-bot-XXXX`. Это имя зависит от имен бота и приложения в предыдущем разделе.

    [ ![Создание бота веб-приложения](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

6. Не закрывайте эту вкладку браузера. Для каждого последующего шага на портале LUIS открывайте новую вкладку браузера. По завершении развертывания новой службы ботов перейдите к следующему разделу.

## <a name="add-prebuilt-domain-to-model"></a>Добавление предварительно созданного домена в модель
Элемент развертывания службы ботов создает новое приложение LUIS с намерениями и примерами фраз. В новом приложении LUIS бот предоставляет сопоставление намерений для следующих намерений: 

|Намерения базового бота LUIS|пример фразы|
|--|--|
|Отмена|`stop`|
|Greeting|`hello`|
|Справка|`help`|
|None|все, что находится за пределами домена приложения.|

Добавьте в модель предварительно созданное приложение HomeAutomation для обработки фраз, таких как: `Turn off the living room lights`

1. Перейдите на портал [LUIS](https://www.luis.ai) и войдите в систему.
2. Чтобы отсортировать приложения по дате создания, выберите столбец **Дата создания** на странице **Мои приложения**. В предыдущем разделе служба Azure Bot создала новое приложение. Его имя `luis-csharp-bot-` + `<your-name>` + 4 случайных символа.
3. Откройте приложение и выберите раздел **Сборка** на верхней панели навигации.
4. В левой панели навигации выберите **Предварительно созданные домены**
5. Выберите домен **HomeAutomation**, выбрав **Добавить домен** на карте.
6. В правом верхнем меню выберите **Обучить**.
7. В правом верхнем меню выберите **Опубликовать**. 

    Теперь приложение, созданное службой Azure Bot, имеет новые намерения:

    |Новые намерения базового бота|пример фразы|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Загрузка бота веб-приложения 
Чтобы разрабатывать код бота веб-приложения, загрузите код и используйте его на локальном компьютере. 

1. На портале Azure, по-прежнему в ресурсе бота веб-приложения, выберите **Параметры приложения** и скопируйте значения **botFilePath** и **botFileSecret**. Позже необходимо добавить эти значения в файл среды. 

2. На портале Azure в разделе **Управление ботом** выберите **Сборка**. 

3. Выберите **Загрузить исходный код бота**. 

    [ ![Загрузка исходного кода бота веб-приложения для базового бота](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Когда исходный код запакован, сообщение предоставит ссылку для загрузки кода. Перейдите по ссылке. 

5. Сохраните ZIP-файл на локальном компьютере и извлеките файлы. Откройте проект. 

6. Откройте файл bot.cs и найдите в нем `_services.LuisServices`. Это место, где фраза пользователя, введенная в бот, отправляется в LUIS.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    Бот отправляет фразу пользователя в LUIS и получает результаты. Главное намерение определяет последовательность общения. 


## <a name="start-the-bot"></a>Запуск бота
Прежде чем изменить какой-либо код или параметры, убедитесь, что бот работает. 

1. Откройте файл решения в Visual Studio. 

2. Создайте файл `appsettings.json` для хранения переменных бота, которые ищет код бота.

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    Задайте значения переменных для тех значений, которые вы скопировали из параметров приложения службы Azure Bot на шаге 1 в разделе **[Загрузка бота веб-приложения](#download-the-web-app-bot)**.

3. Запустите бот в Visual Studio. Откроется окно браузера с веб-сайтом бота веб-приложения на `http://localhost:3978/`.

## <a name="start-the-emulator"></a>Запуск эмулятора

1. Запустите Bot Emulator.

2. В Bot Emulator выберите файл с расширением *.bot в корневой папке проекта. Этот файл `.bot` включает конечную точку URL-адреса бота для сообщений:

    [ ![Bot Emulator версии 4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Введите секрет бота, который вы скопировали из параметров приложения службы Azure Bot на шаге 1 в разделе **[Загрузка бота веб-приложения](#download-the-web-app-bot)**. Это позволяет эмулятору получить доступ к любому зашифрованному полю в файле `.bot`.

    ![Секрет Bot Emulator версии 4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. В Bot Emulator введите `Hello` и получите корректный ответ базового бота.

    [ ![Ответ базового бота в эмуляторе](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Изменение кода бота 

Добавьте код для обработки новых намерений в файл `BasicBot.cs`. 

1. В верхней части файла найдите раздел **Поддерживаемые намерения LUIS** и добавьте константы для намерений HomeAutomation.

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    Обратите внимание, что точка `.` между доменом и намерением приложения портала LUIS заменяется на символ подчеркивания `_`. 

2. Найдите метод **OnTurnAsync**, который получает прогнозирование фразы LUIS. Добавьте код в оператор переключения, чтобы вернуть ответ LUIS для двух намерений HomeAutomation. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    Ответы бота и запросов REST API LUIS не полностью совпадают, поэтому важно изучить различия, посмотрев файл JSON ответа. Свойства текста и намерений одинаковы, но значения свойств сущности были изменены. 

    ```JSON
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```



## <a name="view-results-in-bot"></a>Просмотр результатов в боте

1. В эмуляторе бота введите фразу: `Turn on the livingroom lights to 50%`

2. Бот выдает:

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    ```

## Learn more about Bot Framework
Azure Bot service uses the Bot Framework SDK. Learn more about the SDK and bot framework:

* [Azure Bot Service](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 documentation
* [Bot Builder Samples](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder tools](https://github.com/Microsoft/botbuilder-tools):

## Next steps

You created an Azure bot service, copied the bot secret and `.bot` file path, downloaded the zip file of the code. You added the prebuilt HomeAutomation domain to the LUIS app created as part of the new Azure bot service, then trained and published the app again. You extracted the code project, created an environment file (`.env`), and set the bot secret and the `.bot` file path. In the bot.js file, you added code to handle the two new intents. Then you tested the bot in the bot emulator to see the LUIS response for an utterance of one of the new intents. 


> [!div class="nextstepaction"]
> [Build a custom domain in LUIS](luis-quickstart-intents-only.md)
