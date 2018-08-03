---
title: Использование действий с ожиданием и без ожидания в модели Conversation Learner в Microsoft Cognitive Services | Документация Майкрософт
titleSuffix: Azure
description: Сведения об использовании действий с ожиданием и без ожидания в модели Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a8f7ccf79e750c9f3c21c25c50c3e275db7e4195
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173090"
---
# <a name="wait-and-non-wait-actions"></a>Действия с ожиданием и без ожидания

В этом руководстве показано различие между действиями с ожиданием и действиями без ожидания в приложении Conversation Learner.

## <a name="video"></a>Видео

[![Предварительная версия руководства 2](http://aka.ms/cl-tutorial-02-preview)](http://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>Требования
Для работы с этим руководством требуется запущенный бот обучения общего назначения.

    npm run tutorial-general

## <a name="details"></a>Сведения

- Действие с ожиданием — в этом режиме система останавливает выполнение действий и ожидает ввода от пользователя.
- Действие без ожидания — в этом режиме система сразу же выбирает следующее действие (не дожидаясь ввода от пользователя).

## <a name="steps"></a>Действия

### <a name="create-a-new-model"></a>Создание модели

1. В пользовательском веб-интерфейсе щелкните "Новая модель".
2. В поле Name (Имя) введите WaitNonWait. Затем нажмите Create (Создать).

### <a name="create-the-first-wait-action"></a>Создание первого действия с ожиданием

1. Щелкните Actions (Действия), а затем New Action (Новое действие).
2. В поле ответа введите Which animal do you want? (Какое животное выбрать?).
    - Это действие с ожиданием, поэтому не снимайте флажок Wait for Response (Ожидать ответа).
3. Щелкните Создать.

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
    - Бот не ожидает ввода, а сразу выполняет следующее действие.
2. Щелкните Which animal do you want? (Какое животное выбрать?).
3. Введите duck (Утка).
5. Щелкните Score Actions (Оценить действия) и выберите Ducks say quack (Утки крякают).

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> Последовательность ответов бота при выполнении действий с ожиданием и без ожидания.

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Introduction to entities](./3-introduction-to-entities.md) (Основные сведения о сущностях)
