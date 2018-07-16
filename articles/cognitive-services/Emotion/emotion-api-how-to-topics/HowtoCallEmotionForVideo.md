---
title: Вызов API распознавания эмоций для видео | Документация Майкрософт
description: Узнайте, как вызывать API распознавания эмоций для видео в Cognitive Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 0875013b2061a84e3e23ae90c1106382672fdca6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380721"
---
# <a name="how-to-call-emotion-api-for-video"></a>Как вызвать API распознавания эмоций для видео

> [!IMPORTANT]
> Срок действия предварительной версии API для видео истекает 30 октября 2017 г. Оцените новую [предварительную версию API индексатора видео](https://azure.microsoft.com/services/cognitive-services/video-indexer/), которая позволяет легко извлекать аналитические сведения из видео и оптимизировать возможности обнаружения содержимого (например, результаты поиска) по обнаружению произнесенных слов, лиц, символов и эмоций. [Узнайте больше](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

В этом руководстве показано, как вызвать API распознавания эмоций для видео. Примеры написаны на языке C# с помощью API распознавания эмоций для клиентской библиотеки видео.

### <a name="Prep">Подготовка</a> 
Чтобы использовать API распознавания эмоций для видео, вам понадобится видео с людьми, стоящими лицом к камере.

### <a name="Step1">Шаг 1. Авторизация вызова API</a> 
Для каждого вызова API распознавания эмоций для видео требуется ключ подписки. Этот ключ должен быть либо передан через параметр строки запроса, либо указан в заголовке запроса. Чтобы передать ключ подписки через строку запроса, см. пример URL-адреса запроса ниже для API распознавания эмоций для видео:

```
https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognizeInVideo&subscription-key=<Your subscription key>
```

В качестве альтернативы ключ подписки также можно указать в заголовке запроса HTTP:

```
ocp-apim-subscription-key: <Your subscription key>
```

При использовании клиентской библиотеки ключ подписки передается через конструктор класса VideoServiceClient. Например: 

```
var emotionServiceClient = new emotionServiceClient("Your subscription key");
```
Чтобы получить ключ подписки, см. раздел [Подписки] (https://azure.microsoft.com/try/cognitive-services/). 

### <a name="Step2">Шаг 2. Отправка видео в службу и проверка состояния</a>
Самый простой способ выполнить любой из вызовов API распознавания эмоций для видео — это напрямую отправить видео. Для этого нужно отправить запрос "POST" с типом содержимого application/octet-stream вместе с данными, считываемыми из видеофайла. Максимальный размер видео —100 МБ.

При использовании клиентской библиотеки стабилизация за счет отправки выполняется путем передачи объекта потока. См. пример ниже.

```
Operation videoOperation;
using (var fs = new FileStream(@"C:\Videos\Sample.mp4", FileMode.Open))
{
      videoOperation = await videoServiceClient.CreateOperationAsync(fs, OperationType.recognizeInVideo);
}
```

Обратите внимание, что метод CreateOperationAsync для VideoServiceClient является асинхронным. Вызывающий метод должен быть помечен как async для использования предложения await.
Если видео уже находится в Интернете и имеет общедоступный URL-адрес, можно получить доступ к API распознавания эмоций для видео, указав URL-адрес. В этом примере тело запроса будет строкой JSON, содержащей URL-адрес.

При применении клиентской библиотеки стабилизацию с помощью URL-адреса можно легко выполнить, используя другую перегрузку метода CreateOperationAsync.


```
var videoUrl = "http://www.example.com/sample.mp4";
Operation videoOperation = await videoServiceClient.CreateOperationAsync(videoUrl, OperationType. recognizeInVideo);

```

Этот метод отправки будет одинаковым для всех вызовов API распознавания эмоций для видео. 

После отправки видео нужно проверить его статус. Так как видеофайлы обычно крупнее и разнообразнее других файлов, на этом этапе пользователи могут столкнуться с длительным временем обработки. Время зависит от размера и длины файла.

Используя клиентскую библиотеку, вы можете получить состояние и результат операции с помощью метода GetOperationResultAsync.


```
var operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);

```
Как правило, клиентская сторона должна периодически получать состояние операции, пока не отобразится состояние "Успешно" или "Сбой".

```
OperationResult operationResult;
while (true)
{
      operationResult = await videoServiceClient.GetOperationResultAsync(videoOperation);
      if (operationResult.Status == OperationStatus.Succeeded || operationResult.Status == OperationStatus.Failed)
      {
           break;
      }

      Task.Delay(30000).Wait();
}

```

Когда состояние VideoOperationResult сменится на "Успешно", результат может быть получен путем приведения VideoOperationResult к VideoOperationInfoResult<VideoAggregateRecognitionResult> и доступа к полю ProcessingResult.

```
var emotionRecognitionJsonString = ((VideoOperationInfoResult<VideoAggregateRecognitionResult>)operationResult).ProcessingResult;
```

### <a name="Step3">Шаг 3. Получение распознанных эмоций, их анализ и отслеживание выходных данных JSON</a>

Выходной результат содержит метаданные по лицам в заданном JSON-файле.

Как описано в шаге 2, выходные данные JSON доступны в поле ProcessingResult OperationResult, когда его статус отображается как "Успешно".

В JSON-файл обнаружения и отслеживания лиц входят следующие атрибуты:

Атрибут | ОПИСАНИЕ
-------------|-------------
Version (версия) | Ссылается на версию API распознавания эмоций для видео JSON.
Шкала времени | Количество тактов в секунду видео.
Offset  |Смещение времени для меток времени. В версии 1.0 API распознавания эмоций для видео это всегда будет 0. В будущих поддерживаемых сценариях это значение может измениться.
Framerate | Количество кадров в секунду видео.
Fragments   | Метаданные разделены на разные более мелкие элементы, называемые фрагментами. Каждый фрагмент имеет начало, длительность, номер интервала и события.
Начало   | Время начала первого события в тактах.
Duration |  Продолжительность фрагмента в тактах.
Интервал |  Длина каждого события внутри фрагмента в тактах.
События  | Массив событий. Внешний массив представляет один интервал времени. Внутренний массив состоит из нуля или более событий, которые выполнялись в этот момент времени.
windowFaceDistribution |    Процент лиц, выражающих определенную эмоцию во время события.
windowMeanScores |  Средние оценки для каждой эмоции на лицах на изображении.

JSON нужно форматировать таким образом, чтобы настроить API для будущих сценариев, где будет важно быстро получать метаданные и управлять большим потоком результатов. Это форматирование использует методы фрагментации (позволяющие разбить метаданные на периоды по времени, где вы можете загружать только то, что вам нужно) и сегментации (позволяющие разбить события, если они становятся слишком большими). Выполнив простые вычисления, вы сможете преобразовать данные. Например, если событие началось на 6300 (такте) со шкалой времени 2997 (тактов в секунду) и частотой кадров 29,97 (кадров в секунду), то:

*   начало/шкала времени = 2,1 секунды
*   секунды x (частота кадров/шкала времени) = 63 кадра

Ниже приведен простой пример извлечения JSON-файла в формат кадра для обнаружения и отслеживания лиц.

```
var emotionRecognitionTrackingResultJsonString = operationResult.ProcessingResult;
var emotionRecognitionTracking = JsonConvert.DeserializeObject<EmotionRecognitionResult>(emotionRecognitionTrackingResultJsonString, settings);
```
Так как эмоции сглаживаются с течением времени, при создании визуализации для наложения ваших результатов поверх исходного видео вычтите 250 миллисекунд из предоставленной отметки времени.

### <a name="Summary">Сводка</a>
В этом руководстве вы узнали о функциях API распознавания эмоций для видео: отправка видео, проверка его состояния и получение метаданных распознавания эмоций.

Дополнительные сведения об API см. в справочном руководстве по API [Emotion API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f8d40e1984551ec0a0984e) (API распознавания эмоций).
