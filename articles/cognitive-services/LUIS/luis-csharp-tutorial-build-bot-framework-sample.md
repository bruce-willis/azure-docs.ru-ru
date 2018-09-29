---
title: Бот LUIS с C# — Бот веб-приложения — Bot Framework SDK 3.0
titleSuffix: Azure Cognitive Services
description: Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), используя C#. Для быстрой реализации решений ботов этот чат-бот использует предварительно созданный домен HomeAutomation.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 0ad95d5683ea10803c2f3986be97617051f191cf
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47032280"
---
# <a name="luis-bot-in-c"></a>Бот LUIS на C#

Создавайте чат-боты, интегрированные со службой распознавания речи (LUIS), используя C#. Для быстрой реализации решений ботов этот чат-бот использует предварительно созданный домен HomeAutomation. Бот создается с помощью Bot Framework 3.x и бота веб-приложения Azure.

## <a name="prerequisite"></a>Предварительные требования

* [Приложение LUIS HomeAutomation](luis-get-started-create-app.md). Намерения из этого приложения LUIS соответствуют обработчикам диалогов бота. 

## <a name="luis-homeautomation-intents"></a>Намерения приложения LUIS HomeAutomation

| Намерение | Пример высказывания | Функция бота |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Turn on the lights (Включить свет). | При обнаружении намерения LUIS `HomeAutomation.TurnOn` бот вызывает обработчик диалога `OnIntent`. В этом диалоге вы вызываете службу IoT для включения устройства и сообщаете пользователю, что устройство включено. |
| HomeAutomation.TurnOff | Turn off the bedroom lights (Выключить свет в спальне). | При обнаружении намерения LUIS `HomeAutomation.TurnOff` бот вызывает обработчик диалога `OffIntent`. В этом диалоге вы вызываете службу IoT для выключения устройства и сообщаете пользователю, что устройство выключено. |

## <a name="create-a-language-understanding-bot-with-bot-service"></a>Создание бота для распознавания речи с помощью службы Azure Bot

1. На [портале Azure](https://portal.azure.com) в верхнем левом меню выберите пункт **Создать ресурс**.

    ![Создание ресурса](./media/luis-tutorial-cscharp-web-bot/bot-service-creation.png)

2. В поле поиска введите **Бот веб-приложения**. 

    ![Создание ресурса](./media/luis-tutorial-cscharp-web-bot/bot-service-selection.png)

3. В окне бота веб-приложения нажмите кнопку **Создать**.

4. В окне **Служба Bot Service** введите необходимые сведения и нажмите кнопку **Создать**. В Azure будут созданы и развернуты служба ботов и приложение LUIS. Чтобы использовать функцию [подготовки речи](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), перед созданием бота ознакомьтесь с [требованиями к региону](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming). 
    * В поле **Имя приложения** укажите имя бота. При развертывании бота в облаке имя используется в качестве поддомена (например, mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Заполните поля "Подписка", [Группа ресурсов](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), "План службы приложений" и [Расположение](https://azure.microsoft.com/regions/).
    * В поле **Шаблон бота** выберите:
        * **SDK v3**
        * **C#**
        * **Распознавание речи**
    * Выберите значение для поля **LUIS App Location** (Расположение приложения LUIS). Это [регион](luis-reference-regions.md) разработки, в котором создано приложение.
    * Установите флажок принятия юридического уведомления. Условия юридического уведомления приводятся под флажком.

    ![Служба Bot Service](./media/luis-tutorial-cscharp-web-bot/bot-service-setting-callout-template.png)


5. Убедитесь, что служба Bot Service развернута.
    * Щелкните "Notifications" (Уведомления) (значок колокольчика, расположенный в верхней части портала Azure). Уведомление изменится с **Развертывание начато** на **Развертывание прошло успешно**.
    * После того как уведомление изменится на **Развертывание прошло успешно**, в этом уведомлении щелкните **Go to resource** (Перейти к ресурсу).

> [!Note]
> В ходе этого процесса создания бота веб-приложения автоматически создается приложение LUIS. Оно уже прошло обучение и опубликовано. 

## <a name="try-the-default-bot"></a>Использование бота по умолчанию

Убедитесь, что бот был развернут, установив флажок **Notifications** (Уведомления). Уведомление изменится с **Deployment in progress...** (Выполняется развертывание) на **Развертывание прошло успешно**. Нажмите кнопку **Go to resource** (Перейти к ресурсу), чтобы открыть ресурсы бота.

После развертывания бота выберите пункт **Test in Web Chat** (Тестировать в веб-чате), чтобы открыть панель веб-чата. В веб-чате введите "hello" (привет).

  ![Тестирование бота в веб-чате](./media/luis-tutorial-cscharp-web-bot/bot-service-web-chat.png)

Бот отвечает: "You have reached Greeting. You said: hello" (Вы находитесь на этапе приветствия. Вы сказали: привет).  Этот ответ подтверждает, что бот получил ваше сообщение и передал его в созданное приложение LUIS по умолчанию. Это приложение LUIS по умолчанию обнаружило намерение приветствия. На следующем шаге вы подключите бот не к приложению LUIS по умолчанию, а к созданному ранее приложению LUIS.

## <a name="connect-your-luis-app-to-the-bot"></a>Подключение приложения LUIS к боту

Откройте окно **Параметры приложения** и измените значение в поле **LuisAppId** на идентификатор приложения LUIS. Если приложение LUIS HomeAutomation создано в регионе, отличном от западной части США, необходимо также изменить значение в поле **LuisAPIHostName**. В качестве значения **LuisAPIKey** сейчас задан ключ разработки. Когда трафик превысит квоту уровня "Бесплатный", это значение следует изменить на ключ конечной точки. 

  ![Обновление идентификатора приложения LUIS в Azure](./media/luis-tutorial-cscharp-web-bot/bot-service-app-settings.png)

> [!Note]
> Если у вас нет идентификатора приложения LUIS для [приложения Home Automation](luis-get-started-create-app.md), войдите на веб-сайт [LUIS](luis-reference-regions.md) с помощью учетной записи, используемой для входа в Azure. 
> 1. Щелкните **Мои приложения**. 
> 2. Найдите ранее созданное приложение LUIS, которое содержит намерения и сущности из предметной области HomeAutomation.
> 3. На странице **Параметры** приложения LUIS найдите и скопируйте идентификатор приложения. Приложение должно быть [обучено](luis-interactive-test.md) и [опубликовано](luis-how-to-publish-app.md). 

    > [!WARNING]
    > If you delete your app ID or LUIS key, the bot will stop working.

## <a name="modify-the-bot-code"></a>Изменение кода бота

1. Выберите пункт **Build** (Сборка) и щелкните ссылку **Open online code editor** (Открыть сетевой редактор кода).

   ![Открытие сетевого редактора кода](./media/luis-tutorial-cscharp-web-bot/bot-service-build.png)

2. Щелкните правой кнопкой мыши `build.cmd` и выберите пункт **Run from Console** (Запустить из консоли), чтобы выполнить сборку приложения. Служба автоматически выполнит ряд шагов сборки. Сборка завершена, когда будет выведено сообщение "Finished successfully" (Успешно завершено).

3. В редакторе кода откройте `/Dialogs/BasicLuisDialog.cs`. Он содержит следующий код:

   [!code-csharp[Default BasicLuisDialog.cs](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/Default_BasicLuisDialog.cs "Default BasicLuisDialog.cs")]

## <a name="change-code-to-homeautomation-intents"></a>Изменение кода на намерения HomeAutomation


1. Удалите три атрибута намерения и методы для **Greeting** (Приветствие), **Cancel** (Отмена) и **Help** (Справка). Эти намерения не используются в предварительно созданной предметной области HomeAutomation. Оставьте атрибут и метод для намерения **None**. 

2. Добавьте зависимости в верхнюю часть файла с другими зависимостями:

   [!code-csharp[Dependencies](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=4-5&dedent=8 "dependencies")]

3. В верхнюю часть класса `BasicLuisDialog ` добавьте константы для управления строками:

   [!code-csharp[Add Intent and Entity Constants](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=23-32&dedent=8 "Add Intent and Entity Constants")]

4. Добавьте код для новых намерений `HomeAutomation.TurnOn` и `HomeAutomation.TurnOff` внутри класса `BasicLuisDialog `:

   [!code-csharp[Add Intents](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=61-71&dedent=8 "Add Intents")]

5. Добавьте код для получения всех сущностей, обнаруженных LUIS внутри `BasicLuisDialog ` класса:

   [!code-csharp[Collect entities](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=34-53&dedent=8 "Collect entities")]

6. Измените метод **ShowLuisResult** в классе `BasicLuisDialog ` для округления оценок, сбора сущностей и отображения сообщения ответа в чат-боте:

   [!code-csharp[Display message in chatbot](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs?range=73-83&dedent=8 "Display message in chatbot")]

## <a name="build-the-bot"></a>Сборка бота
В редакторе кода щелкните правой кнопкой мыши `build.cmd` и выберите пункт **Run from Console** (Запустить из консоли).

![Сборка веб-бота ](./media/luis-tutorial-cscharp-web-bot/bot-service-build-run-from-console.png)

Представление кода заменяется окном терминала, в котором отображаются ход и результаты сборки.

![Успешная сборка веб-бота](./media/luis-tutorial-cscharp-web-bot/bot-service-build-success.png)

> [!TIP]
> Альтернативным способом сборки бота является выделение имени бота на верхней синей панели и выбор команды **Open Kudu Console** (Открыть консоль Kudu). Консоль откроется в каталог **D:\home**. 
> 
> Измените каталог на **D:\home\site\wwwroot**, введя: `cd site\wwwroot`
>
> Запустите скрипт сборки, введя: `build.cmd`

## <a name="test-the-bot"></a>Тестирование бота

На портале Azure щелкните **Test in Web Chat** (Тестировать в веб-чате), чтобы протестировать бот. Введите сообщения, такие как "Turn on the lights" (Включить свет) "turn off my heater" (Выключить нагреватель), чтобы вызвать добавленные намерения.

   ![Тестирование бота HomeAutomation веб-чате](./media/luis-tutorial-cscharp-web-bot/bot-service-chat-results.png)

> [!TIP]
> Вы можете повторно обучить приложение LUIS, не изменяя код бота. См. разделы [Добавление примеров высказываний](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) и [Обучение и тестирование приложений LUIS](https://docs.microsoft.com/azure/cognitive-services/LUIS/luis-interactive-test). 

## <a name="download-the-bot-to-debug"></a>Скачивание бота для отладки
Если бот не работает, скачайте проект на локальный компьютер и продолжите [отладку](https://docs.microsoft.com/bot-framework/bot-service-debug-bot#debug-a-c-bot). 

## <a name="learn-more-about-bot-framework"></a>Дополнительные сведения о платформе Bot Framework
Ознакомьтесь с дополнительными сведениями о платформе [Bot Framework](https://dev.botframework.com/) и пакетах SDK версии [3.x](https://github.com/Microsoft/BotBuilder) и [4.x](https://github.com/Microsoft/botbuilder-dotnet).

## <a name="next-steps"></a>Дополнительная информация

Добавьте намерения LUIS и диалоговые окна службы Bot для обработки намерений **Help** (Справка), **Cancel** (Отмена) и **Greeting** (Приветствие). Обучите, опубликуйте и выполните [сборку](#build-the-bot) бота веб-приложения. LUIS и бот должны иметь одинаковые намерения.

> [!div class="nextstepaction"]
> [Добавление намерений](./luis-how-to-add-intents.md)
> [Подготовка речи](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


<!-- Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-cs-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/csharp
[Github-LUIS-Samples-cs-hotel-bot-readme]: https://github.com/Microsoft/LUIS-Samples/blob/master/bot-integration-samples/hotel-finder/csharp/README.md
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[VisualStudio]: https://www.visualstudio.com/

<!-- tested on Win10 -->
