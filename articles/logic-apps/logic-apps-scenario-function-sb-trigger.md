---
title: 'Сценарий: запуск приложений логики с помощью Функций Azure и служебной шины Azure | Документация Майкрософт'
description: Создайте функцию, которая активирует приложение логики с помощью службы "Функции Azure" и Служебной шины Azure.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 8d6f2ecaec7bf7ae7e4415ccd60fc6ec3ff487f3
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126178"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Сценарий: запуск приложения логики с помощью службы "Функции Azure" и Служебной шины Azure

Можно использовать функции Azure для создания триггера приложения логики, когда требуется развернуть долго выполняющиеся прослушиватели или задачи. Например, можно создать функцию, которая ожидает передачи данных из очереди и немедленно запускает извещающим триггером приложение логики.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, <a href="https://azure.microsoft.com/free/" target="_blank">зарегистрируйтесь для получения бесплатной учетной записи Azure</a>. 

* Базовые знания [создания приложений логики](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Перед созданием функции Azure [создайте приложение-функцию](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Создание приложения логики

В этом примере для каждого приложения логики, которое нужно запускать, создается отдельная функция. Сначала создайте приложение логики с триггером HTTP-запроса. Функция Azure вызывает эту конечную точку при получении каждого сообщения из очереди.  

1. Войдите на [портал Azure](https://portal.azure.com) и создайте пустое приложение логики. 

   Если вы пока не знакомы с приложениями логики, ознакомьтесь со статьей [Краткое руководство. Создание первого автоматизированного рабочего процесса с помощью Azure Logic Apps на портале Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. В поле поиска введите фильтр "HTTP-запрос". В списке триггеров выберите триггер **При получении HTTP-запроса**.

   ![Выбор триггера](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. Для триггера **Запрос** вы можете при необходимости ввести схему JSON для использования с сообщением очереди. Схемы JSON помогают конструктору приложений логики понять структуру входных данных и упрощают выбор выходов в рабочем процессе. 

   Чтобы указать схему, введите ее в поле **Схема JSON текста запроса**, например: 

   ![Указание схемы JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Если у вас нет схемы, но у вас есть пример полезных данных в формате JSON, можно создать схему на основе полезных данных.

   1. В триггере запросов выберите **Использовать пример полезной нагрузки, чтобы создать схему**.

   1. В разделе **Введение или вставка примера полезных данных JSON** введите свой пример полезных данных, а затем выберите **Готово**.
      
      ![Ввод примера полезных данных](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Этот пример полезных данных создает схему, которая появляется в триггере:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Добавьте любые другие действия, которые необходимо выполнить после получения сообщения очереди. 

   Например, можно отправить сообщение электронной почты с соединителем Office 365 Outlook.

1. Сохраните свое приложение логики, которое создает URL-адрес обратного вызова для триггера в этом приложении логики. Этот URL-адрес отображается в свойстве **URL-адрес HTTP POST**.

   ![Созданный URL-адрес обратного вызова для триггера](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Создание функции Azure

Далее создайте функцию, которая выступает в качестве триггера и ожидает передачи данных из очереди. 

1. На портале Azure откройте и разверните приложение-функцию, если оно еще не открыто. 

1. В разделе с именем приложения-функции разверните **Функции**. На панели **Функции** выберите **Новая функция**. Выберите этот шаблон: **Service Bus Queue trigger — C#** (Триггер очереди служебной шины — C#).
   
   ![Выбор на портале службы "Функции Azure"](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Предоставьте имя триггеру, а затем настройте подключение к очереди служебной шины, где используется прослушиватель `OnMessageReceive()` из пакета SDK для служебной шины Azure.

1. Напишите простую функцию для вызова ранее созданной конечной точки приложения логики, используя сообщение очереди в качестве триггера, например: 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

   В примере используется сообщение с типом содержимого `application/json`. При необходимости этот тип можно изменить.

1. Чтобы протестировать функцию, добавьте сообщение очереди с помощью такого средства, как [Обозреватель служебной шины](https://github.com/paolosalvatori/ServiceBusExplorer). 

   Приложение логики должно активироваться сразу же, как только функция получит это сообщение.

## <a name="get-support"></a>Получение поддержки

* Если у вас возникли вопросы, то посетите [форум Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Отправить идею по поводу возможности или проголосовать за нее вы можете на [сайте отзывов пользователей Logic Apps](http://aka.ms/logicapps-wish).

