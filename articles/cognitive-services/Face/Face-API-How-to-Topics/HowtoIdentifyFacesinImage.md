---
title: Идентификация лиц на изображении с помощью API распознавания лиц | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Использование API распознавания лиц Cognitive Services для идентификации лиц на изображениях.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 3f75db176055d9f784ec978497d7cae077ff629f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382684"
---
# <a name="how-to-identify-faces-in-images"></a>Как идентифицировать лица на изображениях

В этом руководстве показано, как идентифицировать неизвестные лица с помощью PersonGroups, заранее созданные на основе известных людей. Примеры написаны на языке C# с помощью API распознавания лиц для клиентской библиотеки.

## <a name="concepts"></a>Основные понятия

Если вы не знакомы со следующими понятиями в этом руководстве, выполните поиск определений в нашем [глоссарии](../Glossary.md) в любое время:

- Лицо — обнаружить;
- Лицо — идентифицировать;
- PersonGroup.

## <a name="preparation"></a> Подготовка

В этом образце продемонстрировано следующее:

- Как создать PersonGroup. PersonGroup содержит список известных людей.
- Как присвоить лица каждому человеку. Эти лица используются в качестве основы для идентификации людей. Рекомендуется использовать четкое изображение лица, как в удостоверении личности. Хороший набор фотографий должен содержать фрагменты лица одного и того же человека в разных позах, в одежде разных цветов или с разными прическами.

Чтобы выполнить демонстрацию этого образца, вам нужно подготовить много фотографий:

- Несколько фотографий с лицом человека. [Щелкните здесь, чтобы загрузить образец фото](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data) для Анны, Билла и Клэр.
- Серию тестовых фотографий, которые могут содержать или не содержать лица Анны, Билла или Клэр, используемые для проверки идентификации. Вы также можете выбрать некоторые образцы изображений из предыдущей ссылки.

## <a name="step1">Шаг 1. Авторизация вызова API</a>

Каждый вызов API распознавания лиц требует ключ подписки. Этот ключ может быть либо передан через параметр строки запроса, либо указан в заголовке запроса. Чтобы передать ключ подписки через строку запроса, обратитесь к URL-адресу запроса [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) в качестве примера:
```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription key>
```

В качестве альтернативы ключ подписки также можно указать в заголовке запроса HTTP: **ocp-apim-subscription-key:&lt;Ключ подписки&gt;**. При использовании клиентской библиотеки ключ подписки передается через конструктор класса FaceServiceClient. Например: 
 
```CSharp 
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```
 
На странице Marketplace портала Azure можно получить ключ подписки. Ознакомьтесь со страницей [подписок](https://azure.microsoft.com/try/cognitive-services/).

## <a name="step2"></a> Шаг 2. Создание PersonGroup

На этом шаге мы создали PersonGroup, названную MyFriends, содержащую троих людей: Анну, Билла и Клэр. Каждый пользователь имеет несколько зарегистрированных лиц. Лица должны определяться с изображений. После выполнения всех этих шагов у вас есть PersonGroup, как показано на следующем изображении:

![HowToIdentify1](../Images/group.image.1.jpg)

### <a name="step2-1"></a> 2.1 Определение людей для PersonGroup
Пользователь является базовой единицей идентификации. Для пользователя может быть зарегистрировано одно или несколько известных лиц. Тем не менее, PersonGroup — это совокупность людей, и каждый человек определен в конкретной группе людей. Идентификация выполняется в PersonGroup. Таким образом от задачи требуется создать группу людей (PersonGroup), а затем создать в ней людей, таких как Анна, Билл и Клэр.

Сначала необходимо создать группу людей. Это выполняется с помощью API [РersonGroup — создание](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244). Соответствующим API клиентской библиотеки является метод CreatePersonGroupAsync для класса FaceServiceClient. Идентификатор группы, указанный для создания группы, уникален для каждой подписки. Вы также можете получать, обновлять или удалять PersonGroups с использованием других API группы людей. Как только группа определена, в ней можно определить людей, используя API [PersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c). Метод клиентской библиотеки — CreatePersonAsync. Вы можете добавить лицо для каждого человека после его создания.

```CSharp 
// Create an empty PersonGroup
string personGroupId = "myfriends";
await faceServiceClient.CreatePersonGroupAsync(personGroupId, "My Friends");
 
// Define Anna
CreatePersonResult friend1 = await faceServiceClient.CreatePersonAsync(
    // Id of the PersonGroup that the person belonged to
    personGroupId,    
    // Name of the person
    "Anna"            
);
 
// Define Bill and Clare in the same way
```
### <a name="step2-2"></a> 2.2 Обнаружение лиц и их регистрация для корректировки человека
Обнаружение выполняется путем отправки веб-запроса POST в API [Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) с файлом изображения в теле запроса HTTP. При использовании клиентской библиотеки распознавание лиц выполняется методом DetectAsync для класса FaceServiceClient.

Для каждого обнаруженного лица вы можете вызвать [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b), чтобы добавить его к правильному человеку.

Следующий код демонстрирует процесс определения лица с изображения и добавления его к человеку:
```CSharp 
// Directory contains image files of Anna
const string friend1ImageDir = @"D:\Pictures\MyFriends\Anna\";
 
foreach (string imagePath in Directory.GetFiles(friend1ImageDir, "*.jpg"))
{
    using (Stream s = File.OpenRead(imagePath))
    {
        // Detect faces in the image and add to Anna
        await faceServiceClient.AddPersonFaceAsync(
            personGroupId, friend1.PersonId, s);
    }
}
// Do the same for Bill and Clare
``` 
Обратите внимание, если изображение содержит несколько лиц, добавляется только самое большое. Вы можете добавить к лицу другие лица, передав строку в формате targetFace=left, top, width, height в параметр targetFace API [PersonGroup Person — Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) или используя необязательный параметр targetFace для метода AddPersonFaceAsync для добавления других лиц. Каждому лицу, добавленному для человека, будет присвоен уникальный идентификатор лица, который может быть использован в [PersonGroup Person — Delete Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523e) и [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
## <a name="step3"></a> Шаг 3. Обучение группы людей

Группа людей должна пройти обучение до того, как ее можно будет идентифицировать. Кроме того, она должна быть повторно обучена после добавления или удаления какого-либо лица, или если зарегистрированное лицо человека было изменено. Обучение выполняется API-интерфейсом [PersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249). При использовании клиентской библиотеки это просто вызов метода TrainPersonGroupAsync:
 
```CSharp 
await faceServiceClient.TrainPersonGroupAsync(personGroupId);
```
 
Обучение является асинхронным процессом. Оно может не завершиться даже после возврата метода TrainPersonGroupAsync. Возможно, вам потребуется запросить статус обучения, используя API [PersonGroup — Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247) или метод GetPersonGroupTrainingStatusAsync клиентской библиотеки. В следующем примере демонстрируется простая логика ожидания завершения обучения группы людей:
 
```CSharp 
TrainingStatus trainingStatus = null;
while(true)
{
    trainingStatus = await faceServiceClient.GetPersonGroupTrainingStatusAsync(personGroupId);
 
    if (trainingStatus.Status != Status.Running)
    {
        break;
    }
 
    await Task.Delay(1000);
} 
``` 

## <a name="step4"></a> Шаг 4. Определение лица для определенной группы людей
При выполнении идентификаций API распознавания лиц может вычислять сходство контрольной поверхности между всеми лицами внутри группы и возвращать наиболее сопоставимое лицо для этого тестируемого лица. Это осуществляется с помощью API [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) или метода клиентской библиотеки IdentifyAsync.

Лицо для тестирования должно быть обнаружено с использованием предыдущих шагов, а затем идентификатор лица передается идентификатору API в качестве второго аргумента. Идентификаторы нескольких лиц могут быть определены сразу, и результат будет содержать все результаты идентификации. По умолчанию идентификатор возвращает только одного человека, который лучше всего подходит для тестового лица. При желании вы можете указать необязательный параметр maxNumOfCandidatesReturned, чтобы идентификатор возвращал больше кандидатов.

Этот процесс идентификации показан в следующем коде:
```CSharp 
string testImageFile = @"D:\Pictures\test_img1.jpg";

using (Stream s = File.OpenRead(testImageFile))
{
    var faces = await faceServiceClient.DetectAsync(s);
    var faceIds = faces.Select(face => face.FaceId).ToArray();
 
    var results = await faceServiceClient.IdentifyAsync(personGroupId, faceIds);
    foreach (var identifyResult in results)
    {
        Console.WriteLine("Result of face: {0}", identifyResult.FaceId);
        if (identifyResult.Candidates.Length == 0)
        {
            Console.WriteLine("No one identified");
        }
        else
        {
            // Get top 1 among all candidates returned
            var candidateId = identifyResult.Candidates[0].PersonId;
            var person = await faceServiceClient.GetPersonAsync(personGroupId, candidateId);
            Console.WriteLine("Identified as {0}", person.Name);
        }
    }
}
``` 

После завершения действия вы можете попытаться определить разные лица и посмотреть, можно ли правильно определить лица Анны, Билла или Клэр в соответствии с изображениями, загруженными для обнаружения лиц. Рассмотрим следующие примеры:

![HowToIdentify2](../Images/identificationResult.1.jpg )

## <a name="step5"></a> Шаг 5. Крупномасштабный запрос

Как известно, группа людей может вмещать до 10 000 человек из-за ограничения предыдущего проектирования.
Дополнительную информацию о миллионных сценариях см. в статье [How to use the large-scale feature](how-to-use-large-scale.md) (Как использовать функцию увеличения масштаба).

## <a name="summary"></a> Сводная информация

В этом руководстве вы узнали о процессе создания группы людей и идентификации человека. Ниже приведено краткое напоминание об особенностях, которые были описаны и продемонстрированы:

- Обнаружение лиц с помощью API[Face — Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d).
- Создание PersonGroups с помощью API [PersonGroup — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244).
- Создание группы людей с помощью API [PersonGroup Person — Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c).
- Обучение PersonGroup с помощью API [PersonGroup — Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).
- Идентификация неизвестного лица в группе людей с помощью API [Face — Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="related"></a> Связанные разделы

- [How to Detect Faces in Image](HowtoDetectFacesinImage.md) (Как обнаружить лица на изображении)
- [How to add faces](how-to-add-faces.md) (Как добавлять лица)
- [How to use the large-scale feature](how-to-use-large-scale.md) (Использование функции для увеличения масштаба)
