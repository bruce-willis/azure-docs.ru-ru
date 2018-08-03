---
title: Создание приложения с помощью службы LUIS | Документы Майкрософт
description: Создание и настройка приложений на веб-странице Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: diberry
ms.openlocfilehash: 3adeecd4a4e2040a92689b7c92be9630c9a0d93b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225420"
---
# <a name="create-an-app"></a>Создание приложения
Приложение можно создать разными способами: 

* [начать](#create-new-app) с пустого приложения и создать намерения, фрагменты речи и сущности;
* [начать](#create-new-app) с пустого приложения и добавить [готовую предметную область](luis-how-to-use-prebuilt-domains.md);
* [импортировать приложение LUIS](#import-new-app) из JSON-файла, который уже содержит намерения, фрагменты речи и сущности.

## <a name="what-is-an-app"></a>Что представляет собой приложение
Приложение содержит [версию](luis-how-to-manage-versions.md) модели, а также любых [участников совместной работы](luis-how-to-collaborate.md) для приложения. При создании приложения вы выбираете язык и региональные параметры ([язык](luis-supported-languages.md)), которые **нельзя изменить позднее**. 

Версия нового приложения по умолчанию —0.1. 

Создавать приложения и управлять ими можно на странице **My Apps** (Мои приложения). Вы всегда можете открыть эту страницу, выбрав **Мои приложения** на верхней панели навигации веб-сайта [LUIS](luis-reference-regions.md). 

[![](media/luis-create-new-app/apps-list.png "Снимок экрана со списком приложений")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>Создание приложения

1. На странице **Мои приложения** выберите **Create new app** (Создать приложение).
2. В диалоговом окне введите имя приложения TravelAgent.

    ![Диалоговое окно создания приложения](./media/luis-create-new-app/create-app.png)

3. Выберите язык и региональные параметры приложения (для приложения TravelAgent выберите английский язык), а затем нажмите кнопку **Готово**. 

    >[!NOTE]
    >Язык и региональные параметры нельзя изменить после создания приложения. 

## <a name="import-new-app"></a>Импорт нового приложения
Для приложения в JSON-файле можно задать имя (макс. 50 символов), версию (макс. 10 символов) и описание. Примеры JSON-файлов приложений доступны в разделе [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight).

1. На странице **Мои приложения** выберите **Import new app** (Импорт нового приложения).
2. В диалоговом окне **Импорт нового приложения** выберите JSON-файл, определяющий приложение LUIS.

    ![Диалоговое окно импорта нового приложения](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>Экспорт приложения
1. На странице **Мои приложения** нажмите кнопку многоточия (***...***) в конце строки приложения.

    [![](media/luis-create-new-app/apps-list.png "Снимок экрана со всплывающим диалоговым окном действий на уровне приложения")](media/luis-create-new-app/three-dots.png#lightbox)

2. Выберите в меню пункт **Export app** (Экспорт приложения). 

## <a name="rename-app"></a>Переименование приложения

1. На странице **Мои приложения** нажмите кнопку многоточия (***...***) в конце строки приложения. 
2. Выберите в меню пункт **Переименовать**.
3. Введите новое имя приложения и нажмите кнопку **Готово**.

## <a name="delete-app"></a>Удаление приложения

> [!CAUTION]
> Удаление приложения выполняется для всех участников совместной работы и владельца. [Экспортируйте](#export-app) приложение, прежде чем его удалять. 

1. На странице **Мои приложения** нажмите кнопку многоточия (***...***) в конце строки приложения. 
2. В меню выберите пункт **Удалить**.
3. В диалоговом окне подтверждения выберите **ОК**.

## <a name="export-endpoint-logs"></a>Экспорт журналов конечных точек
Журналы содержат запросы, время в формате UTC и ответ JSON службы LUIS.

1. На странице **Мои приложения** нажмите кнопку многоточия (***...***) в конце строки приложения. 
2. Выберите в меню пункт **Export endpoint logs** (Экспорт журналов конечных точек).

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>Дополнительная информация

Первая задача в приложении — [добавление намерений](luis-how-to-add-intents.md).