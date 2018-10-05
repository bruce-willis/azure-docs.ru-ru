---
title: Бот LUIS с Node.js. Руководство. Бот веб-приложения. Bot Framework SDK 4.0
titleSuffix: Azure Cognitive Services
description: Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), используя Node.js. Для быстрой реализации решений ботов этот чат-бот использует приложение "Управление персоналом". Бот создается с помощью Bot Framework версии 4 и бота веб-приложения Azure.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: acd9d9ff0b97bf0eaaca2f8ae9a6909e18e320d6
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47168184"
---
# <a name="tutorial-luis-bot-in-nodejs"></a>Руководство. Бот LUIS в Node.js
Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), с помощью Node.js. Этот бот использует приложение HomeAutomation, чтобы реализовать решение ботов. Бот создается с помощью [бота веб-приложения Azure](https://docs.microsoft.com/azure/bot-service/) и [Bot Framework версии 4](https://github.com/Microsoft/botbuilder-js).

**В этом руководстве рассмотрено, как выполнять следующие задачи.**

> [!div class="checklist"]
> * Создание бота веб-приложения. Этот процесс создает новое приложение LUIS.
> * Добавление предварительно созданного домена в новую модель LUIS
> * Загрузка проекта, созданного веб-службой ботов
> * Запуск бота и эмулятора на локальном компьютере
> * Изменение кода ботов для новых намерений LUIS
> * Просмотр результатов высказывания в боте

## <a name="prerequisites"></a>Предварительные требования

<!--* Samples from 
https://github.com/Microsoft/BotBuilder-Samples/tree/v4/javascript_nodejs/12.nlp-with-luis-->
* [Bot Emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>Создание бота веб-приложения

1. На [портале Azure](https://portal.azure.com) выберите **Создание ресурса**.

2. С помощью поля поиска найдите и выберите **Web App Bot** (Бот веб-приложения). Нажмите кнопку **Создать**.

3. В **Службе Bot** введите необходимые сведения.

    |Параметр|Назначение|Предлагаемый параметр|
    |--|--|--|
    |Имя бота|Имя ресурса|`luis-nodejs-bot-` + `<your-name>`, например `luis-nodejs-bot-johnsmith`|
    |Подписка|Подписка, в которой нужно создать бот.|Основная подписка.
    |Группа ресурсов|Логическая группа ресурсов Azure|Создайте группу для хранения всех ресурсов, используемых с этим ботом, и назовите эту группу `luis-nodejs-bot-resource-group`.|
    |Расположение|Регион Azure не обязательно должен совпадать с регионом разработки или публикации LUIS.|`westus`|
    |Ценовой уровень|Используется для ограничения запросов службы и выставления счетов.|`F0` — уровень "Бесплатный".
    |Имя приложения.|При развертывании бота в облаке имя используется в качестве поддомена (например, humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, например `luis-nodejs-bot-johnsmith`|
    |Шаблон бота|Параметры Bot Framework (см. следующую таблицу)|
    |Расположение приложения LUIS|Должно совпадать с регионом ресурса LUIS|`westus`|

4. Выберите следующие компоненты в **параметрах шаблона бота**, а затем нажмите кнопку **Выбрать**:

    |Параметр|Назначение|Выбор|
    |--|--|--|
    |Версия пакета SDK|Версия Bot Framework|**Пакет SDK версии 4**|
    |Язык пакета SDK|Язык программирования бота|**Node.js**|
    |Echo или базовый бот|Тип бота|**Базовый бот**|
    
5. Нажмите кнопку **Создать**. В Azure будет создана и развернута служба ботов. Элементом этого процесса является создание приложения LUIS с именем `luis-nodejs-bot-XXXX`. Это имя зависит от имен бота и приложения в предыдущем разделе.

    [ ![Создание бота веб-приложения](./media/bfv4-nodejs/create-web-app-service.png) ](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

6. Не закрывайте эту вкладку браузера. Откройте новую вкладку браузера для работы с порталом LUIS. После завершения развертывания новой службы ботов перейдите к следующему разделу.

## <a name="add-prebuilt-domain-to-model"></a>Добавление предварительно созданного домена в модель
Элемент развертывания службы ботов создает новое приложение LUIS с намерениями и примерами высказываний. В новом приложении LUIS бот предоставляет сопоставление намерений для следующих целей. 

|Намерения базового бота LUIS|Пример высказывания|
|--|--|
|Отмена|`stop`|
|Greeting|`hello`|
|Справка|`help`|
|None|Все, что находится за пределами домена приложения.|

Добавление предварительно созданного приложения HomeAutomation в модель для обработки высказывания, например `Turn off the living room lights`.

1. Перейдите на портал [LUIS](https://www.luis.ai) и войдите в систему.
2. Чтобы отсортировать приложения по дате создания, выберите столбец **Дата создания** на странице **My Apps** (Мои приложения). Служба Azure Bot создала новое приложение в предыдущем разделе. Его имя — `luis-nodejs-bot-` + `<your-name>` + 4 случайных символа.
3. Откройте приложение и выберите раздел **Сборка** на верхней панели навигации.
4. В левой области навигации выберите **Prebuilt Domains** (Предварительно созданные домены).
5. Выберите домен **HomeAutomation**, на его карте выбрав **Добавить домен**.
6. Выберите **Train** (Обучать) в правом верхнем меню.
7. Выберите **Опубликовать** в правом верхнем меню. 

    Приложение, созданное службой Azure Bot, теперь обладает новыми намерениями.

    |Новые намерения базового бота|Пример высказывания|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Загрузка бота веб-приложения 
Чтобы разработать код бота веб-приложения, загрузите код и используйте его на локальном компьютере. 

1. На портале Azure, по-прежнему в ресурсе бота веб-приложения, выберите **Параметры приложения** и скопируйте значения **botFilePath** и **botFileSecret**. Позже их необходимо добавить в файл среды. 

2. Выберите **Сборка** в разделе **Bot management** (Управление ботом) на портале Azure. 

3. Выберите **Download Bot source code** (Загрузка исходного кода бота). 

    [ ![Загрузка исходного кода бота веб-приложения для базового бота](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Когда исходный код запакован, появляется сообщение со ссылкой для загрузки кода. Перейдите по ссылке. 

5. Сохраните ZIP-файл на локальном компьютере и извлеките файлы. Откройте проект. 

6. Откройте файл bot.cs и введите `const results = await this.luisRecognizer.recognize(context);`. Здесь высказывание пользователя, введенное в бот, отправляется в LUIS.

    ```javascript
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    Бот отправляет высказывание пользователя в LUIS и возвращает результаты. Главное намерение определяет последовательность общения. 


## <a name="start-the-bot"></a>Запуск бота
Прежде чем изменить любой код или параметры, убедитесь, что бот работает. 

1. Откройте окно терминала в Visual Studio Code. 

2. Установите зависимости npm для этого бота. 

    ```bash
    npm install
    ```
3. Создайте файл для хранения переменных среды, которые ищет код бота. Назовите файл `.env`. Добавьте следующие переменные среды.

    <!--there is no code language that represents an .env file correctly-->
    ```
    botFilePath=
    botFileSecret=
    ```

    Задайте значениям переменных среды те значения, которые вы скопировали из параметров приложения службы Azure Bot на шаге 1 в разделе **[Загрузка бота веб-приложения](#download-the-web-app-bot)**.

4. Запустите бота в режиме просмотра. Любые изменения, внесенные в код после запуска, приведут к автоматическому перезапуску приложения.

    ```bash
    npm run watch
    ```

5. Когда бот запускается, в окне терминала отображается локальный порт, на котором работает бот.

    ```
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>Запуск эмулятора

1. Запустите Bot Emulator. 

2. В эмуляторе бота выберите файл *.bot в корневой папке проекта. Этот файл `.bot` включает в себя конечную точку URL-адреса бота для сообщений.

    [ ![Bot Emulator версии 4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Введите секрет бота, который вы скопировали из параметров приложения службы Azure Bot на шаге 1 в разделе **[Загрузка бота веб-приложения](#download-the-web-app-bot)**. Это позволит эмулятору получить доступ к любому зашифрованному полю в файле с расширением BOT.

    ![Секрет Bot Emulator версии 4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. В эмуляторе бота введите `Hello` и получите корректный ответ базового бота.

    [ ![Ответ базового бота в эмуляторе](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Изменение кода бота 

Добавьте код для обработки новых намерений в файл `bot.js`. 

1. В верхней части файла найдите раздел **Supported LUIS Intents** (Поддерживаемые намерения LUIS) и добавьте константы для намерений HomeAutomation.

    ```javascript
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    Обратите внимание, что точка `.` между доменом и намерением приложения портала LUIS заменяется на символ подчеркивания `_`. 

2. Найдите **isTurnInterrupted**, который получает прогнозирование фразы LUIS и добавляет строку для вывода результата на консоль.

    ```node
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
    ```

    Ответ бота не идентичен ответу запроса REST API LUIS, поэтому важно изучить различия, посмотрев на JSON ответа. Свойства текста и намерений одинаковы, но значения свойств сущности были изменены. 

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

3. Добавьте намерения в оператор switch метода onTurn для случая `DialogTurnStatus.empty`.

    ```javascript
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>Просмотр результатов в боте

1. В эмуляторе бота введите высказывание `Turn on the livingroom lights to 50%`.

2. Бот выдает следующее.

    ```JSON
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>Дополнительные сведения о платформе Bot Framework
Служба Azure Bot использует Bot Framework SDK. Дополнительные сведения о пакете SDK и платформе Bot Framework см. в следующих статьях.

* [Общие сведения о службе Azure Bot](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) версии 4
* [Bot Builder Samples](https://github.com/Microsoft/botbuilder-samples) (Примеры Bot Builder)
* [botbuilder-core package](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder tools](https://github.com/Microsoft/botbuilder-tools) (Средства для Bot Builder)

## <a name="next-steps"></a>Дополнительная информация

Вы создали службу Azure Bot, скопировали секрет бота и путь к файлу с расширением BOT, загрузили ZIP-файл кода. Вы добавили предварительно созданный домен HomeAutomation к приложению LUIS, созданному как часть новой службы Azure Bot, а затем обучили и опубликовали приложение снова. Вы извлекли проект кода, создали файл среды (`.env`) и задали секрет бота и путь к файлу с расширением BOT. Вы добавили код для обработки двух новых намерений в файл bot.js. Затем вы протестировали бота в Bot Emulator, чтобы увидеть ответ LUIS для высказывания одного из новых намерений. 


> [!div class="nextstepaction"]
> [Руководство: 1. Создание приложения с личным доменом](luis-quickstart-intents-only.md)
