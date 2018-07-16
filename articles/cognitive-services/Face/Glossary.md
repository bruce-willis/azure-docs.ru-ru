---
title: Глоссарий для службы API распознавания лиц | Документация Майкрософт
titleSuffix: Microsoft Cognitive Services
description: В глоссарии объясняются термины, с которыми вы можете столкнуться при работе со службой API распознавания лиц.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ec3068c013be9f2fa4ff34b1c24596e46e7c5d05
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35380376"
---
# <a name="glossary"></a>Глоссарий

## <a name="a"></a>A

#### <a name="Attributes"></a>Атрибуты

Атрибуты не являются обязательными в результатах [обнаружения](#Detection-Face-Detection). Примеры атрибутов: [возраст](#Age-Attribute), [пол](#Gender-Attribute), [положение головы](#Head-Pose-Attribute), [растительность на лице](#Facial-Hair-Attribute), [улыбка](#Smile-Attribute).
Их можно получить из API [обнаружения](#Detection-Face-Detection), указав параметры запроса: returnFaceAttributes. Атрибуты предоставляют дополнительную информацию о выбранных [лицах](#Face) помимо [идентификатора лица](#Face-ID) и [прямоугольной рамки](#Face-Rectangle).

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Age-Attribute"></a>Возраст (атрибут)

Возраст — один из [атрибутов](#Attributes), описывающий возраст конкретного лица. Атрибут возраста является необязательным в результатах [обнаружения](#Detection-Face-Detection). Его можно настроить в запросе на [обнаружение](#Detection-Face-Detection), указав параметр returnFaceAttributes.

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="b"></a>b

## <a name="c"></a>C

#### <a name="Candidate"></a>Кандидат

Кандидаты — это результаты [идентификации](#Identification) (например, идентифицированные люди и уровень достоверности их определения). Кандидат представлен значением [PersonID](#Person-ID) и [достоверностью](#Confidence), указывающей, что человек идентифицирован с высоким уровнем достоверности.

Дополнительные сведения см. в руководстве по [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="Confidence"></a>Достоверность

Достоверность — это мера сходства между [лицами](#Face) или [человеком](#Person) в числовом выражении, которое используется при [идентификации](#Identification) и [проверке](#Verification). Достоверность указывает схожесть результатов после поиска, идентификации и проверки.

Дополнительные сведения см. в следующих руководствах: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="Detection-Face-Detection"></a>Обнаружение, обнаружение лиц

Обнаружение лиц — это процесс обнаружения лиц на изображениях. Пользователи могут передать изображение и указать его URL-адрес в запросе. Обнаруженные лица возвращаются с [идентификаторами лиц](#Face-ID), обеспечивающими уникальную идентификацию в API распознавания лиц. Прямоугольные рамки указывают расположение лиц на изображении в пикселях, а также необязательные [атрибуты](#Attributes) для каждого лица, такие как [возраст](#Age-Attribute), [пол](#Gender-Attribute), [положение головы](#Head-Pose-Attribute), [растительность на лице](#Facial-Hair-Attribute) и [улыбка](#Smile-Attribute).

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

## <a name="f"></a>F

#### <a name="Face"></a>Элемент Face

Элемент Face — это общее название результатов, полученных от API распознавания лиц и связанных с обнаруженными лицами. Элемент Face представляют унифицированный идентификатор ([идентификатор лица](#Face-ID)), заданная область на изображениях ([прямоугольная рамка вокруг лица](#Face-Rectangle)) и дополнительные [атрибуты](#Face-Attributes-Facial-Attributes) лица, например [возраст](#Age-Attribute), [пол](#Gender-Attribute), [ориентиры](#Face-Landmarks-Facial-Landmarks) и [положение головы](#Head-Pose-Attribute). Кроме того, лица могут быть возвращены в результате [обнаружения](#Detection-Face-Detection).

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-API"></a>API распознавания лиц

API распознавания лиц — это облачный API, который предоставляет наиболее эффективные алгоритмы определения и распознавания лиц. Основные функции API распознавания лиц можно разделить на две категории: [обнаружение](#Detection-Face-Detection) лиц с [атрибутами](#Face-Attributes-Facial-Attributes) и [распознавание](#Recognition) лиц.

Дополнительные сведения доступны в [обзоре API распознавания лиц](./Overview.md) и в руководствах по следующим API: [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="Face-Attributes-Facial-Attributes"></a>Атрибуты лица

Ознакомьтесь с [атрибутами](#Attributes).

#### <a name="Face-ID"></a>Идентификатор лица

Идентификатор лица извлекается из результатов [обнаружения](#Detection-Face-Detection), в которых строка представляет [лицо](#Face) в [API распознавания лиц](#Face-API).

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Face-Landmarks-Facial-Landmarks"></a>Ориентиры лица, лицевые ориентиры

Ориентиры не обязательно входят в результаты [обнаружения](#Detection-Face-Detection). Это семантические точки лица, например глаза, нос и рот (как показано на следующем рисунке). Ориентирами можно управлять, указывая в запросе на [обнаружение](#Detection-Face-Detection) логическое значение returnFaceLandmarks. Если returnFaceLandmarks имеет значение true, то возвращаемые лица будут содержать атрибуты ориентиров.

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="Face-Rectangle"></a>Прямоугольная рамка вокруг лица

Прямоугольная рамка вокруг лица извлекается из результатов [обнаружения](#Detection-Face-Detection). Это вертикальный прямоугольник (с указанием левой и верхней границ, ширины и высоты) на изображениях, указанный в пикселях. Левый верхний угол [лица](#Face) (слева, вверху), помимо ширины и высоты, указывает размер лица в координатах X и Y соответственно.

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Facial-Hair-Attribute"></a>Растительность на лице (атрибут)

Растительность на лице является одним из [атрибутов](#Attributes), используемых для описания длины растительности на лице. Атрибут растительности на лице является необязательным в результатах [обнаружения](#Detection-Face-Detection). Его можно настроить в запросе на [обнаружение](#Detection-Face-Detection), указав параметр returnFaceAttributes. Если returnFaceAttributes содержит facialHair, возвращенные лица будут содержать атрибуты растительности на лице.

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="FaceList"></a>FaceList

FaceList — это коллекция [PersistedFace](#PersistedFace). FaceList представляет собой единицу в операции [поиска похожих лиц](#Find-Similar). FaceList имеет [идентификатор FaceList](#FaceList-ID) и другие атрибуты, такие как [имя](#Name) и [данные пользователя](#UserData-User-Data).

Дополнительные сведения см. в следующих руководствах: [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="FaceList-ID"></a>Идентификатор FaceList

Идентификатор FaceList — это предоставляемая пользователем строка, используемая в качестве идентификатора [FaceList](#FaceList). Идентификатор FaceList должен быть уникальным в пределах подписки.

Дополнительные сведения см. в следующих руководствах: [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="Find-Similar"></a>Поиск похожих лиц

API Find Similar используется для поиска и запроса похожих лиц в коллекции лиц. Запрашиваемые лица и коллекции лиц представлены в запросе [идентификаторами лиц](#Face-ID) или [идентификатором FaceList](#FaceList-ID)/[LargeFaceList](#LargeFaceList-ID). В возвращенных результатах указываются найденные похожие лица, представленные с помощью [идентификаторов лиц](#Face-ID) или [идентификаторов PersistedFace](#PersistedFace-ID).

Дополнительные сведения см. в следующих руководствах: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G.

#### <a name="Gender-Attribute"></a>Пол (атрибут)

Пол является одним из [атрибутов](#Attributes), используемых для описания половой принадлежности лиц. Атрибут пола является необязательным в результатах [обнаружения](#Detection-Face-Detection). Его можно настроить в запросе на [обнаружение](#Detection-Face-Detection), указав параметр returnFaceAttributes. Если returnFaceAttributes содержит gender, возвращенные лица будут содержать атрибуты пола.

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="Grouping"></a>Группирование

Группирование лиц — это объединение коллекции лиц согласно сходству. Коллекции лиц в запросе задаются с помощью коллекций идентификаторов лиц. В результате группирования похожие лица объединяются в [группы](#Groups), а лица, которые не похожи ни на какое другое лицо, объединяются в разрозненную группу. В результате группирования создается не более одной [разрозненной группы](#Messy-Group).

Дополнительные сведения см. в руководстве по [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="Groups"></a>Группы

Группы получаются в результате [группирования](#Grouping). Каждая группа содержит коллекцию похожих лиц, указанных с помощью [идентификаторов лиц](#Face-ID).

Дополнительные сведения см. в руководстве по [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="Head-Pose-Attribute"></a>Положение головы (атрибут)

Положение головы является одним из [атрибутов](#Attributes), представляющим положение лица в трехмерном пространстве в соответствии с углами вращения, наклона и скоса, как показано на следующем рисунке. Диапазоны значений углов вращения и скоса составляют от –180 до 180 и от –90 до 90 градусов. В текущей версии значение наклона, возвращаемое после обнаружения, всегда равно 0. Атрибут положения головы является необязательным в результатах [обнаружения](#Detection-Face-Detection). Его можно настроить в запросе на [обнаружение](#Detection-Face-Detection), указав параметр returnFaceAttributes. Если параметр returnFaceAttributes содержит headPose, возвращенные лица будут содержать атрибуты положения головы.

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="Identification"></a>Операция Identification

Операция Identification означает определение одного или нескольких лиц в LargePersonGroup или PersonGroup.
[PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) — это коллекция элементов [Person](#Person).
Элементы Face, LargePersonGroup и PersonGroup представлены в запросе [идентификаторами лиц](#Face-ID) и [идентификаторами LargePersonGroup](#LargePersonGroup-ID)/[PersonGroup](#PersonGroup-ID) соответственно.
Идентифицированные результаты являются [кандидатами](#Candidate), представленными элементами [Person](#Person) с определенной степенью достоверности.
Несколько лиц во входных данных обрабатываются отдельно, и для каждого из них будет получен отдельный результат.

> [!NOTE]
> Перед идентификацией необходимо успешно выполнить обучение LargePersonGroup и PersonGroup. Если обучение LargePersonGroup и PersonGroup не выполнено или находится в [состоянии](#Status-Train), отличном от успешного (т. е. выполняется, произошла ошибка или истекло время ожидания), то ответом на запросу будет код 400.
> 

Дополнительные сведения см. в следующих руководствах: [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="Is-Identical"></a>IsIdentical

IsIdentical — это логическое поле в результатах [проверки](#Verification), указывающее, принадлежат ли два лица одному человеку.

Дополнительные сведения см. в руководстве по [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="j"></a>J

## <a name="k"></a>K

## <a name="l"></a>L

#### <a name="landmarks"></a>Ориентиры

Ознакомьтесь с [ориентирами лица](#Face-Landmarks-Facial-Landmarks).

#### <a name="LargeFaceList"></a>LargeFaceList

LargeFaceList — это коллекция [PersistedFace](#PersistedFace). LargeFaceList представляет собой единицу в операции [поиска похожих лиц](#Find-Similar). LargeFaceList имеет [идентификатор LargeFaceList](#LargeFaceList-ID) и другие атрибуты, такие как [имя](#Name) и [данные пользователя](#UserData-User-Data).

Дополнительные сведения см. в следующих руководствах: [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - List Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="LargeFaceList-ID"></a>Идентификатор LargeFaceList

Идентификатор LargeFaceList — это предоставляемая пользователем строка, используемая в качестве идентификатора [LargeFaceList](#LargeFaceList). Идентификатор LargeFaceList должен быть уникальным в пределах подписки.

Дополнительные сведения см. в следующих руководствах: [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="LargePersonGroup"></a>LargePersonGroup

LargePersonGroup — это коллекция элементов [Person](#Person). LargePersonGroup представляет собой единицу в операции [идентификации](#Identification). LargePersonGroup имеет [идентификатор LargePersonGroup](#LargePersonGroup-ID) и другие атрибуты, такие как [имя](#Name) и [данные пользователя](#UserData-User-Data).

Дополнительные сведения см. в следующих руководствах: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="LargePersonGroup-ID"></a>Идентификатор LargePersonGroup

Идентификатор LargePersonGroup — это предоставляемая пользователем строка, используемая в качестве идентификатора [LargePersonGroup](#LargePersonGroup). Идентификатор LargePersonGroup должен быть уникальным в пределах подписки.

Дополнительные сведения см. в следующих руководствах: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="Messy-Group"></a>Разрозненная группа

Разрозненная группа создается на основе результатов [группирования](#Grouping). Она содержит лица, не похожие ни на одно другое лицо. Каждое лицо в разрозненной группе обозначается [идентификатором лица](#Face-ID).

Дополнительные сведения см. в руководстве по [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>Нет

#### <a name="name-person"></a>Имя (элемент Person)

Имя — это понятная строка, описывающая элемент [Person](#Person). В отличие от [идентификатора Person](#Person-ID), имена людей в пределах группы могут дублироваться.

Дополнительные сведения см. в следующих руководствах: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Name"></a>Имя (LargePersonGroup или PersonGroup)

Имя также является понятной строкой, описывающей [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). В отличие от [идентификатора LargePersonGroup](#LargePersonGroup-ID)/[PersonGroup](#PersonGroup-ID), имена LargePersonGroup и PersonGroup могут дублироваться в пределах подписки.

Дополнительные сведения см. в следующих руководствах: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="o"></a>O

## <a name="p"></a>P

#### <a name="PersistedFace"></a>PersistedFace

PersistedFace — это структура данных в API распознавания лиц. PersistedFace имеет [идентификатор PersistedFace](#PersistedFace-ID) и другие атрибуты, такие как [имя](#Name) и [данные пользователя](#UserData-User-Data).

Дополнительные сведения см. в следующих руководствах: [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person-ID"></a>Идентификатор Person

Идентификатор Person создается при успешном создании [PersistedFace](#PersistedFace). Создается строка для представления этого элемента Face в [API распознавания лиц](#Face-API).

Дополнительные сведения см. в следующих руководствах: [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="Person"></a>Элемент Person

Элемент Person — это структура данных, управляемая в API распознавания лиц. Элемент Person имеет [идентификатор Person](#Person-ID) и другие атрибуты, такие как [имя](#Name), коллекция [PersistedFace](#PersistedFace) и [данные пользователя](#UserData-User-Data).

Дополнительные сведения см. в следующих руководствах: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="Person-ID"></a>Идентификатор Person

Идентификатор Person создается при успешном создании [Person](#Person). Создается строка для представления этого элемента Person в [API распознавания лиц](#Face-API).

Дополнительные сведения см. в следующих руководствах: [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="PersonGroup"></a>PersonGroup

PersonGroup — это коллекция элементов [Person](#Person). PersonGroup представляет собой единицу в операции [идентификации](#Identification). PersonGroup имеет [идентификатор PersonGroup](#PersonGroup-ID) и другие атрибуты, такие как [имя](#Name) и [данные пользователя](#UserData-User-Data).

Дополнительные сведения см. в следующих руководствах: [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="PersonGroup-ID"></a>Идентификатор PersonGroup

Идентификатор PersonGroup — это предоставляемая пользователем строка, используемая в качестве идентификатора [PersonGroup](#PersonGroup). Идентификатор PersonGroup должен быть уникальным в пределах подписки.

Дополнительные сведения см. в следующих руководствах: [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Положение (атрибут)

Ознакомьтесь с [положением головы](#Head-Pose-Attribute).

## <a name="q"></a>Q

## <a name="r"></a>R

#### <a name="Recognition"></a>Распознавание

Распознавание представляет собой популярную область приложений, использующих технологии распознавания лиц, такие как [поиск похожих лиц](#Find-Similar), [группирование](#Grouping), [идентификация](#Identification),[проверка двух лиц на принадлежность одному человеку](#Verification).

Дополнительные сведения см. в руководствах по следующим API: [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Прямоугольник (Face)

Ознакомьтесь с [прямоугольной рамкой вокруг лица](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="Smile-Attribute"></a>Улыбка (атрибут)

Улыбка является одним из [атрибутов](#Attributes), используемых для описания улыбающихся выражений на лицах. Атрибут улыбки является необязательным в результатах [обнаружения](#Detection-Face-Detection). Его можно настроить в запросе на [обнаружение](#Detection-Face-Detection), указав параметр returnFaceAttributes. Если returnFaceAttributes содержит smile, возвращенные лица будут содержать атрибуты улыбки.

Дополнительные сведения см. в руководстве по [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="similar-face-searching"></a>Поиск похожих лиц

Ознакомьтесь с [поиском похожих лиц](#Find-Similar).

#### <a name="Status-Train"></a>Состояние (обучение)

Состояние является строкой, используемой для описания процедуры [обучения LargeFaceList, LargePersonGroup или PersonGroup](#Train). Возможные состояния: notstarted, running, succeeded, failed.

Дополнительные сведения см. в следующих руководствах: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Ключ подписки

Ключ подписки — это строка, которую необходимо указать в качестве параметра строки запроса для вызова любого API распознавания лиц. Ключ подписки можно найти на странице "My Subscriptions" (Мои подписки) после входа на портал Microsoft Cognitive Services. С каждой подпиской связаны два ключа: один первичный ключ и один вторичный ключ. Оба эти ключа можно использовать для вызова API. Необходимо обеспечить безопасность ключей подписки. Кроме того, в любой момент их можно создать повторно на странице "My Subscriptions" (Мои подписки).

## <a name="t"></a>T

#### <a name="Train"></a>Обучение (LargePersonGroup, LargeFaceList, PersonGroup)

API Train используется для предварительной обработки [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) для обеспечения производительности операций [поиска похожих лиц](#Find-Similar)/[идентификации](#Identification). Если обучение не выполнено или [состояние обучения](#Status-Train) не является успешным, то идентификация с помощью этого объекта PersonGroup приведет к ошибке.

Дополнительные сведения см. в следующих руководствах: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="UserData-User-Data"></a>Данные пользователя

Данные пользователя — это дополнительные сведения для элементов [Person](#Person) и [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Данные пользователя задают пользователи, чтобы упростить использование, понимание и запоминание информации.

Дополнительные сведения см. в следующих руководствах: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup Person - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup Person - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup Person - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="Verification"></a>Проверка

API Verification используется для проверки двух лиц на принадлежность одному человеку. Оба лица представлены в запросе как идентификаторы лиц. Результаты проверки содержат логическое поле ([isIdentical](#Is-Identical)), указывающее, принадлежат ли лица одному человеку (если да, то поле имеет значение true), и числовое поле ([достоверность](#Confidence)), указывающее уровень достоверности.

Дополнительные сведения см. в руководстве по [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="w"></a>W

## <a name="x"></a>X

## <a name="y"></a>Да

## <a name="z"></a>Z
