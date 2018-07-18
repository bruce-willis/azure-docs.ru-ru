---
title: Приложение Javascript для обнаружения аномалий в Microsoft Cognitive Services | Документация Майкрософт
description: Изучите веб-приложение Javascript, в котором используется API обнаружения аномалий в Microsoft Cognitive Services. Отправьте исходные точки данных в API и получите ожидаемое значение и точки аномалий.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 42c3941a05efe8b74f818cd99f3606b3073892a9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38453175"
---
# <a name="anomaly-detection-javascript-application"></a>Приложение Javascript для обнаружения аномалий

Изучите веб-приложение, которое использует REST API обнаружения аномалий для обнаружения аномалий. В примере выполняется отправка данных временных рядов в API обнаружения аномалий с ключом подписки и последующее получение всех точек аномалий и ожидаемого значения для каждой точки данных из API.

## <a name="prerequisites"></a>предварительным требованиям

### <a name="platform-requirements"></a>Требования платформы

Это руководство было разработано с использованием простого текстового редактора.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Подписка на службу обнаружения аномалий и получение ключа подписки 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Получение и использование примера

В этом руководстве представлены два сценария для обнаружения аномалий в данных временных рядов. Давайте приступим.

<a name="Step1"></a> 
### <a name="download-the-tutorial-project"></a>Скачивание учебного проекта

Клонируйте репозиторий [Cognitive Services JavaScript Anomaly Detection Tutorial](https://github.com/MicrosoftAnomalyDetection/javascript-sample) или скачайте ZIP-файл и извлеките его содержимое в пустой каталог.

<a name="Step2"></a>
### <a name="run-the-example"></a>Выполнение примера

Вы можете изучить примеры для двух сценариев.
1. Введите **ключ подписки** в поле "Subscription Key" (Ключ подписки) функции обнаружения на странице anomalydetection.html.
2. Укажите конечную точку API обнаружения аномалий и убедитесь, что вы указали правильный регион в поле "Subscription Region" (Регион подписки).
3. Откройте файл **anomalydetection.html** в веб-браузере.

**Сценарий 1. Обнаружение еженедельных данных временных рядов**
1. В поле "Period" (Период) укажите значение **7**. 
2. Замените пример данных своими еженедельным точками данных временных рядов (JSON) в поле "Points" (Точки) или используйте предложенный пример данных.
3. Нажмите кнопку "Anomaly Detection" (Обнаружить аномалии) и просмотрите результаты в текстовом поле "Response" (Ответ) справа.

**Сценарий 2. Обнаружение данных временных рядов без периода**
1. Оставьте пустым поле "Period" (Период). Предположим, вам неизвестен период временных рядов.
2. Используйте те же данные временных рядов, что и в сценарии 1.
3. Нажмите кнопку "Anomaly Detection" (Обнаружить аномалии) и просмотрите поле "Period" (Период) в текстовом поле "Response" (Ответ) справа.

<a name="Step3"></a>
### <a name="read-the-result"></a>Чтение результатов

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Проверки и изучение

Теперь у вас есть работающее приложение. Давайте на его примере рассмотрим интеграцию приложения с технологией Cognitive Services. Благодаря этому вам будет проще продолжить развитие этого приложения или разработать собственное приложение, которое использует службу обнаружения аномалий (Майкрософт).
Этот пример приложения использует конечную точку REST API обнаружения аномалий.
Чтобы понять, как REST API используется в примере приложения, давайте рассмотрим фрагмент кода из файла anomalydetection.cs.
```JavaScript
function anomalyDetection(url, subscriptionKey, points, period) {
    var obj = new Object();
    obj.Points = JSON.parse(points); // this points are read from text box.
    obj.Period = parseInt(period);//period=7 weekly period
    var tsData = JSON.stringify(obj);
    // Perform the REST API call.
    $.ajax({
        url: url, //Anomaly Detection API endpoint
        // Request headers.
        beforeSend: function (xhrObj) {
            xhrObj.setRequestHeader("Content-Type", "application/json");
            xhrObj.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey); // Replace your subscription key
        },
        type: "POST",
        // Request body.
        data: tsData, // json format
        })
        .done(function (data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })
        .fail(function (jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ? "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" : jQuery.parseJSON(jqXHR.responseText).message;
            $("#responseTextArea").val(errorString);           
        });
}

```

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Справочник по REST API](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
