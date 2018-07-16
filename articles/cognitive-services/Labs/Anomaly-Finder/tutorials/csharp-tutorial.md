---
title: Приложение C# для обнаружения аномалий — Microsoft Cognitive Services | Документация Майкрософт
description: Изучите приложение C#, в котором используется API обнаружения аномалий в службах Microsoft Cognitive Services. Отправьте исходные точки данных в API и получите ожидаемое значение и точки аномалий.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2e4100fd7d8e85a6b103c31000176aaaeb3d7151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381332"
---
# <a name="anomaly-detection-c-application"></a>Приложение C# для обнаружения аномалий

Изучите простое приложение для Windows, которое использует API для обнаружения аномалий во входных данных. В примере выполняется отправка данных по временным рядам в API обнаружения аномалий с ключом подписки и последующее получение всех точек аномалий и ожидаемого значения для каждой точки данных из API.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="platform-requirements"></a>Требования платформы

Пример был разработан для платформы .NET Framework с помощью [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Подписка на службу обнаружения аномалий и получение ключа подписки 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Получение и использование примера

Вы можете клонировать на компьютер пример приложения для обнаружения аномалий из репозитория [GitHub](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>Установка примера

В GitHub Desktop откройте файл Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>Компиляция примера

Нажмите клавиши CTRL+SHIFT+B или выберите действие "Build" (Сборка) в меню ленты, а затем выберите "Build Solution" (Собрать решение).

<a name="Step3"></a>
### <a name="run-the-example"></a>Выполнение примера

1. После завершения сборки нажмите клавишу **F5** или щелкните **Start** (Запустить) в меню ленты, чтобы выполнить созданный пример.
2. Найдите окно пользовательского интерфейса системы обнаружения аномалий, в котором текстовое поле содержит значение "{your_subscription_key}".
3. В файле request.json замените данные примера собственными данными, а затем нажмите кнопку "Отправить". Корпорация Майкрософт получит отправленные вами данные и проведет их анализ для определения точек аномалий в этих данных. Отправляемые данные не сохраняются на сервере корпорации Майкрософт. Чтобы повторить поиск точек аномалий, данные придется отправить повторно.
4. Если данные пригодны для анализа, результат обнаружения аномалий будет помещен в поле "Response" (Ответ). Если произойдет любая ошибка, в это поле будет помещена информация об этой ошибке.

<a name="Review"></a>
### <a name="read-the-result"></a>Чтение результатов

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Изучение и понимание

Теперь у вас есть работающее приложение, и на его примере мы рассмотрим интеграцию приложений с технологией Cognitive Services. Благодаря этому вам будет проще продолжать развитие этого приложения или разработать собственное приложение, которое использует службу обнаружения аномалий от Майкрософт.

Этот пример приложения использует конечную точку REST API обнаружения аномалий.

Чтобы понять, как REST API используется в примере приложения, давайте рассмотрим фрагмент кода из файла **AnomalyDetectionClient.cs**. Этот файл содержит комментарии к коду, в которых есть фразы "KEY SAMPLE CODE STARTS HERE" (Здесь начинается основной пример кода) и "KEY SAMPLE CODE ENDS HERE" (Здесь завершается основной пример кода), которые помогут найти приведенные ниже фрагменты кода.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
**Request(...)**. В следующем фрагменте кода показано, как с помощью HttlClient отправить ключ подписки и точки данных в конечную точку API обнаружения аномалий.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
