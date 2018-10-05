---
title: LUIS и QnA Maker — интеграция с ботом
titleSuffix: Azure Cognitive Services
description: Пошаговое руководство по интеграции QnA Maker и LUIS с ботом.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 53e46fa84bcd7b96403dcb0ec70b45b800bc4acb
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042012"
---
# <a name="integrate-qna-maker-and-luis-to-distribute-your-knowledge-base"></a>Интеграция QnA Maker и API распознавания речи для распределения базы знаний
По мере роста базы знаний QnA Maker становится трудно поддерживать ее как единый монолитный набор, поэтому базу знаний необходимо разделить на маленькие логические блоки.

Хотя в QnA Maker просто создать несколько баз знаний, понадобится элемент логики для направления входящего вопроса в соответствующую базу знаний. Это можно сделать с помощью API распознавания речи.

## <a name="architecture"></a>Архитектура

![Архитектура API распознавания речи службы QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-luis-architecture.PNG)

В вышеприведенном сценарии показано, что из модели API распознавания речи (LUIS) сначала QnA Maker получает намерение входящего вопроса, а затем использует его, чтобы направить в правильную базу знаний QnA Maker.

## <a name="prerequisites"></a>Предварительные требования
- Войдите на портал[LUIS](https://www.luis.ai/) и [создайте приложение](https://docs.microsoft.com/azure/cognitive-services/luis/create-new-app).
- [Добавьте намерения](https://docs.microsoft.com/azure/cognitive-services/luis/add-intents) в сценарий.
- [Обучите](https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-train) и [опубликуйте](https://docs.microsoft.com/azure/cognitive-services/luis/publishapp) ваше приложение API распознавания речи.
- Войдите в [QnA Maker](https://qnamaker.ai) и [создайте базы знаний](https://www.qnamaker.ai/Create) согласно сценарию.
- Проверьте и опубликуйте базы знаний.

## <a name="qna-maker--luis-bot"></a>QnA Maker и бот API распознавания речи
1. Сначала создайте бот веб-приложения с помощью шаблона API распознавания речи, свяжите его с созданным ранее приложением API распознавания речи и измените намерения. Подробные шаги описаны [здесь](https://docs.microsoft.com/azure/cognitive-services/luis/luis-csharp-tutorial-build-bot-framework-sample).

2. Добавьте зависимости в верхнюю часть файла с другими зависимостями.

    ```
    using RestSharp;
    using System.Collections.Generic;
    using Newtonsoft.Json;
    ```
3. Добавьте нижний класс для вызова службы QnA Maker.

    ```
        /// <summary>
        /// QnAMakerService is a wrapper over the QnA Maker REST endpoint
        /// </summary>
        [Serializable]
        public class QnAMakerService
        {
            private string qnaServiceHostName;
            private string knowledgeBaseId;
            private string endpointKey;
    
            /// <summary>
            /// Initialize a particular endpoint with it's details
            /// </summary>
            /// <param name="hostName">Hostname of the endpoint</param>
            /// <param name="kbId">Knowledge base ID</param>
            /// <param name="ek">Endpoint Key</param>
            public QnAMakerService(string hostName, string kbId, string ek)
            {
                qnaServiceHostName = hostName;
                knowledgeBaseId = kbId;
                endpointKey = ek;
            }
    
            /// <summary>
            /// Call the QnA Maker endpoint and get a response
            /// </summary>
            /// <param name="query">User question</param>
            /// <returns></returns>
            public string GetAnswer(string query)
            {
                var client = new RestClient( qnaServiceHostName + "/qnamaker/knowledgebases/" + knowledgeBaseId + "/generateAnswer");
                var request = new RestRequest(Method.POST);
                request.AddHeader("authorization", "EndpointKey " + endpointKey);
                request.AddHeader("content-type", "application/json");
                request.AddParameter("application/json", "{\"question\": \"" + query + "\"}", ParameterType.RequestBody);
                IRestResponse response = client.Execute(request);
    
                // Deserialize the response JSON
                QnAAnswer answer = JsonConvert.DeserializeObject<QnAAnswer>(response.Content);
    
                // Return the answer if present
                if (answer.answers.Count > 0)
                    return answer.answers[0].answer;
                else
                    return "No good match found.";
            }
        }
    
        /* START - QnA Maker Response Class */
        public class Metadata
        {
            public string name { get; set; }
            public string value { get; set; }
        }
    
        public class Answer
        {
            public IList<string> questions { get; set; }
            public string answer { get; set; }
            public double score { get; set; }
            public int id { get; set; }
            public string source { get; set; }
            public IList<object> keywords { get; set; }
            public IList<Metadata> metadata { get; set; }
        }
    
        public class QnAAnswer
        {
            public IList<Answer> answers { get; set; }
        }
        /* END - QnA Maker Response Class */
    ```

3. Перейдите в https://qnamaker.ai -> My Knowledge Bases (Мои базы знаний) -> "Представление кода" соответствующей базы знаний. Получите следующие сведения.

    ![Запрос HTTP QnA Maker](../media/qnamaker-tutorials-qna-luis/qnamaker-http-request.png)

4. Создайте экземпляр класса QnAMakerService для каждой из баз знаний.
    ```
            // Instantiate the knowledge bases
            public QnAMakerService hrQnAService = new QnAMakerService("https://hrkb.azurewebsites.net", "<HR knowledge base id>", "<HR endpoint key>");
            public QnAMakerService payrollQnAService = new QnAMakerService("https://payrollkb.azurewebsites.net", "<Payroll knowledge base id>", "<Payroll endpoint key>");
            public QnAMakerService financeQnAService = new QnAMakerService("https://financekb.azurewebsites.net", "<Finance knowledge base id>", "<Finance endpoint key>");
    ```

5. Вызовите соответствующую базу знаний для намерения.
    ```
            // HR Intent
            [LuisIntent("HR")]
            public async Task CancelIntent(IDialogContext context, LuisResult result)
            {
                // Ask the HR knowledge base
                await context.PostAsync(hrQnAService.GetAnswer(result.Query));
            }
    
            // Payroll intent
            [LuisIntent("Payroll")]
            public async Task GreetingIntent(IDialogContext context, LuisResult result)
            {
                // Ask the payroll knowledge base
                await context.PostAsync(payrollQnAService.GetAnswer(result.Query));
            }
    
            // Finance intent
            [LuisIntent("Finance")]
            public async Task HelpIntent(IDialogContext context, LuisResult result)
            {
                // Ask the finance knowledge base
                await context.PostAsync(financeQnAService.GetAnswer(result.Query));
            }
    ```

## <a name="build-the-bot"></a>Сборка бота
1. В редакторе кода щелкните правой кнопкой мыши `build.cmd` и выберите пункт **Run from Console** (Запустить из консоли).

    ![запуск из консоли](../media/qnamaker-tutorials-qna-luis/run-from-console.png)

2. Представление кода заменяется окном терминала, в котором отображаются ход и результаты сборки.

    ![сборка консоли](../media/qnamaker-tutorials-qna-luis/console-build.png)

## <a name="test-the-bot"></a>Тестирование бота
На портале Azure выберите **Test in Web Chat** (Тестировать в веб-чате), чтобы протестировать бот. Введите сообщения с разными намерениями, чтобы получить ответ соответствующей базы знаний.

![тест веб-чата](../media/qnamaker-tutorials-qna-luis/qnamaker-web-chat.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Create a business continuity plan for your QnA Maker service](../How-To/business-continuity-plan.md) (Создание плана по обеспечению непрерывности бизнеса для службы QnA Maker)
