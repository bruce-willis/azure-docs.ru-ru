---
title: Использование функции увеличения масштаба в API распознавания лиц | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Использование функции увеличения масштаба в API распознавания лиц в Cognitive Services.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: f7b3ac57cf6b24c8a90b4ea59757d3a2cfafd781
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380332"
---
# <a name="how-to-use-the-large-scale-feature"></a>Как использовать функцию увеличения масштаба

Это руководство представляет собой расширенную статью о переносе кода для увеличения масштаба существующих PersonGroup и FaceList до LargePersonGroup и LargeFaceList соответственно.
В нем демонстрируется процесс переноса и предполагается, что пользователь владеет базовыми навыками работы с PersonGroup и FaceList.
Чтобы ознакомиться с базовыми операциями, обратитесь к другим руководствам, например [Как идентифицировать лица на изображениях](HowtoIdentifyFacesinImage.md).

Недавно в API распознавания лиц Майкрософт были добавлены две функции для крупномасштабных сценариев, LargePersonGroup и LargeFaceList, далее называемые крупномасштабными операциями.
LargePersonGroup может содержать до 1 000 000 человек и до 248 лиц для каждого из них, а LargeFaceList может содержать до 1 000 000 лиц.

Крупномасштабные операции аналогичны обычным PersonGroup и FaceList, но из-за новой архитектуры имеют некоторые важные отличия.
В этом руководстве демонстрируется процесс переноса и предполагается, что пользователь владеет базовыми навыками работы с PersonGroup и FaceList.
Примеры написаны на языке C# с помощью клиентской библиотеки API распознавания лиц.

Чтобы обеспечить производительность поиска лиц для операций Identification и FindSimilar в больших масштабах, необходимо вызвать операцию Train, чтобы предварительно обработать LargeFaceList и LargePersonGroup.
Время обучения составляет от нескольких секунд до получаса в зависимости фактического объема данных.
В ходе обучения можно выполнять операции Identification и FindSimilar, если перед этим уже было выполнено успешное обучение.
Однако недостаток заключается в том, что новые добавленные люди и лица не появятся в результатах, пока не будет завершено обучение после перехода на крупномасштабные операции.

## <a name="concepts"></a> Основные понятия

Если вы не знакомы с приведенными ниже понятиями в этом руководстве, их определения можно найти в нашем [глоссарии](../Glossary.md).

- LargePersonGroup: коллекция Person, содержащая до 1 000 000 элементов.
- LargePersonGroup: коллекция Face, содержащая до 1 000 000 элементов.
- Train: предварительная обработка для обеспечения производительности операций Identification и FindSimilar.
- Identification: определение одного или нескольких лиц в PersonGroup или LargePersonGroup.
- FindSimilar: поиск похожих лиц в FaceList или LargeFaceList.

## <a name="initialization"></a> Шаг 1. Авторизация вызова API

При использовании клиентской библиотеки API распознавания лиц ключ и конечная точка подписки передаются через конструктор класса FaceServiceClient. Например: 

```CSharp
string SubscriptionKey = "<Subscription Key>";
// Use your own subscription endpoint corresponding to the subscription key.
string SubscriptionRegion = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/";
FaceServiceClient FaceServiceClient = new FaceServiceClient(SubscriptionKey, SubscriptionRegion);
```

Ключ подписки и соответствующую конечную точку можно получить на странице "Marketplace" портала Azure.
Ознакомьтесь со страницей [подписок](https://azure.microsoft.com/services/cognitive-services/directory/vision/).

## <a name="migrate"></a> Шаг 2. Перенос кода

Этот раздел посвящен только переносу реализации PersonGroup и FaceList в LargePersonGroup и LargeFaceList.
Хотя структура и внутренняя реализация LargePersonGroup и LargeFaceList отличается от PersonGroup и FaceList, их интерфейсы API схожи, что обеспечивает обратную совместимость.

Перенос данных не поддерживается, вам потребуется воссоздать LargePersonGroup и LargeFaceList.

## <a name="largepersongroup"></a> Шаг 2.1. Перенос PersonGroup в LargePersonGroup

Перенести PersonGroup в LargePersonGroup просто, так как они используют одинаковые операции уровня группы.

Для реализации PersonGroup и Person необходимо только изменить пути API либо класс или модуль пакета SDK на LargePersonGroup и Person в LargePersonGroup.

С переносом данных можно ознакомиться в разделе [Как добавлять лица](how-to-add-faces.md).

## <a name="largefacelist"></a> Шаг 2.2. Перенос FaceList в LargeFaceList

| Интерфейсы API FaceList | Интерфейсы API LargeFaceList |
|:---:|:---:|
| Создание | Создание |
| Delete | Delete |
| Получить | Получить |
| список | список |
| Блокировка изменений | Блокировка изменений |
| - | Train |
| - | Get Training Status |

В таблице выше приведено сравнение операций уровня списка FaceList и LargeFaceList.
Как видно, в LargeFaceList есть новые операции, Train и Get Training Status, которых нет в FaceList.
Обучение LargeFaceList является предварительным условием для операции [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), тогда как для FaceList операция Train не требуется.
Ниже приведен фрагмент кода вспомогательной функции, которая ожидает обучения LargeFaceList.

```CSharp
/// <summary>
/// Helper function to train LargeFaceList and wait for finish.
/// </summary>
/// <remarks>
/// The time interval can be adjusted considering the following factors:
/// - The training time which depends on the capacity of the LargeFaceList.
/// - The acceptable latency for getting the training status.
/// - The call frequency and cost.
///
/// Estimated training time for LargeFaceList in different scale:
/// -     1,000 faces cost about  1 to  2 seconds.
/// -    10,000 faces cost about  5 to 10 seconds.
/// -   100,000 faces cost about  1 to  2 minutes.
/// - 1,000,000 faces cost about 10 to 30 minutes.
/// </remarks>
/// <param name="largeFaceListId">The Id of the LargeFaceList for training.</param>
/// <param name="timeIntervalInMilliseconds">The time interval for getting training status in milliseconds.</param>
/// <returns>A task of waiting for LargeFaceList training finish.</returns>
private static async Task TrainLargeFaceList(
    string largeFaceListId,
    int timeIntervalInMilliseconds = 1000)
{
    // Trigger a train call.
    await FaceServiceClient.TrainLargeFaceListAsync(largeFaceListId);

    // Wait for training finish.
    while (true)
    {
        Task.Delay(timeIntervalInMilliseconds).Wait();
        var status = await FaceServiceClient.GetLargeFaceListTrainingStatusAsync(largeFaceListId);

        if (status.Status == Status.Running)
        {
            continue;
        }
        else if (status.Status == Status.Succeeded)
        {
            break;
        }
        else
        {
            throw new Exception("The train operation is failed!");
        }
    }
}
```

Вот типичное использование FaceList с добавлением лиц и операцией FindSimilar.

```CSharp
// Create a FaceList.
const string FaceListId = "myfacelistid_001";
const string FaceListName = "MyFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateFaceListAsync(FaceListId, FaceListName).Wait();

// Add Faces to the FaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToFaceListAsync(FaceListId, stream);
            }
        });

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, FaceListId, 20));
    }
}
```

И вот как оно должно измениться при переносе в LargeFaceList.

```CSharp
// Create a LargeFaceList.
const string LargeFaceListId = "mylargefacelistid_001";
const string LargeFaceListName = "MyLargeFaceListDisplayName";
const string ImageDir = @"/path/to/FaceList/images";
FaceServiceClient.CreateLargeFaceListAsync(LargeFaceListId, LargeFaceListName).Wait();

// Add Faces to the LargeFaceList.
Parallel.ForEach(
    Directory.GetFiles(ImageDir, "*.jpg"),
    async imagePath =>
        {
            using (Stream stream = File.OpenRead(imagePath))
            {
                await FaceServiceClient.AddFaceToLargeFaceListAsync(LargeFaceListId, stream);
            }
        });

// Train() is newly added operation for LargeFaceList.
// Must call it before FindSimilarAsync() to ensure the newly added faces searchable.
await TrainLargeFaceList(LargeFaceListId);

// Perform FindSimilar.
const string QueryImagePath = @"/path/to/query/image";
var results = new List<SimilarPersistedFace[]>();
using (Stream stream = File.OpenRead(QueryImagePath))
{
    var faces = FaceServiceClient.DetectAsync(stream).Result;
    foreach (var face in faces)
    {
        results.Add(await FaceServiceClient.FindSimilarAsync(face.FaceId, largeFaceListId: LargeFaceListId));
    }
}
```

Как показано выше, управление данными и часть FindSimilar почти идентичны.
Единственным исключением является новая операция предварительной обработки Train, которую необходимо выполнить в LargeFaceList перед операцией FindSimilar.

## <a name="train"></a>Шаг 3. Формирование вариантов поиска

Хотя операция Train ускоряет операции [FindSimilar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) и [Identification](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), время обучения при большом масштабе особенно увеличивается.
В следующей таблице приведено предполагаемое время обучения при различных масштабах.

| Масштаб (люди или лица) | Предполагаемое время обучения |
|:---:|:---:|
| 1000 | 1–2 с |
| 10 000 | 5–10 с |
| 100 000 | 1–2 мин |
| 1 000 000 | 10–30 мин |

Для оптимального использования крупномасштабных функций рекомендуется рассмотреть несколько стратегий.

## <a name="interval"></a>Шаг 3.1. Интервал времени настройки

Как показано в `TrainLargeFaceList()`, имеется параметр `timeIntervalInMilliseconds`, позволяющий отложить процесс проверки бесконечного состояния обучения.
Если указать больший интервал для LargeFaceList с большим числом лиц, это сократит количество вызовов и затраты.
Интервал времени должно быть настроен в соответствии с ожидаемой емкостью LargeFaceList.

Эта стратегия применима и к LargePersonGroup.
Например, при обучении LargePersonGroup с 1 000 000 людьми интервал `timeIntervalInMilliseconds` может быть равен 60 000 (то есть 1 минуте).

## <a name="buffer"></a>Шаг 3.2. Небольшой буфер

Поиск элементов Person и Faces в LargePersonGroup и LargeFaceList будет возможен только после обучения.
В случае динамического сценария новые люди и лица добавляются постоянно и должны немедленно становиться доступными для поиска, хотя обучение может занимать больше времени, чем хотелось бы.
Чтобы устранить эту проблему, можно использовать дополнительные небольшие объекты LargePersonGroup и LargeFaceList в качестве буфера только для вновь добавленных записей.
Этот временный буфер требует меньше времени на обучение из-за гораздо меньшего размера, и быстрый поиск в нем будет работать.
Используйте этот буфер в сочетании с обучением на данных основных объектов LargePersonGroup и LargeFaceList, ежедневно выполняя основное обучение в менее загруженное время, например в полночь.

Пример рабочего процесса:
1. Создайте основные объекты LargePersonGroup и LargeFaceList (основную коллекцию) и буферные объекты LargePersonGroup и LargeFaceList (буферную коллекцию). Буферная коллекция предназначена только для вновь добавленных элементов Person и Face.
1. Добавляйте новые элементы Person и Face в основную и буферную коллекции.
1. Выполняйте обучение только буферной коллекции с небольшим интервалом, чтобы учитывать при поиске новые добавленные записи.
1. Вызывайте операции Identification и FindSimilar для основной и буферной коллекций, после чего объединяйте результаты.
1. Когда размер буферной коллекции достигнет порога или наступит простой системы, создайте новую буферную коллекцию и активируйте обучение на данных основной коллекции.
1. Удалите старую буферную коллекцию после завершения обучения на данных основной коллекции.

## <a name="standalone"></a>Шаг 3.3. Отдельное обучение

Если допускается относительно большая задержка, не обязательно активировать операцию Train сразу же после добавления новых данных.
Вместо этого операцию Train можно отделить от главной логики и активировать с регулярным интервалом.
Эта стратегия подходит для динамических сценариев с допустимой задержкой и может применяться для статических сценариев для уменьшения частоты операции Train.

Допустим, существует функция `TrainLargePersonGroup`, аналогичная `TrainLargeFaceList`.
Типичная реализация изолированного обучения на данных LargePersonGroup путем вызова класса [`Timer`](https://msdn.microsoft.com/library/system.timers.timer(v=vs.110).aspx) в `System.Timers` будет следующей.

```CSharp
private static void Main()
{
    // Create a LargePersonGroup.
    const string LargePersonGroupId = "mylargepersongroupid_001";
    const string LargePersonGroupName = "MyLargePersonGroupDisplayName";
    FaceServiceClient.CreateLargePersonGroupAsync(LargePersonGroupId, LargePersonGroupName).Wait();

    // Setup a standalone training at regular intervals.
    const int TimeIntervalForStatus = 1000 * 60; // 1 minute interval for getting training status.
    const double TimeIntervalForTrain = 1000 * 60 * 60; // 1 hour interval for training.
    var trainTimer = new Timer(TimeIntervalForTrain);
    trainTimer.Elapsed += (sender, args) => TrainTimerOnElapsed(LargePersonGroupId, TimeIntervalForStatus);
    trainTimer.AutoReset = true;
    trainTimer.Enabled = true;

    // Other operations like creating persons, adding faces and Identification except for Train.
    // ...
}

private static void TrainTimerOnElapsed(string largePersonGroupId, int timeIntervalInMilliseconds)
{
    TrainLargePersonGroup(largePersonGroupId, timeIntervalInMilliseconds).Wait();
}
```

Дополнительные сведения о реализации управления данными и идентификации см. в разделах [Как добавлять лица](how-to-add-faces.md) и [Практическое руководство по идентификации лиц на изображении](HowtoIdentifyFacesinImage.md).

## <a name="summary"></a> Сводная информация

В этом руководстве вы узнали, как перенести существующий код PersonGroup и FaceList (а не данные) в LargePersonGroup и LargeFaceList.

- LargePersonGroup и LargeFaceList работают аналогично PersonGroup и FaceList, за исключением того, что перед операцией LargeFaceList нужно выполнять операцию Train.
- Выберите соответствующую стратегию для динамического обновления данных в крупномасштабном наборе данных.

## <a name="related"></a> Связанные разделы

- [Практическое руководство по идентификации лиц на изображении](HowtoIdentifyFacesinImage.md)
- [Как добавлять лица](how-to-add-faces.md)
