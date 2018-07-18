---
title: Редактирование базы знаний в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Редактирование базы знаний
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: eaa65bf3d257399fceadaa42f0d9ddbbf8afe234
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381820"
---
# <a name="edit-a-knowledge-base"></a>Редактирование базы знаний

QnA Maker позволяет управлять содержимым базы знаний, предоставляя возможности удобного редактирования.

## <a name="edit-your-knowledge-base-content"></a>Редактирование содержимого базы знаний

1.  Выберите **My knowledge bases** (Мои базы знаний) на панели навигации вверху. 

    Отобразятся все службы, созданные или предоставленные для совместного использования, в порядке убывания по дате **последнего изменения**.

    ![Раздел My Knowledge Bases (Мои базы знаний)](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Выберите определенную базу знаний, чтобы внести в нее изменения.

3. Внеся изменения, щелкните **Save and train** (Сохранить и обучить) в правом верхнем углу страницы, чтобы сохранить изменения.    

    ![Сохранение и обучение](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Если покинуть страницу до нажатия Save and train (Сохранить и обучить), изменения не будут сохранены.

## <a name="add-a-qna-pair"></a>Добавление пары QnA

Выберите **Add QnA pair** (Добавить пару QnA), чтобы добавить новую строку в таблицу базы знаний.

![Добавление пары QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Удаление пары QnA

Чтобы удалить пару QnA, щелкните значок **Удалить** справа в конце строки QnA.

![Удаление пары QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Добавление альтернативных вопросов

Добавьте альтернативные вопросы к существующей паре QnA, чтобы повысить вероятность совпадения с запросом пользователя.

![Добавление альтернативных вопросов](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Добавление метаданных


Добавьте пары метаданных, выбрав значок фильтра

![Добавление метаданных](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Не забывайте периодически сохранять и обучать базу знаний после внесения правок, чтобы сохранить изменения.

## <a name="manage-large-knowledge-bases"></a>Управление большими базами знаний

1. Вопросы и ответы (QnA) **группируются** по источникам данных, из которых они были извлечены. Источник данных можно развернуть или свернуть.
2. Вы можете выполнить **поиск** в базе знаний, введя текст в соответствующее поле в верхней части таблицы базы знаний. Щелкните Enter (Ввести) для поиска по вопросу, ответу или содержимому метаданных. Щелкните значок X, чтобы удалить фильтр поиска.
3. **Разбиение на страницы** позволяет управлять большими базами знаний

    ![Поиск, разбиение на страницы, группирование](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Collaborate on a knowledge base](./collaborate-knowledge-base.md) (Совместная работа над базой знаний)
