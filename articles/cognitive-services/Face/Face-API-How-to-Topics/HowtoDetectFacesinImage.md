---
title: Обнаружение лиц на изображениях с помощью API распознавания лиц | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: Использование API распознавания лиц в Cognitive Services для обнаружения лиц на изображениях.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 57cd0915450428399fd680638aa4fae2cdbe17c9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380244"
---
# <a name="how-to-detect-faces-in-image"></a>Как обнаруживать лица на изображении

В этом руководстве демонстрируется обнаружение лиц на изображении и извлечение таких атрибутов лица, как пол, возраст или положение. Примеры написаны на языке C# с помощью клиентской библиотеки API распознавания лиц. 

## <a name="concepts"></a> Основные понятия

Если вы не знакомы с какими либо понятиями в этом руководстве, ознакомьтесь с определениями в нашем [глоссарии](../Glossary.md) в любое время. 

- Обнаружение лиц
- Ориентиры лица
- Положение головы
- Атрибуты лица

## <a name="preparation"></a> Подготовка

В этом примере демонстрируются следующие возможности: 

- Распознавание лиц на изображении и их выделение с помощью прямоугольной рамки.
- Анализ расположения зрачков, носа или рта и их выделение на изображении.
- Анализ положения головы, пола и возраста лица.

Для выполнения этих функций необходимо подготовить изображение по крайней мере с одним четким изображением лица. 

## <a name="step1"></a> Шаг 1. Авторизация вызова API

Каждый вызов API распознавания лиц требует ключ подписки. Этот ключ должен быть либо передан через параметр строки запроса, либо указан в заголовке запроса. Чтобы передать ключ подписки через строку запроса, обратитесь к URL-адресу запроса [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) в качестве примера.

```
https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes]
&subscription-key=<Subscription Key>
```

В качестве альтернативы ключ подписки также можно указать в заголовке запроса HTTP: **ocp-apim-subscription-key: &lt;Ключ подписки&gt;**. При использовании клиентской библиотеки ключ подписки передается через конструктор класса FaceServiceClient. Например: 
```CSharp
faceServiceClient = new FaceServiceClient("<Subscription Key>");
```

## <a name="step2"></a> Шаг 2. Передача изображения в службу и выполнения обнаружения лиц

Проще всего выполнить обнаружение лиц, передав изображение напрямую. Для этого нужно отправить запрос POST с типом содержимого application/octet-stream и считать данные из изображения в формате JPEG. Максимальный размер изображения составляет 4 МБ.

При использовании клиентской библиотеки обнаружения лиц передается объект Stream. См. пример ниже.
```CSharp
using (Stream s = File.OpenRead(@"D:\MyPictures\image1.jpg"))
{
    var faces = await faceServiceClient.DetectAsync(s, true, true);
 
    foreach (var face in faces)
    {
        var rect = face.FaceRectangle;
        var landmarks = face.FaceLandmarks;
    }
}
```

Обратите внимание на то, что метод DetectAsync FaceServiceClient является асинхронным. Вызывающий метод должен быть помечен как асинхронный для использования предложения await.
Если изображение уже размещено в Интернете и имеет URL-адрес, его также можно использовать для обнаружения лиц. В этом примере текстом запроса будет строка JSON, содержащая URL-адрес.
Выполнить обнаружение лиц с помощью клиентской библиотеки, указав URL-адрес, довольно просто. Для этого следует использовать другую перегрузку метода DetectAsync.
```CSharp
string imageUrl = "http://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceServiceClient.DetectAsync(imageUrl, true, true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
}
``` 

Свойство FaceRectangle, возвращаемое вместе с определенными лицами, представляет собой расположения на лице в пикселях. Как правило, этот прямоугольник содержит глаза, брови, нос и рот. Верхняя часть головы, уши и подбородок не включаются. При обрезке всей головы или портрета (как на фотографии для документов) может потребоваться увеличить область прямоугольной рамки вокруг лица, так как область лица может быть слишком маленькой для некоторых приложений. Для более точного нахождения лица можно использовать ориентиры лица (механизмы поиска черт или направления лица), как описано в следующем разделе.

## <a name="step3"></a> Шаг 3. Описание и использование ориентиров лица

Ориентиры лица представляют собой ряд информативных точек на лице. Обычно они расположены на таких частях лица, как зрачки, угол глазной щели или нос. Ориентиры лица стороны являются необязательными атрибутами, которые могут быть проанализированы во время обнаружения лиц. Вы можете передать логическое значение true для параметра запроса returnFaceLandmarks при вызове [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) или использовать необязательный параметр returnFaceLandmarks для метода DetectAsync класса FaceServiceClient, чтобы включить ориентиры лиц в результаты обнаружения.

По умолчанию используются 27 предопределенных точек ориентиров. На следующем рисунке показаны все точек 27.

![HowToDetectFace](../Images/landmarks.1.jpg)

Данные точек возвращаются в пикселях, как и прямоугольная рамка вокруг лица. Это упрощает пометку определенных точек интереса на изображении. В следующем коде показано получение расположений носа и зрачков.
```CSharp
var faces = await faceServiceClient.DetectAsync(imageUrl, returnFaceLandmarks:true);
 
foreach (var face in faces)
{
    var rect = face.FaceRectangle;
    var landmarks = face.FaceLandmarks;
 
    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;
 
    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;
 
    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
``` 

Помимо пометки черт лица на изображении, ориентиры лица могут также использоваться для точного вычисления направления лица. Например, можно определить направление лица как вектор, направленный от центра рта к центру глаз. Это подробно описано в приведенном ниже коде.

```CSharp
var landmarks = face.FaceLandmarks;
 
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;
 
var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);
 
var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;
 
var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);
 
Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
``` 

Зная направление лица, можно повернуть прямоугольную рамку вокруг лица, чтобы совместить ее с лицом. Очевидно, что ориентиры лица могут обеспечить дополнительные сведения и возможности.

## <a name="step4"></a> Шаг 4. Использование других атрибутов лица

Помимо ориентиров лица API Face - Detect может также анализировать несколько других атрибутов на лице. Они перечислены ниже:

- Age
- пол;
- интенсивность улыбки;
- растительность на лице;
- положение головы в трехмерном пространстве.

Эти атрибуты прогнозируются с использованием статистических алгоритмов, которые не всегда могут быть точными на 100%. Тем не менее они удобны для классификации лиц по этим атрибутам. Дополнительные сведения о каждом из атрибутов см. в [глоссарии](../Glossary.md).

Ниже приведен простой пример извлечения атрибутов лиц во время обнаружения лиц.
```CSharp
var requiredFaceAttributes = new FaceAttributeType[] {
                FaceAttributeType.Age,
                FaceAttributeType.Gender,
                FaceAttributeType.Smile,
                FaceAttributeType.FacialHair,
                FaceAttributeType.HeadPose,
                FaceAttributeType.Glasses
            };
var faces = await faceServiceClient.DetectAsync(imageUrl,
    returnFaceLandmarks: true,
    returnFaceAttributes: requiredFaceAttributes);

foreach (var face in faces)
{
    var id = face.FaceId;
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
}
``` 
## <a name="summary"></a> Сводная информация

В этом руководстве вы изучили функциональные возможности API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) и узнали, как с его помощью определять лица на изображениях (переданных локально или из Интернета по URL-адресам), как он может определять лица, возвращая прямоугольные рамки вокруг лиц, и как с его помощью анализировать ориентиры лица, положение головы в трехмерном пространстве и другие атрибуты лица.

Дополнительные сведения об этом API вы можете найти в справочном руководстве по API [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="related"></a> Связанные разделы

[Практическое руководство по идентификации лиц на изображении](HowtoIdentifyFacesinImage.md)
