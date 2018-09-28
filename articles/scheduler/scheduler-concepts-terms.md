---
title: Основные понятия, терминология и иерархия сущностей планировщика Azure | Документы Майкрософт
description: Основные понятия, терминология и иерархия сущностей планировщика Azure, включая задания и коллекции заданий
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 3ef16fab-d18a-48ba-8e56-3f3e0a1bcb92
ms.topic: get-started-article
ms.date: 08/18/2016
ms.openlocfilehash: 07b7cce4b026464ba34296b54c4ae90d6d2b1afa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46981167"
---
# <a name="concepts-terminology-and-entities-in-azure-scheduler"></a>Основные понятия, терминология и сущности планировщика Azure

> [!IMPORTANT]
> Служба [Azure Logic Apps](../logic-apps/logic-apps-overview.md) заменяет планировщик Azure, который выводится из эксплуатации. Для планирования заданий [попробуйте использовать Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="entity-hierarchy"></a>Иерархия сущностей

REST API планировщика Azure предоставляет и использует следующие основные сущности, или ресурсы:

| Сущность | ОПИСАНИЕ |
|--------|-------------|
| **Задание.** | Определяет одно повторяющееся действие с простой или сложной стратегией выполнения. К действиям относятся HTTP-запросы, запросы к очереди хранилища, запросы к очереди служебной шины и запросы к разделу служебной шины. | 
| **Коллекция заданий** | Содержит группу заданий, а также параметры, квоты и регулирования, которые являются общими для заданий в этой коллекции. Как владелец подписки Azure, вы можете создавать коллекции заданий и групповые задания на основании использования ими границ приложений. Коллекция заданий имеет следующие особенности: <p>— ограничивается одним регионом; <br>— позволяет вводить квоты, поэтому вы можете ограничить использование для всех заданий в коллекции; <br>— среди квот есть MaxJobs и MaxRecurrence. | 
| **Журнал заданий** | Описывает сведения о выполнении задания, например состояние и все данные ответов. |
||| 

## <a name="entity-management"></a>Управление сущностями

На высоком уровне REST API планировщика предоставляет эти операции для управления сущностями.

### <a name="job-management"></a>Управление заданиями

Поддерживает операции для создания и изменения заданий. Все задания должны входить в уже существующую коллекцию, поэтому неявное создание коллекций не предусмотрено. Дополнительные сведения см. в разделе [REST API планировщика — задания](https://docs.microsoft.com/rest/api/scheduler/jobs). Вот URI-адрес для этих операций:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`

### <a name="job-collection-management"></a>Управление коллекциями заданий

Поддерживает операции создания и изменения заданий и коллекций заданий, которые сопоставляются с квотами и общими настройками. В качестве примеров квот можно привести максимальное число заданий и наименьший интервал повторения. Дополнительные сведения см. в разделе [REST API планировщика — коллекции заданий](https://docs.microsoft.com/rest/api/scheduler/jobcollections). Вот URI-адрес для этих операций:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`

### <a name="job-history-management"></a>Управление журналами заданий

Поддержка операции GET для извлечения журнала заданий за последние 60 дней, например с указанием времени и результатов выполнения задания. Включает поддержку строкового параметра запроса для фильтрации результатов по состоянию и статусу. Дополнительные сведения см. в разделе [REST API планировщика — журнал выполнения заданий](https://docs.microsoft.com/rest/api/scheduler/jobs/listjobhistory). Вот URI-адрес для этой операции:

`https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`

## <a name="job-types"></a>Типы заданий

Планировщик Azure поддерживает несколько типов заданий: 

* Задания HTTP, включая задания HTTPS, поддерживающие протокол SSL, когда у вас есть конечная точка для существующей службы или рабочей нагрузки.
* Задания очереди хранилища для рабочих нагрузок, использующих очереди хранилища, например публикации сообщений в очередях хранилища.
* Задания очереди служебной шины для рабочих нагрузок, использующих очереди служебной шины.
* Задания разделов служебной шины для рабочих нагрузок, использующих разделы служебной шины.

## <a name="job-definition"></a>определение задания;

На высоком уровне задание планировщика состоит из следующих основных частей:

* Действие, выполняемое при срабатывании таймера задания.
* Время выполнения задания (необязательно).
* Время и периодичность повторного выполнения (необязательно).
* Действие, выполняемое в случае сбоя первичного действия (необязательно).

Задание также включает в себя системные данные, такие как время следующего запланированного запуска задания. Определение кода задания — это объект в формате JavaScript Object Notation (JSON), который содержит следующие элементы:

| Элемент | Обязательно | ОПИСАНИЕ | 
|---------|----------|-------------| 
| [**startTime**](#start-time) | Нет  | Время начала задания со смещением часового пояса в [формате ISO 8601](http://en.wikipedia.org/wiki/ISO_8601). | 
| [**action**](#action) | Yes | Подробные сведения об основном действии, которые могут включать объект **errorAction**. | 
| [**errorAction**](#error-action) | Нет  | Подробные сведения о дополнительном действии, которое выполняется при сбое основного действия. |
| [**recurrence**](#recurrence) | Нет  | Такие сведения, как частота и интервал, для повторяющегося задания. | 
| [**retryPolicy**](#retry-policy) | Нет  | Сведения о том, как часто повторять действие. | 
| [**state**](#state) | Yes | Сведения о текущем состоянии задания. |
| [**status**](#status) | Yes | Сведения о текущем состоянии задания, которым управляет служба. |
||||

Ниже приведен пример, показывающий комплексное определение задания для действия HTTP; более полные сведения об элементе приведены в следующих разделах: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z",
   "action": {
      "type": "Http",
      "request": {
         "uri": "http://contoso.com/some-method", 
         "method": "PUT",          
         "body": "Posting from a timer",
         "headers": {
            "Content-Type": "application/json"
         },
         "retryPolicy": { 
             "retryType": "None" 
         },
      },
      "errorAction": {
         "type": "Http",
         "request": {
            "uri": "http://contoso.com/notifyError",
            "method": "POST"
         }
      }
   },
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]
      },
      "count": 10,
      "endTime": "2012-11-04"
   },
   "state": "Disabled",
   "status": {
      "lastExecutionTime": "2007-03-01T13:00:00Z",
      "nextExecutionTime": "2007-03-01T14:00:00Z ",
      "executionCount": 3,
      "failureCount": 0,
      "faultedCount": 0
   }
}
```

<a name="start-time"></a>

## <a name="starttime"></a>startTime

В объекте **startTime** можно указать время начала и смещение часового пояса в [формате ISO 8601](http://en.wikipedia.org/wiki/ISO_8601).

<a name="action"></a>

## <a name="action"></a>action

Задание планировщика выполняет основное действие (**action**) на основе заданного расписания. Планировщик поддерживает действия, связанные с HTTP, очередью хранилища, разделами служебной шины и очередями служебной шины. Если основное действие (**action**) завершается ошибкой, планировщик может запустить дополнительное действие [**errorAction**](#errorAction), которое обрабатывает ошибку. Объект **action** описывает следующие элементы:

* Тип службы действия.
* Сведения о действии.
* Альтернативное действие: **errorAction**.

Предыдущий пример описывает действие HTTP. Ниже приведен пример с действием для очереди хранилища.

```json
"action": {
   "type": "storageQueue",
   "queueMessage": {
      "storageAccount": "myStorageAccount",  
      "queueName": "myqueue",                
      "sasToken": "TOKEN",                   
      "message": "My message body"
    }
}
```

Ниже приведен пример с действием для очереди служебной шины.

```json
"action": {
   "type": "serviceBusQueue",
   "serviceBusQueueMessage": {
      "queueName": "q1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {  
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",  
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Ниже приведен пример с действием для раздела служебной шины.

```json
"action": {
   "type": "serviceBusTopic",
   "serviceBusTopicMessage": {
      "topicPath": "t1",  
      "namespace": "mySBNamespace",
      "transportType": "netMessaging", // Either netMessaging or AMQP
      "authentication": {
         "sasKeyName": "QPolicy",
         "type": "sharedAccessKey"
      },
      "message": "Some message",
      "brokeredMessageProperties": {},
      "customMessageProperties": {
         "appname": "FromScheduler"
      }
   }
},
```

Дополнительные сведения о подписанных URL-адресах см. в статье [Авторизация на основе подписанных URL-адресов (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

<a name="error-action"></a>

## <a name="erroraction"></a>errorAction

Если основное действие (**action**) задания завершается ошибкой, планировщик может запустить дополнительное действие **errorAction**, которое обрабатывает ошибку. В основном действии (**action**) можно указать объект **errorAction**, чтобы планировщик мог вызвать конечную точку обработки ошибок или отправить пользователю уведомление. 

Например, в случае сбоя в основной конечной точке можно использовать **errorAction** для вызова вторичной конечной точки или уведомления конечной точки обработки ошибок. 

Аналогично основному действию (**action**), действие в случае ошибки может содержать как простой, так и сложный алгоритм в зависимости от других действий. 

<a name="recurrence"></a>

## <a name="recurrence"></a>recurrence

Задание повторяется, если определение задания в формате JSON включает объект **recurrence**, например:

```json
"recurrence": {
   "frequency": "Week",
   "interval": 1,
   "schedule": {
      "hours": [10, 22],
      "minutes": [0, 30],
      "weekDays": ["Monday", "Wednesday", "Friday"]
   },
   "count": 10,
   "endTime": "2012-11-04"
},
```

| Свойство | Обязательно | Значение | ОПИСАНИЕ | 
|----------|----------|-------|-------------| 
| **frequency** | Да, если используется **recurrence** | "Minute", "Hour", "Day", "Week", "Month", "Year". | Единица измерения времени между выполнениями. | 
| **interval** | Нет  | От 1 до 1000 включительно. | Положительное целое число, которое определяет количество единиц времени между каждым повторением на основании значения **frequency**. | 
| **schedule** | Нет  | Varies | Сведения о более сложных и расширенных расписаниях. См. параметры **hours**, **minutes**, **weekDays**, **months** и **monthDays**. | 
| **hours** | Нет  | От 1 до 24. | Массив, содержащий часы, который задает время запуска задания. | 
| **minutes** | Нет  | От 1 до 60. | Массив, содержащий минуты, который задает время запуска задания. | 
| **months** | Нет  | От 1 до 12. | Массив, содержащий месяцы, который задает время запуска задания. | 
| **monthDays** | Нет  | Varies | Массив, содержащий дни месяца, который задает время запуска задания. | 
| **weekDays** | Нет  | "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday". | Массив, содержащий дни недели, который задает время запуска задания. | 
| **count** | Нет  | <*none*> | Количество повторений. По умолчанию используется бесконечное повторение. Невозможно одновременно использовать **count** и **endTime**; применяется правило, по которому задание завершается раньше. | 
| **endTime** | Нет  | <*none*> | Дата и время, когда нужно прекратить повторение. По умолчанию используется бесконечное повторение. Невозможно одновременно использовать **count** и **endTime**; применяется правило, по которому задание завершается раньше. | 
||||

Дополнительные сведения об этих элементах см. в разделе [Создание сложных расписаний и расширенных схем повторения](../scheduler/scheduler-advanced-complexity.md).

<a name="retry-policy"></a>

## <a name="retrypolicy"></a>retryPolicy

Если задание планировщика может завершиться ошибкой, вы можете задать политику повтора, которая определяет необходимость и способ повторения действия планировщиком. Планировщик по умолчанию выполняет еще четыре попытки запустить задание с интервалом 30 секунд. Политику можно сделать более или менее агрессивной; например, эта политика повторяет действие два раза в день:

```json
"retryPolicy": { 
   "retryType": "Fixed",
   "retryInterval": "PT1D",
   "retryCount": 2
},
```

| Свойство | Обязательно | Значение | ОПИСАНИЕ | 
|----------|----------|-------|-------------| 
| **retryType** | Yes | **Fixed**, **None**. | Определяет, задана ли политика повтора (**Fixed**) или нет (**None**). | 
| **retryInterval** | Нет  | PT30S | Указывает интервал и частоту между повторными попытками в [формате ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Минимальное значение — 15 секунд, максимальное значение — 18 месяцев. | 
| **retryCount** | Нет  | 4. | Задает количество повторных попыток. Максимальное значение — 20. | 
||||

Дополнительные сведения см. в статье [Высокая доступность и надежность](../scheduler/scheduler-high-availability-reliability.md).

<a name="status"></a>

## <a name="state"></a>state

Задание может иметь состояние **Enabled** (включено), **Disabled** (отключено), **Completed** (выполнено) или **Faulted** (сбой), например: 

`"state": "Disabled"`

Для перевода задания в состояние **Enabled** или **Disabled** можно использовать операцию PUT или PATCH.
Если же задание находится в состоянии **Completed** или **Faulted**, изменить состояние невозможно, хотя можно выполнить операцию DELETE для задания. Завершенные и сбойные задания удаляются через 60 дней. 

<a name="status"></a>

## <a name="status"></a>status

После запуска задания планировщик возвращает сведения о состоянии задания через объект **status**, которым управляет только планировщик. Тем не менее объект **status** находится внутри объекта **job**. Ниже приведена информация, которую включает в себя состояние задания:

* Время предыдущего выполнения, если применимо.
* Время следующего запланированного выполнения для выполняемых заданий.
* Число выполнений задания.
* Количество ошибок.
* Количество сбоев.

Например: 

```json
"status": {
   "lastExecutionTime": "2007-03-01T13:00:00Z",
   "nextExecutionTime": "2007-03-01T14:00:00Z ",
   "executionCount": 3,
   "failureCount": 0,
   "faultedCount": 0
}
```

## <a name="see-also"></a>См. также

* [Что такое планировщик Azure?](scheduler-intro.md)
* [Основные понятия, терминология и иерархия сущностей](scheduler-concepts-terms.md)
* [Создание сложных расписаний и расширенных схем повторения](scheduler-advanced-complexity.md)
* [Лимиты, квоты, значения по умолчанию и коды ошибок](scheduler-limits-defaults-errors.md)
* [Справочник по API REST планировщика Azure](https://docs.microsoft.com/rest/api/schedule)
* [Справочник по командлетам PowerShell планировщика Azure](scheduler-powershell-reference.md)
