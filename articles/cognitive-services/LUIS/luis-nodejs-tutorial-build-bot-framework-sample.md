---
title: Бот LUIS с Node.js — бот веб-приложения — Bot Framework SDK 3.0
titleSuffix: Azure Cognitive Services
description: Создайте бот, интегрированный с приложением LUIS, на основе платформы Bot Framework.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 4967c6c8eb9f849006beb78cfd2e41eba53b6867
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952976"
---
# <a name="luis-bot-in-nodejs"></a>Бот LUIS в Node.js

Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), используя Node.js. Для быстрой реализации решений ботов этот чат-бот использует предварительно созданный домен HomeAutomation. Бот создается с помощью Bot Framework 3.x и бота веб-приложения Azure.

## <a name="prerequisite"></a>Предварительные требования

Перед созданием бота выполните действия, описанные в разделе [Создание приложение](./luis-get-started-create-app.md), чтобы создать приложение LUIS, которое будет использовать бот.

Бот отвечает на намерения из домена HomeAutomation, которые находятся в приложении LUIS. Для каждого из этих намерений приложение LUIS предоставляет соответствующее намерение. Бот предоставляет диалоговое окно, которое обрабатывает намерение, обнаруженное LUIS.

| Намерение | Пример высказывания | Функция бота |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Turn on the lights (Включить свет). | Бот вызывает `TurnOnDialog` при обнаружении `HomeAutomation.TurnOn`. В этом диалоговом окне вы вызываете службу IoT для включения устройства и сообщаете пользователю, что устройство включено. |
| HomeAutomation.TurnOff | Turn off the bedroom lights (Выключить свет в спальне). | Бот вызывает `TurnOffDialog` при обнаружении `HomeAutomation.TurnOff`. В этом диалоговом окне вы вызываете службу IoT для выключения устройства и сообщаете пользователю, что устройство выключено. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Создание бота для распознавания речи с помощью службы Azure Bot

1. На [портале Azure](https://portal.azure.com) в колонке меню выберите **Создать ресурс**, а затем выберите **Показать все**.

    ![Создание ресурса](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. В поле поиска введите **Бот веб-приложения**. 

    ![Создание ресурса](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. В колонке **Служба ботов** введите необходимые сведения и нажмите кнопку **Создать**. В Azure будут созданы и развернуты служба ботов и приложение LUIS. Чтобы использовать функцию [подготовки речи](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), перед созданием бота ознакомьтесь с [требованиями к региону](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming). 
    * В поле **Имя приложения** укажите имя бота. При развертывании бота в облаке имя используется в качестве поддомена (например, mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Заполните поля "Подписка", [Группа ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), "План службы приложений" и [Расположение](https://azure.microsoft.com/regions/).
    * В поле **Шаблон бота** выберите:
        * **SDK v3**
        * **Node.js**
        * **Распознавание речи**
    * Выберите значение для поля **LUIS App Location** (Расположение приложения LUIS). Это [регион][LUIS] разработки, в котором создано приложение.
    * Установите флажок принятия юридического уведомления. Условия юридического уведомления приводятся под флажком.

    ![Колонка "Служба ботов"](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Убедитесь, что служба Bot Service развернута.
    * Щелкните "Уведомления" (значок колокольчика, расположенный в верхней части портала Azure). Уведомление изменится с **Развертывание начато** на **Развертывание выполнено**.
    * После того как уведомление изменится на **Развертывание выполнено**, в этом уведомлении выберите **Перейти к ресурсу**.

## <a name="try-the-default-bot"></a>Использование бота по умолчанию

Убедитесь, что бот был развернут, проверив раздел **Уведомления**. Уведомление изменится с **Развертывание выполняется...** на **Развертывание выполнено**. Чтобы открыть колонку ресурсов бота, нажмите кнопку **Перейти к ресурсу**.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Установка ресурсов NPM
Установите пакеты NPM, выполнив следующие действия:

1. Выберите **Сборка** в разделе **Управление ботом** на странице бота веб-приложения. 

2. Откроется второе окно браузера. Выберите **Открыть сетевой редактор кода**.

3. В верхней панели навигации выберите имя бота веб-приложения `homeautomationluisbot`. 

4. В раскрывающемся списке выберите **Открыть консоль Kudu**.

5. Откроется новое окно браузера. В консоли выполните следующую команду:

    ```
    cd site\wwwroot && npm install
    ```

    Дождитесь завершения установки. Вернитесь в первое окно браузера. 

## <a name="test-in-web-chat"></a>Тестирование в веб-чате
После регистрации бота выберите **Тестировать в веб-чате**, чтобы открыть панель веб-чата. В веб-чате введите "hello" (привет).

  ![Тестирование бота в веб-чате](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

Бот отвечает: "You have reached Greeting. You said: hello" (Вы находитесь на этапе приветствия. Вы сказали: привет). Этот ответ подтверждает, что бот получил ваше сообщение и передал его в созданное приложение LUIS по умолчанию. Это приложение LUIS по умолчанию обнаружило намерение приветствия. На следующем шаге вы подключите бот не к приложению LUIS по умолчанию, а к созданному ранее приложению LUIS.

## <a name="connect-your-luis-app-to-the-bot"></a>Подключение приложения LUIS к боту

Откройте окно **Параметры приложения** в первом окне браузера и измените значение в поле **LuisAppId** на идентификатор приложения LUIS.

  ![Обновление идентификатора приложения LUIS в Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Если у вас нет идентификатора приложения LUIS, войдите на веб-сайт [LUIS](luis-reference-regions.md) с помощью учетной записи, используемой для входа в Azure. Выберите **Мои приложения**. 

1. Найдите ранее созданное приложение LUIS, которое содержит намерения и сущности из предметной области HomeAutomation.

2. На странице **Параметры** приложения LUIS найдите и скопируйте идентификатор приложения.

3. Если вы еще не обучили приложение, нажмите кнопку **Обучение** в правом верхнем углу, чтобы обучить приложение.

4. Если вы еще не опубликовали приложение, нажмите кнопку **Опубликовать** на верхней панели навигации, чтобы открыть страницу **Публикация**. Выберите слот "Production" (Рабочий) и нажмите кнопку **Publish** (Опубликовать).

## <a name="modify-the-bot-code"></a>Изменение кода бота

Перейдите во второе окно браузера, если оно еще открыто, или останьтесь в первом окне браузера. Выберите **Сборка** и затем **Открыть сетевой редактор кода**.

   ![Открытие сетевого редактора кода](./media/luis-tutorial-node-bot/bot-service-build.png)

В редакторе кода откройте `app.js`. Он содержит следующий код:

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

Существующие намерения в файле app.js игнорируются. Их можно не изменять. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Добавление диалогового окна, соответствующего намерению HomeAutomation.TurnOn

Скопируйте следующий код и добавьте его в `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

Имя намерения указано в параметре [matches][matches] действия [triggerAction][triggerAction], связанного с диалоговым окном. Распознаватель запускается каждый раз, когда бот получает высказывание от пользователя. Если обнаруженное намерение с максимальной оценкой соответствует `triggerAction`, привязанному к диалоговому окно, бот вызывает это диалоговое окно.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Добавление диалогового окна, соответствующего намерению HomeAutomation.TurnOff

Скопируйте следующий код и добавьте его в `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Тестирование бота

На портале Azure выберите **Тестировать в веб-чате**, чтобы протестировать бот. Введите сообщения, такие как "Turn on the lights" (Включить свет) "turn off my heater" (Выключить нагреватель), чтобы вызвать добавленные намерения.
   ![Тестирование бота HomeAutomation веб-чате](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Если ваш бот не всегда распознает правильное намерение или сущности, необходимо улучшить производительность приложения LUIS, предоставив ему больше примеров высказываний для обучения. Вы можете повторно обучить приложение LUIS, не изменяя код бота. См. разделы [Добавление примеров высказываний](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) и [Обучение и тестирование приложений LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test).

## <a name="learn-more-about-bot-framework"></a>Дополнительные сведения о платформе Bot Framework
Ознакомьтесь с дополнительными сведениями о платформе [Bot Framework](https://dev.botframework.com/) и пакетах SDK версии [3.x](https://github.com/Microsoft/BotBuilder) и [4.x](https://github.com/Microsoft/botbuilder-js).

## <a name="next-steps"></a>Дополнительная информация

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> Вы можете добавить в приложение LUIS другие намерения, такие как Help, Cancel и Greeting. Затем добавьте диалоговые окна для новых намерений и протестируйте их с помощью бота. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Добавление намерений](./luis-how-to-add-intents.md)
> [Подготовка речи](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions

