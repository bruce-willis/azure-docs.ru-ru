---
title: Управление данными пользователей в средстве для обучения ведению диалога (Microsoft Cognitive Services) | Документация Майкрософт
titleSuffix: Azure
description: Узнайте, как управлять данными пользователей в средстве для обучения ведению диалога.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f9de4377857188a8cf483321654fb857e428c7f5
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171645"
---
# <a name="managing-user-data"></a>Управление данными пользователей

На этой странице указано, какие данные регистрируются в облачной службе средства для обучения ведению диалога при ведении диалогов с пользователями.  Также из этой статьи вы узнаете, как связать журналы средства для обучения ведению диалога с идентификаторами пользователей, чтобы получить и (или) удалить все журналы, связанные с определенным пользователем.

## <a name="overview-of-end-user-data-logging"></a>Общие сведения о регистрации данных пользователей в журналах

По умолчанию в облачной службе средства для обучения ведению диалога регистрируются все данные взаимодействия между пользователями и ботами.  Журналы с этими данными важны для улучшения ботов, так как позволяют отследить случаи неправильного извлечения сущностей или выбора неправильных действий.  Такие ошибки можно исправить на странице Log Dialogs (Запись диалогов) пользовательского интерфейса. Здесь вносятся нужные исправления и исправленный диалог сохраняется в качестве нового диалога для обучения. Дополнительные сведения см. в руководстве по записи диалогов.

## <a name="how-to-disable-logging"></a>Отключение ведения журнала

Вы можете выбрать, будут ли доступны для модели Conversation Learner диалоги с пользователями на странице "Параметры".  Здесь есть флажок Log Conversations (Сохранять диалоги).  Если вы снимете этот флажок, диалоги с пользователями сохраняться не будут.

## <a name="what-is-logged"></a>Какие данные регистрируются в журнале 

Средство для обучения ведению диалога регистрирует в журналах все вводимые пользователем данные, значения сущностей, выбранные действия и метки времени для каждого шага.  Эти журналы хранятся в течение определенного времени, а затем удаляются (дополнительные сведения см. на странице справки по значениям и границам по умолчанию).  

Средство для обучения ведению диалога создает уникальный идентификатор для каждого сохраненного диалога.  Средство для обучения ведению диалога *не* сохраняет идентификатор пользователя в составе данных диалогов.  

## <a name="associating-logged-dialogs-with-a-user-id"></a>Сопоставление сохраненных диалогов с идентификатором пользователя

Часто требуется сопоставить сохраненные диалоги с идентификатором пользователя. Например, если вам нужно извлечь или удалить журналы диалогов, имеющих отношение к конкретному пользователю.  Так как средство для обучения ведению диалога не сохраняет идентификатор пользователя, эту связь необходимо обеспечить в коде приложения.  

Чтобы создать такое сопоставление, получите идентификатор сохраняемого диалога из `EntityDetectionCallback` и сохраните сведения о связи между идентификатором пользователя и идентификатором сохраненного диалога в хранилище, которое использует бот.  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific datastore, store an association
    // bewteen your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>Заголовки для вызовов HTTP

В каждый вызов HTTP следует добавлять следующий заголовок:

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

Здесь `<LUIS_AUTHORING_KEY>` содержит ключ разработки Интеллектуальной службы распознавания речи, используемый для доступа к приложениям средства для обучения ведению диалога.

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>Получение необработанных данных сохраненного диалога

Чтобы получить необработанные данные сохраненного диалога, выполните следующий вызов HTTP:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Здесь `<appId>` — это идентификатор GUID модели Conversation Learner, а `<logDialgoId>` — это идентификатор журнала для извлекаемого диалога.  

> [!NOTE]
> Разработчик может редактировать журналы диалогов и сохранять их в качестве диалогов для обучения.  В таких случаях средство для обучения ведению диалога сохраняет идентификатор "исходного" диалога вместе с созданным диалогом для обучения.  Позднее вы можете отделить диалог для обучения, используя пользовательский интерфейс. Если диалог для обучения сопоставлен с идентификатором исходного журнала диалога, все отделенные копии этого диалога для обучения отмечаются тем же идентификатором журнала диалога.

Чтобы получить все диалоги для обучения, производные от конкретного диалога в журнале, выполните указанные ниже действия.

Прежде всего получите все диалоги для обучения:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

Здесь `<appId>` — это идентификатор GUID модели Conversation Learner.  

Этот запрос возвращает все диалоги для обучения.  Найдите в этом списке связанное значение `sourceLogDialogId` и зафиксируйте значение `trainDialogId`. 

Чтобы получить один диалог для обучения по его идентификатору, сделайте следующее:

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Здесь `<appId>` — это идентификатор GUID модели Conversation Learner, а `<trainDialogId>` — это идентификатор извлекаемого диалога для обучения.  

## <a name="how-to-delete-a-logged-dialog"></a>Удаление сохраненного диалога из журнала

Если вы хотите удалить диалог из журнала по известному идентификатору, используйте такой вызов HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

Здесь `<appId>` — это идентификатор GUID модели Conversation Learner, а `<logDialogId>` — это идентификатор журнала для удаляемого диалога. 

Если вы хотите удалить диалог для обучения по известному идентификатору, используйте такой вызов HTTP:

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

Здесь `<appId>` — это идентификатор GUID модели Conversation Learner, а `<trainDialogId>` — это идентификатор удаляемого диалога для обучения. 
