---
title: Добавление лиц с помощью API распознавания лиц | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Использование API распознавания лиц в Cognitive Services для добавления лиц на изображения.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3306c13d6c3d231ddbda38cfcbc5419fcdbd30db
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380140"
---
# <a name="how-to-add-faces"></a>Как добавлять лица

В этом руководстве демонстрируются лучшие методики добавления значительного числа людей и лиц в PersonGroup.
Та же стратегия применима к FaceList и LargePersonGroup.
Примеры написаны на языке C# с помощью клиентской библиотеки API распознавания лиц.

## <a name="step1"></a> Шаг 1. Инициализация

Объявляется несколько переменных и реализуется вспомогательная функция для планирования запросов.

- `PersonCount` — общее количество людей.
- `CallLimitPerSecond` — максимальное количество вызовов в секунду в соответствии с категорией подписки.
- `_timeStampQueue` — очередь для записи меток времени запросов.
- `await WaitCallLimitPerSecondAsync()` задает ожидание, пока допустима отправка следующего запроса.

```CSharp
const int PersonCount = 10000;
const int CallLimitPerSecond = 10;
static Queue<DateTime> _timeStampQueue = new Queue<DateTime>(CallLimitPerSecond);

static async Task WaitCallLimitPerSecondAsync()
{
    Monitor.Enter(_timeStampQueue);
    try
    {
        if (_timeStampQueue.Count >= CallLimitPerSecond)
        {
            TimeSpan timeInterval = DateTime.UtcNow - _timeStampQueue.Peek();
            if (timeInterval < TimeSpan.FromSeconds(1))
            {
                await Task.Delay(TimeSpan.FromSeconds(1) - timeInterval);
            }
            _timeStampQueue.Dequeue();
        }
        _timeStampQueue.Enqueue(DateTime.UtcNow);
    }
    finally
    {
        Monitor.Exit(_timeStampQueue);
    }
}
```

## <a name="step2"></a>Шаг 2. Авторизация вызова API

При использовании клиентской библиотеки ключ подписки передается через конструктор класса FaceServiceClient. Например: 

```CSharp
FaceServiceClient faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

Ключ подписки можно получить на странице "Marketplace" портала Azure. Ознакомьтесь со страницей [подписок](https://www.microsoft.com/cognitive-services/en-us/sign-up).

## <a name="step3"></a>Шаг 3. Создание PersonGroup

Для сохранения данных людей лиц создается PersonGroup с именем MyPersonGroup.
Время запроса ставится в очередь `_timeStampQueue` для обеспечения общей проверки.

```CSharp
const string personGroupId = "mypersongroupid";
const string personGroupName = "MyPersonGroup";
_timeStampQueue.Enqueue(DateTime.UtcNow);
await faceServiceClient.CreatePersonGroupAsync(personGroupId, personGroupName);
```

## <a name="step4"></a>Шаг 4. Создание данных людей в PersonGroup

Данные людей создаются одновременно. Кроме того, во избежание превышения предельного количества вызовов применяется `await WaitCallLimitPerSecondAsync()`.

```CSharp
CreatePersonResult[] persons = new CreatePersonResult[PersonCount];
Parallel.For(0, PersonCount, async i =>
{
    await WaitCallLimitPerSecondAsync();

    string personName = $"PersonName#{i}";
    persons[i] = await faceServiceClient.CreatePersonAsync(personGroupId, personName);
});
```

## <a name="step5"></a>Шаг 5. Добавление лиц людей

Добавление лиц разных людей выполняется параллельно, в то время как для одного определенного пользователя это выполняется последовательно.
Опять же, во избежание превышения предельной частоты запросов вызывается `await WaitCallLimitPerSecondAsync()`.

```CSharp
Parallel.For(0, PersonCount, async i =>
{
    Guid personId = persons[i].PersonId;
    string personImageDir = @"/path/to/person/i/images";

    foreach (string imagePath in Directory.GetFiles(personImageDir, "*.jpg"))
    {
        await WaitCallLimitPerSecondAsync();

        using (Stream stream = File.OpenRead(imagePath))
        {
            await faceServiceClient.AddPersonFaceAsync(personGroupId, personId, stream);
        }
    }
});
```

## <a name="summary"></a> Сводная информация

В этом руководстве вы узнали о процессе создания PersonGroup с большим числом людей и лиц. Несколько напоминаний:

- Эта стратегия также применима к FaceList и LargePersonGroup.
- Добавление или удаление лиц для разных объектов FaceList или Person в LargePersonGroup может обрабатываться параллельно.
- Те же операции для одного конкретного FaceList или Person в LargePersonGroup должны выполняться последовательно.
- В целях упрощения в данном руководстве не рассматривается обработка возможных исключений. Если вы хотите повысить надежность, следует применить соответствующую политику повтора.

Ниже приведены краткие напоминания об особенностях, которые были описаны и продемонстрированы.

- Создание объектов PersonGroup с помощью API [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244).
- Создание данных людей с помощью API [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c).
- Добавление лиц людей с помощью API [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="related"></a> Связанные разделы
- [Практическое руководство по идентификации лиц на изображении](HowtoIdentifyFacesinImage.md)
- [Практическое руководство по обнаружению лиц на изображении](HowtoDetectFacesinImage.md)
- [Как использовать функцию увеличения масштаба](how-to-use-large-scale.md)
