---
title: Получение данных Application Insights из LUIS с использованием C#
titleSuffix: Azure Cognitive Services
description: Создание бота, интегрированного с приложением LUIS и Application Insights, с помощью Node.js.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 342c4f98d854d94426c3e4a1eb79bb2a6adffaad
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038006"
---
# <a name="add-luis-results-to-application-insights"></a>Добавление результатов распознавание речи с помощью LUIS в Application Insights
В этом руководстве приведена процедура добавления данных запроса и ответа LUIS в хранилище данных телеметрии [Application Insights](https://azure.microsoft.com/services/application-insights/). После получения этих данных можно включить их запрос (используя язык Kusto или PowerBi) для анализа, статистической обработки и создания отчетов по намерениям и сущностям фрагментов речи в режиме реального времени. Этот анализ помогает определить, следует ли добавлять или изменять намерения и сущности приложения LUIS.

Бот создается с помощью Bot Framework 3.x и бота веб-приложения Azure.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
* добавление Application Insights в бот веб-приложения;
* запись и отправка результатов запроса LUIS в Application Insights;
* отправка запроса в Application Insights для получения основных намерений, оценки и фрагментов речи.

## <a name="prerequisites"></a>Предварительные требования

* Бот веб-приложения LUIS из **[предыдущего руководства](luis-nodejs-tutorial-build-bot-framework-sample.md)** с включенным компонентом Application Insights. 

> [!Tip]
> Если у вас еще нет подписки, вы можете зарегистрироваться для получения [бесплатной учетной записи](https://azure.microsoft.com/free/).

Весь код из этого руководства можно найти в [репозитории Github LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs). Все строки, связанные с этим руководством, закомментированы с помощью `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Бот веб-приложения с LUIS
Предполагается, что у вас есть код, который выглядит следующим образом, или вы выполнили процедуру в [другом руководстве](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Добавление Application Insights в бот веб-приложения
В настоящее время служба Application Insights использует этот бот веб-приложения и собирает общие данные телеметрии состояния для бота. Он не собирает информацию о запросах и ответах LUIS, которые необходимо проверить и по которым нужно исправить намерения и сущности. 

Чтобы бот веб-приложения мог записывать запросы и ответы LUIS, пакет **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM должен быть установлен и настроен для файла **app.js**. Затем обработчики диалогового окна намерения должны отправить информацию запроса и ответа LUIS в Application Insights. 

1. На портале Azure в службе ботов веб-приложения выберите **Сборка** в разделе **Управление ботом**. 

    ![Поиск Application Insights](./media/luis-tutorial-appinsights/build.png)

2. Откроется новая вкладка браузера с редактором службы приложений. Выберите имя приложения на верхней панели, а затем **Открытие консоли Kudu**. 

    ![Поиск Application Insights](./media/luis-tutorial-appinsights/kudu-console.png)

3. В консоли введите следующую команду, чтобы установить Application Insights и пакеты Underscore:

    ```
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Поиск Application Insights](./media/luis-tutorial-appinsights/npm-install.png)

    Подождите, пока пакеты будут установлены:

    ```
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Вы закончили работу с вкладкой браузера консоли Kudu.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Запись и отправка результатов запроса LUIS в Application Insights
1. На вкладке браузера редактора службы приложений откройте файл **app.js**.

2. Добавьте следующие библиотеки NPM в существующие строки `requires`:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Создайте объект Application Insights и используйте параметр бота веб-приложения **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Добавьте функцию **appInsightsLog**:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    Последняя строка функции — это то, где данные добавляются в Application Insights. Имя события — **LUIS-results**, уникальное имя, получаемое отдельно от всех остальных данных телеметрии, собираемых этим ботом веб-приложения. 

5. Используйте функцию **appInsightsLog**. Ее следует добавить в каждое диалоговое окно намерения:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Для проверки бота веб-приложения используйте функцию **тестирования в веб-чате**. Вы не должны видеть разницу, потому что все операции находятся в Application Insights, а не в ответах бота.

## <a name="view-luis-entries-in-application-insights"></a>Просмотр записей LUIS в Application Insights
Откройте Application Insights, чтобы просмотреть записи LUIS. 

1. На портале выберите **Все ресурсы**, выполните фильтрацию по имени бота веб-приложения. Щелкните ресурс с типом **Application Insights**. Значок для Application Insights — лампочка. 

    ![Поиск Application Insights](./media/luis-tutorial-appinsights/search-for-app-insights.png)



2. Когда ресурс откроется, щелкните значок **поиска** (лупы) на крайней правой панели. Откроется новая панель справа. В зависимости от объема обнаруженных данных телеметрии вывод панели может занять некоторое время. Найдите `LUIS-results` и нажмите клавишу ВВОД на клавиатуре. Список будет уточнен для включения только тех результатов запросов LUIS, которые были добавлены в этом руководстве.

    ![Фильтр зависимостей](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Выберите верхнюю запись. Откроется новое окно с более подробными данными, включая пользовательские данные для запроса LUIS в правом углу. Данные содержат основное намерение и его оценку.

    ![Сведения о зависимости](./media/luis-tutorial-appinsights/app-insights-detail.png)

    Когда вы закончите, нажмите **X** в верхнем правом углу для возврата к списку элементов зависимостей. 


> [!Tip]
> Если вы хотите сохранить список зависимостей и вернуться к нему позже, щелкните **...Дополнительно** и выберите **Сохранить избранное**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Запрос Application Insights для получения намерения, оценки и фрагмента речи
Application Insights позволяет выполнять запросы данных с помощью языка [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics), а также экспортировать их в [PowerBI](https://powerbi.microsoft.com). 

1. Щелкните **Аналитика** в верхней части списка зависимостей над полем фильтра. 

    ![Кнопка аналитики](./media/luis-tutorial-appinsights/analytics-button.png)

2. Откроется новое окно с окном запроса вверху и окном таблицы данных внизу. Если вы использовали базы данных, то такая организация будет вам знакома. Запрос содержит все элементы за последние 24 часа, начиная с имени `LUIS-results`. В столбце **CustomDimensions** содержатся результаты запросов LUIS в виде пар "имя-значение".

    ![Окно запроса Analytics](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Чтобы получить основное намерение, оценку и фрагмент речи, добавьте следующий код над последней строкой в окне запроса:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Выполните запрос. Прокрутите таблицу данных вправо. Вы увидите новые столбцы для основного намерения, оценки и фрагмента речи. Щелкните столбец topIntent для сортировки.

    ![Намерение Analytics top](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Дополнительные сведения о [языке запросов Kusto](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) или [экспорте данных в PowerBI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Дополнительная информация

Другие данные, которые может потребоваться добавить в данные Application Insights, включают идентификатор приложения, идентификатор версии, время последнего изменения модели, дату последнего обучения, дату последней публикации. Эти значения можно получить из URL-адреса конечной точки (идентификатор приложения и идентификатор версии) или из вызова [API разработки](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d), задания в параметрах бота веб-приложения и получения из этих параметров.  

При использовании одной подписки конечной точки для нескольких приложений LUIS вам также потребуется включить идентификатор подписки и свойство, сообщающее о том, что это общий ключ. 

> [!div class="nextstepaction"]
> [Подробнее о примерах фрагментов речи](luis-how-to-add-example-utterances.md)
