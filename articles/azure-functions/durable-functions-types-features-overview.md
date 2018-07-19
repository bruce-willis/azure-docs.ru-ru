---
title: Общие сведения о типах и возможностях функций в устойчивых функциях в Azure
description: Ознакомьтесь с типами функций и ролями, обеспечивающими взаимодействие между функциями в процессе оркестрации устойчивых функций.
services: functions
author: jeffhollan
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/04/2018
ms.author: azfuncdf
ms.openlocfilehash: dfcf52c6d8d5c04e15d653376f52107fc4776d02
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38973156"
---
# <a name="overview-of-function-types-and-features-for-durable-functions-azure-functions"></a>Общие сведения о типах и возможностях функций в устойчивых функциях в Azure (Функции Azure)

Устойчивые функции Azure предоставляют возможность оркестрации выполнения функции с отслеживанием состояния.  Устойчивая функция — это решение, сформированное из различных функций Azure.  В процессе оркестрации каждая из этих функций может выполнять разные роли.  В следующем документе содержится обзор типов функций, применяемых в оркестрации устойчивых функций.  Здесь также приводятся некоторые распространенные схемы взаимодействия функций.  

![Типы устойчивых функций][1]  

## <a name="types-of-functions"></a>Типы функций

### <a name="activity-functions"></a>Функции действий

Функции действий являются базовой единицей работы в устойчивой оркестрации.  Функции действий — это функции и задачи, управляемые в процессе.  Например, можно создать устойчивую функцию для обработки заказа, которая проверяет запасы, получает оплату от клиента и организует доставку.  Каждая из этих задач будет представлять функцию действия.  У функций действий нет ограничений по типу выполняемых работ.  Они могут быть написаны на любом языке, поддерживаемом функциями Azure.  Платформа устойчивых задач гарантирует, что каждая вызываемая функция действия будет выполнена во время оркестрации по меньшей мере один раз.

Функция действия должна активироваться с помощью [триггера действия](durable-functions-bindings.md#activity-triggers).  В качестве параметра эта функция получит [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html). Триггер можно также привязать к любому другому объекту для передачи входных данных в функцию.  Функция действия может возвращать значения в оркестратор.  При отправке большого количества значений в функцию действия или при возвращении из нее больших объемов данных можно [использовать кортежи и массивы](durable-functions-bindings.md#passing-multiple-parameters).  Функции действий запускаются только из экземпляра оркестрации.  Несмотря на то, что у функции действия и другой функции (например, функции, активируемой по HTTP) могут быть общие фрагменты кода, для каждой функции поддерживается только один триггер.

Дополнительные сведения и примеры можно найти в [статье о привязке устойчивых функций](durable-functions-bindings.md#activity-triggers).

### <a name="orchestrator-functions"></a>Функции оркестратора

Функции оркестратора являются ядром устойчивой функции.  Они описывают способ и порядок выполнения действий.  Функции оркестратора описывают оркестрацию в коде (C# или JavaScript), как показано в статье с [обзором устойчивых функций](durable-functions-overview.md).  Оркестрация может поддерживать различные типы действий, например [функции действий](#activity-functions), [вложенные оркестрации](#sub-orchestrations), [ожидание внешних событий](#external-events) и [ таймеры](#durable-timers).  

Функция оркестратора должена активироваться [триггером оркестрации](durable-functions-bindings.md#orchestration-triggers).

Оркестратор запускается [клиентом оркестратора](#client-functions), который, в свою очередь, может быть запущен из любого источника (HTTP, очередей, потоков событий).  Каждый экземпляр оркестрации имеет идентификатор, создаваемый автоматически (рекомендуемый вариант) или создаваемый пользователем.  Этот идентификатор используется для [управления экземплярами](durable-functions-instance-management.md) оркестрации.

Дополнительные сведения и примеры можно найти в [статье о привязке устойчивых функций](durable-functions-bindings.md#orchestration-triggers).

### <a name="client-functions"></a>Клиентские функции

Клиентские функции — это активированные функции, которые создают экземпляры оркестрации.  Они представляют собой точку входа для создания экземпляра устойчивой оркестрации.  Клиентские функции запускаются с помощью любого триггера (HTTP, очередей, потоков событий и т. д.) и создаются на любом языке, поддерживаемом приложением.  Помимо триггера у клиентских функцией есть привязка [клиента оркестрации](durable-functions-bindings.md#orchestration-client), которая позволяет им создавать и контролировать устойчивые оркестрации.  Самым простым примером клиентской функции является активируемая по HTTP функция, которая запускает функцию оркестратора и возвращает ответ состояния проверки, как [показано в следующем примере](durable-functions-http-api.md#http-api-url-discovery).

Дополнительные сведения и примеры можно найти в [статье о привязке устойчивых функций](durable-functions-bindings.md#orchestration-client).

## <a name="features-and-patterns"></a>Возможности и шаблоны

### <a name="sub-orchestrations"></a>Вложенные оркестрации

Кроме вызова функций действия, функции оркестратора могут вызывать другие функции оркестратора. Например, вы можете построить создать крупную оркестрацию из библиотеки функций оркестратора. Или можно запустить несколько экземпляров функции оркестратора одновременно.

Дополнительные сведения и примеры можно найти в [статье о вложенной оркестрации](durable-functions-sub-orchestrations.md).

### <a name="durable-timers"></a>Устойчивые таймеры

[Устойчивые функции](durable-functions-overview.md) предоставляют *устойчивые таймеры*, которые используются в функциях оркестраторов для реализации задержек или настройки времени ожидания в асинхронных действиях. Устойчивые таймеры следует использовать в функциях оркестраторов вместо `Thread.Sleep` (C#) или `Task.Delay``setTimeout()``setInterval()` (JavaScript).

Дополнительные сведения и примеры устойчивых таймеров можно найти в [статье об устойчивых таймерах](durable-functions-timers.md).

### <a name="external-events"></a>Внешние события

Функции оркестратора могут ожидать передачи внешних событий для обновления экземпляра оркестрации. Эта возможность устойчивых функций часто используется для обработки действий человека или других внешних обратных вызовов.

Дополнительные сведения и примеры можно найти в [статье о внешних событиях](durable-functions-external-events.md).

### <a name="error-handling"></a>Обработка ошибок

Оркестрации устойчивых функций реализованы в коде и могут использовать возможности языка программирования по обработке ошибок.  Это означает, что в оркестрации будут работать шаблоны типа "try/catch".  В устойчивых функциях также реализованы некоторые встроенные политики повтора.  При возникновении исключений действие может автоматически откладывать и повторно выполнять действия.  Повторные попытки позволяют обрабатывать временные исключения, не отказываясь от оркестрации.

Дополнительные сведения и примеры можно найти в [статье об обработке ошибок](durable-functions-error-handling.md).

### <a name="cross-function-app-communication"></a>Взаимодействие между приложениями-функциями

В то время как устойчивая оркестрация обычно размещается в контексте одного приложения-функции, существуют шаблоны для координации оркестраций между множеством приложений-функций.  Несмотря на то, что взаимодействие между приложениями может осуществляться только по протоколу HTTP, используя платформу устойчивых функций для каждого действия, вы можете по-прежнему поддерживать устойчивый процесс между двумя приложениями.

Ниже приведен пример оркестрации между приложениями-функциями на C#.  Одно действие запускает внешнюю оркестрацию. Другое действие извлекает и возвращает состояние.  Перед продолжением оркестратор ожидает завершение состояния.

```csharp
[FunctionName("OrchestratorA")]
public static async Task RunRemoteOrchestrator(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    // Do some work...

    // Call a remote orchestration
    string statusUrl = await context.CallActivityAsync<string>(
        "StartRemoteOrchestration", "OrchestratorB");

    // Wait for the remote orchestration to complete
    while (true)
    {
        bool isComplete = await context.CallActivityAsync<bool>("CheckIsComplete", statusUrl);
        if (isComplete)
        {
            break;
        }

        await context.CreateTimer(context.CurrentUtcDateTime.AddMinutes(1), CancellationToken.None);
    }

    // B is done. Now go do more work...
}

[FunctionName("StartRemoteOrchestration")]
public static async Task<string> StartRemoteOrchestration([ActivityTrigger] string orchestratorName)
{
    using (var response = await HttpClient.PostAsync(
        $"https://appB.azurewebsites.net/orchestrations/{orchestratorName}",
        new StringContent("")))
    {
        string statusUrl = await response.Content.ReadAsAsync<string>();
        return statusUrl;
    }
}

[FunctionName("CheckIsComplete")]
public static async Task<bool> CheckIsComplete([ActivityTrigger] string statusUrl)
{
    using (var response = await HttpClient.GetAsync(statusUrl))
    {
        // 200 = Complete, 202 = Running
        return response.StatusCode == HttpStatusCode.OK;
    }
}
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Bindings for Durable Functions (Azure Functions)](durable-functions-bindings.md) (Привязки устойчивых функций (Функции Azure))

> [!div class="nextstepaction"]
> [Установка расширения устойчивых функций и примеров (Функции Azure)](durable-functions-install.md)

<!-- Media references -->
[1]: media/durable-functions-types-features-overview/durable-concepts.png