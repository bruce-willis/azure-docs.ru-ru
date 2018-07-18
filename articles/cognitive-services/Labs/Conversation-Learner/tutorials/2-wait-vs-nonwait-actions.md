---
title: Использование действий с ожиданием и без ожидания в приложении Conversation Learner в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Узнайте, как использовать действия с ожиданием и без ожидания в приложении Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb2cbd55b6c8be51213095926bb592169613d1fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35381721"
---
# <a name="wait-and-non-wait-actions"></a>Действия с ожиданием и без ожидания

В этом руководстве показано различие между действиями с ожиданием и действиями без ожидания в приложении Conversation Learner.

## <a name="requirements"></a>Требования
Для работы с этим руководством необходим запущенный бот обучения общего назначения

    npm run tutorial-general

## <a name="details"></a>Сведения

- Действие с ожиданием — в этом режиме система останавливает выполнение действий и ожидает ввода от пользователя.
- Действие без ожидания — в этом режиме система сразу же выбирает следующее действие (не дожидаясь ввода от пользователя).

## <a name="steps"></a>Действия

### <a name="create-a-new-app"></a>Создание нового приложения

1. В пользовательском веб-интерфейсе щелкните New App (Новое приложение).
2. В поле Name (Имя) введите WaitNonWait. Затем нажмите Create (Создать).

### <a name="create-the-first-wait-action"></a>Создание первого действия с ожиданием

1. Щелкните Actions (Действия), а затем New Action (Новое действие).
2. В поле ответа введите Which animal do you want? (Какое животное выбрать?).
    - Это действие с ожиданием, поэтому не снимайте флажок Wait for Response (Ожидать ответа).
3. Нажмите кнопку Done (Готово).

### <a name="create-a-non-wait-action"></a>Создание действия без ожидания

1. Щелкните New Action (Новое действие).
2. В поле ответа введите Cows say moo (Коровы мычат).
3. Снимите флажок Wait for Response (Ожидать ответа).
4. Нажмите кнопку "Создать"

### <a name="create-a-second-non-wait-action"></a>Создание второго действия без ожидания

1. Щелкните New Action (Новое действие).
2. В поле ответа введите Ducks say quack (Утки крякают).
3. Снимите флажок Wait for Response (Ожидать ответа).
4. Нажмите кнопку "Создать"

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Обучение бота

1. Щелкните Train Dialogs (Обучение диалогам), а затем New Train Dialog (Обучение новому диалогу).
2. Введите hello (Привет).
3. Щелкните Score Actions (Оценить действия) и выберите Which animal do you want? (Какое животное выбрать?).
4. Введите cow (Корова).
5. Щелкните Score Actions (Оценить действия) и выберите Cows say moo (Коровы мычат).
    - Обратите внимание, что бот не ожидает ввода, а сразу выполняет следующее действие.
2. Щелкните Which animal do you want? (Какое животное выбрать?).
3. Введите duck (Утка).
5. Щелкните Score Actions (Оценить действия) и выберите Ducks say quack (Утки крякают).

![](../media/tutorial2_dialogs.PNG)

Обратите внимание на последовательность ответов бота при выполнении действий с ожиданием и без ожидания.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Introduction to entities](./3-introduction-to-entities.md) (Основные сведения о сущностях)
